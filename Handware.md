# 一.RoboMaster AI车底层

## USB通讯部分

### USB发送

初始化部分

```Cpp
void services_task(void const *argument) //all task init. This thread is called in main.c

void hw_init(void) {
    board_config();  //注册了usb的receive与send的回调函数
    system_config();
    easyflash_init();
}

void board_config(void){
    ......
    soft_timer_init();
    soft_timer_register(usb_tx_flush, NULL, 1); //usb_tx_flush()中利用
    usb_vcp_rx_callback_register(usb_rcv_callback);
    protocol_uart_interface_register("usb", 1024, 1, USB_COM, usb_interface_send); //注册名为"usb"的interface及其成员的发送函数
}
//注册一个interface，并声明他的发送回调函数
int32_t protocol_uart_interface_register(char *interface_name,
        uint16_t rcv_buf_size,
        uint8_t broadcast_output_enable,
        com_port_t com_port,
        uint32_t (*com_send_fn)(uint8_t *p_data, uint16_t len)){
    struct perph_interface interface = {0};
    uint32_t status;
    status = PROTOCOL_SUCCESS;
    interface.type = COM_PORT;
    interface.send_callback.com_send_fn = com_send_fn;
    interface.user_data.com.port = com_port;
    status = protocol_interface_init(&interface, interface_name, broadcast_output_enable, rcv_buf_size);
    return status;
}
```
创建一个task进行不断循环

```cpp
void communicate_task(void const *argument){
    protocol_rcv_cmd_register(CMD_REPORT_VERSION, report_firmware_version);
    while (1)
    {
        protocol_send_flush();
        protocol_unpack_flush();
        /* referee protocol unpack */
        referee_unpack_fifo_data();
        osDelay(1);
    }
}
```

数据发送内部迭代调用函数

```Cpp
uint32_t protocol_send_flush(void){
	......
    protocol_s_interface_normal_send_flush(protocol_local_info.interface + i);//遍历调用每个interface的normal_node与ack_node，发送send_list_node_t结构体内部的成员data
}

uint32_t protocol_s_interface_normal_send_flush(struct perph_interface *obj){
	list_for_each_prev_safe(cur_node, store_list, head_node) //得到cur_node
    ......
    protocol_s_interface_send_data(cur_send_node, obj);
}

uint32_t protocol_s_interface_send_data(send_list_node_t *cur_send_node, struct perph_interface *obj){
	......
    protocol_interface_send_data(obj, cur_send_node->p_data, cur_send_node->len);//根据perph_interface,利用send_list_node_t结构体的数据成员uint9_t *p_data与uint16_t len发送数据;
}

int32_t protocol_interface_send_data(struct perph_interface *perph, uint8_t *buff, uint16_t len){
   //按照perph_interface的类型（CAN或者USB）调用对应的发送函数回调函数成员interface.send_callback.com_send_fn()
}

uint32_t usb_interface_send(uint8_t *p_data, uint16_t len){ 
    CDC_Transmit_FS(p_data, len);//USB发送函数
    return 0;
}
```

填充发送数据

```cpp
soft_timer_register(chassis_info_push, p_chassis, 10);//对于底盘，创建软定时回调函数，
int32_t chassis_info_push(void *argc){
	//回调函数中填充protocol,添加发送节点
	protocol_send(MANIFOLD2_ADDRESS, CMD_PUSH_CHASSIS_INFO, &cmd_chassis_info, sizeof(cmd_chassis_info));
}
uint32_t protocol_send(uint8_t reciver, uint16_t cmd, void *p_data, uint32_t data_len){
    // add send node
    status = protocol_s_add_sendnode(reciver, session, PROTOCOL_PACK_NOR, p_data,
                                         data_len, cmd, 0);
}
uint32_t protocol_s_add_sendnode(uint8_t reciver, uint8_t session, uint8_t pack_type,
                                 void *p_data, uint32_t data_len, uint16_t cmd, uint16_t ack_seq){
	//填充send_list_node_t *send_node的成员
}
```

### USB接收

初始化

```cpp
void services_task(void const *argument) //all task init. This thread is called in main.c
其中的task_init中注册protocol的CMD回调函数,如下：
void app_protocol_init(void){
    // 将对应app（云台或者底盘）声明的接收CMD与对应的CMD接收回调函数
    protocol_rcv_cmd_register(current_app.recv_cmd_table[i].cmd,
    current_app.recv_cmd_table[i].rcv_callback);
}
void hw_init(void) {
    board_config();  //注册了usb的receive与send的回调函数
    system_config();
    easyflash_init();
}

void board_config(void){
    ......
    soft_timer_init();
    usb_vcp_rx_callback_register(usb_rcv_callback);//注册接收中断回调函数所调用的一部分
}

static int8_t CDC_Receive_FS(uint8_t *Buf, uint32_t *Len){
    //每次进入都会调用注册的USB接收回调函数
    if (usb_vcp_call_back != NULL)
        (*usb_vcp_call_back)(Buf, *Len);
    USBD_CDC_SetRxBuffer(&hUsbDeviceFS, &Buf[0]);
    USBD_CDC_ReceivePacket(&hUsbDeviceFS);
    return (USBD_OK);
}
```

中断接收回调迭代嵌套函数

```cpp
int32_t usb_rcv_callback(uint8_t *buf, uint32_t len){
    protocol_uart_rcv_data(USB_COM, buf, len);
    return len;
}

uint32_t protocol_uart_rcv_data(com_port_t com_port, void *p_data, uint32_t data_len){
    uint32_t status = PROTOCOL_SUCCESS;
    //遍历判断是哪个interface调用的接收回调函数
    ......
    protocol_rcv_data(p_data, data_len, &protocol_local_info.interface[i]);
}

uint32_t protocol_rcv_data(void *p_data, uint32_t data_len, struct perph_interface *perph){
    ......
    // 将数据放入对应的interface->rcvd.fifo
    rcv_length = fifo_s_puts_noprotect(&(obj->rcvd.fifo), p_data, data_len);
}
```

在communicate_task中不断循环解析储存在对应interface中的fifo

```cpp
task中：protocol_unpack_flush();

uint32_t protocol_unpack_flush(void){
    ......
    //解包
    protocol_s_extract(&(protocol_local_info.interface[i]));
}

#protocol_transmit.c中
uint32_t protocol_s_extract(struct perph_interface *obj){}//解包函数 不断extract不同的perph_interface->rcvd
```

