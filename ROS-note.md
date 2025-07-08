# code snip

固定字符串格式，提取数字，直接寻找索引

```cpp
std::string str = "ok X92.3600 Y-22.3836 Z29.6611";
double x, y, z;

size_t okPos = str.find('ok');
size_t xPos = str.find('X');
size_t yPos = str.find('Y');
size_t zPos = str.find('Z');
if (xPos != std::string::npos && yPos != std::string::npos && zPos != std::string::npos&& okPos != std::string::npos) {
    x = std::stod(str.substr(xPos + 1, yPos - xPos - 1));
    y = std::stod(str.substr(yPos + 1, zPos - yPos - 1));
    z = std::stod(str.substr(zPos + 1));}
```

```cpp
std::string Gcode = "";
Gcode = (std::string)"G0 X" + x + " Y" + y + " Z" + z + " F1000" + "\r\n"; //字符串拼接
ROS_INFO("set: %s", Gcode.c_str());//c格式

std::stringstream ss;
ss << msg_front << count;   //字符串流操作
```



# Navigating the ROS Filesystem**

## 一.Filesystem Tools

### 1.1 Using rospack

​    rospack help [subcommand]       get more specific help
​    rospack find [package]          Print absolute path to the package
​    rospack list  
​    ....****

### 1.2 Using roscd   (roscd is part of the rosbash suite)

​    roscd <package-or-stack>[/subdir]    change directory starting with package,stack,or location name 
​      //roscd without argument will take you to $ROS_WORKSPACE
​    roscd log                        take you to the folder where ROS stores log files
​    rospd                           --pushd equivalent of roscd
​    rosd                            lists directories in the directory-stack  

### 1.3 rosls [locationname[/subdir]]   list files of a ros package

​    rosed [package] [file]          							Edit a file within a package  
​    roscp package filename target   				  copy a file from a package
​    rosrun [--prefix cmd] [--debug] PACKAGE EXECUTABLE [ARGS] 

## 二.Creating a ROS Package

### 1.a catkin Package

package.xml     relevant boilerplate CMakeLists.txt      its own folder     

### 2.Packages in a <u>catkin</u> Workspace

...

### 3.Creating a catkin Package

**catkin_create_pkg** <package_name> [depend1] [depend2] [depend3]
     //http://wiki.ros.org/catkin/commands/catkin_create_pkg
 // create a new catkin package

### 4.Building a catkin workspace and sourcing the setup file

--build the packages in the catkin workspace

```
$ cd ~/catkin_ws 
$ catkin_make
```

--To add the workspace to your ROS environment you need to source the generated setup file
$ . ~/catkin_ws/devel/setup.bash

### 5.package dependencies

​    First-order dependencies or indirect dependencies
**rospack depends1[package]**       Print newline-separated,ordered list of immediate dependencies of the package.
//These dependencies for a package are stored in the package.xml file
**rospack depends [package]**       Print newline-separated, ordered list of all dependencies of the package

### 6.Customizing Your Package

#### 6.1Customizing the package.xml

The generated package.xml should be in your new package. 

##### 6.1.1 description tag

​      eg.   <description>The beginner_tutorials package</description>

##### 6.1.2 maintainer tags

​      eg.   <!-- One maintainer tag required, multiple allowed, one person per tag -->  
​	     <maintainer email="you@yourdomain.tld">Your Name</maintainer>

##### 6.1.3 license tags

​          eg.     <!-- One license tag required, multiple allowed, one license per tag -->
​                  <!-- Commonly used license strings: -->
​                  <!--   BSD, MIT, Boost Software License, GPLv2, GPLv3, LGPLv2.1, LGPLv3 -->
​	      <license>BSD</license>

##### 6.1.4 dependencies tags

​      The next set of tags describe the dependencies of your package. The dependencies are split into build_depend, buildtool_depend, exec_depend, test_depend.  

#### 6.2 Customizing the CMakeLists.txt

## 三.Building a ROS Package

​    $ source /opt/ros/%YOUR_ROS_DISTRO%/setup.bash             #if you don't sourceyour environment

### 1. Using catkin_make

 In a catkin workspace

$  **catkin_make** [make_targets] [-DCMAKE_VARIABLES=...]     Creates the catkin workspace layout and invokes cmake and make


​	//http://wiki.ros.org/catkin/commands/catkin_make

### 2. Building Your Package

​	//your own catkin package(catkin_create_pkg done) is in subderectory "src file" of your catkin workspace.

## 四.Understanding ROS Nodes

###   1.Graph Concepts

​    计算图（Computation Graph）是一个由ROS进程组成的点对点网络，它们能够共同处理数据。ROS的基本计算图概念有节点（Nodes）、主节点（Master）、参数服务器（Parameter Server）、消息（Messages）、服务（Services）、话题（Topics）和袋（Bags），它们都以不同的方式向图（Graph）提供数据。
​    Nodes:A node is an executable that uses ROS to communicate with other nodes.
​    Messages: ROS data type used when subscribing or publishing to a topic. 
​    Topics: Nodes can publish messages to a topic as well as subscribe to a topic to receive messages.
​    Master: Name service for ROS (i.e. helps nodes find each other).
​    rosout: ROS equivalent of stdout/stderr .	
​    roscore: Master + rosout + parameter server (parameter server will be introduced later) 	

###   2.Nodes

​    A node really is an executable file within a ROS package.ROS nodes use a ROS client library to communicate with other nodes. Nodes can publish or subscribe to a Topic. Nodes can also provide or use a Service. 

###   3.Client Libraries

​    ROS client libraries allow nodes written in different programming languages to communicate: 
​    -- roscpp(c++ client library) rospy(python client library)

###   4. $ roscore  ——   the first thing you should run when using ROS

//master(provides name service for ROS)+rosout (stdout/stderr)+parameter server(parameter server will be  introduced later) 

### 	rosnode  

——   rosnode is a command-line tool for printing information about ROS Nodes.
	**rosnode ping** [options] <node>  			test connectivity to node
	**rosnode list**	                   		list active nodes
	**rosnode info** [options] node1 [node2...]  	print information about node
	**rosnode machine** [machine-name]     		list nodes running on a particular machine or list machines

#### 	rosrun  

 ——    allows you to use the package name to directly run a node within a package (without having to know the package path). 
**rosrun** [package_name] [node_name]
//you can reassign Names from the command-line.  eg.rosrun turtlesim turtlesim_node __name:=my_turtle

## 五.Understanding ROS Topics

### 1.Setup

 

```shell
 $roscore      
 $rosrun turtlesim turtlesim_node  
 $rosrun turtlesim turtle_teleop_key
```

### 2.ROS Topics

The turtlesim_node and the turtle_teleop_key node are communicating with each other <u>over a ROS TOPIC.</u>

#### 2.1 Using rqt_graph

​    ——rqt_graph creates a dynamic graph of what's going on in the system

```
$ rosrun  rqt_graph  rqt_graph 
```



#### 2.2 Introducing rostopic

The `rostopic` tool allows you to get information about ROS **topics**. 

```
rostopic bw     		        display bandwidth used by topic
rostopic hz [topic]                     display publishing rate of topic    
rostopic list [/topic]   		print information about active topics
rostopic pub    		        publish data to topic
rostopic type   		        print topic type
rostopic echo [options] /topic          print messages to screen
```



#### 	2.3	use rostopic echo

​		`rostopic echo` shows the data published on a topic. 

​		 //eg.$rostopic echo    /turtle1/cmd_vel

#### 	2.4     use rostopic list 

​		`	rostopic list` returns a list of all topics currently subscribed to and published. 		

​		//eg.$ rostopic list -h

###   3.ROS Messages

####    3.1 Using **rostopic type**
​	**rosmsg**  ——a command-line tool for displaying information about ROS Message types
​	**rosmsg show [options]** <message type>	Show message description

###   4.rostopic continued

####     4.1 Using rostopic pub

​	**rostopic pub** [topic] [msg_type] [args]  rostopic pub publishes data on to a topic currently advertised. 
​	// eg. rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'

```shell
$ rostopic pub /turtle1/cmd_vel geometry_msgs/Twist - r  1   -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, -1.8]'
```

####     4.2 Using rostopic hz

```shell
 $ rostopic hz /turtle1/pose
```

###   5.Using rqt_plot

[]: http://wiki.ros.org/cn/ROS/Tutorials/UnderstandingTopics



## 六.Understanding ROS Services and Parameters

###   1.ROS Services

​    Services are another way that nodes can communicate with each other. Services allow nodes to send a request and receive a response. 

###   2.Using rosservice

```shell
rosservice list         输出活跃服务的信息
rosservice call         用给定的参数调用服务
rosservice type         输出服务的类型
rosservice find         按服务的类型查找服务
rosservice uri          输出服务的ROSRPC uri
```

​    2.1 **rosservice list** ——        		print information about active services
​    2.2 **rosservice type** /service		print service type   


​     ` $ rosservice type /spawn | rossrv show`


​    2.3 **rosservice call** [service] [args] 	call the service with the provided args

​     `$ rosservice call /spawn 2 2 0.2 ""`

​    2.4 **rosservice find**         		find services by service type
​	**rosservice uri**          print service ROSRPC uri

###   3.rosparam set and rosparam get

​    rosparam allows you to store and manipulate data on the ROS Parameter Server.
​    **rosparam set** [param_name]               set parameter
​    **rosparam get** [param_name]           	get parameter
​    **rosparam load**           			                 load parameters from file
​    **rosparam dump**          						   dump parameters to file
​    **rosparam delete**         			               delete parameter
​    **rosparam list**           			                    list parameter names 
​    3.1 **rosparam list**
​    3.2 **rosparam set** and **rosparam get**
 	//eg. $ rosparam set /turtlesim/background_r 150      $ rosparam get /
​	      $ rosservice call /clear
​    3.3 rosparam dump and rosparam load
​	**rosparam dump** [file_name] [namespace] 

```
$  rosparam  dump  params.yaml  //Here we write all the parameters to the file params.yaml 
```

​	**rosparam load** [file_name] [namespace] 

## 七.Using rqt_console and roslaunch

###   1.Prerequisites rqt and turtlesim package

​    $ sudo apt-get install ros-<distro>-rqt ros-<distro>-rqt-common-plugins ros-<distro>-turtlesim

###   2.Using rqt_console and rqt_logger_level

​    **rqt_console**:attaches to ROS's logging framework to display output from nodes.
​    **rqt_logger_level**:allows us to change the verbosity level(DEBUG,WARN,INFO,and ERROR) of nodes as they run.	Before we start the turtlesim, **in two new terminals** start......

```
$ rosrun rqt_console rqt_console
```

```
$ rosrun rqt_logger_level rqt_logger_level
```

####   2.1 Quick Note about logger levels

Logging levels are prioritized in the following order: 

```
Fatal--Error--Warn--Info--Debug
```

####   2.2 Using roslaunch

`roslaunch` starts nodes as defined in a launch file. 

  $ **roslaunch**   [package]    [filename.launch]

```
$ roscd beginner_tutorials
```

  If `roscd` says something similar to *roscd: No such package/stack 'beginner_tutorials'* , you will        need to `source` the environment setup file like you did at the end of the [create_a_workspace](http://wiki.ros.org/catkin/Tutorials/create_a_workspace) tutorial: 

```
$ cd ~/catkin_ws
$ source devel/setup.bash
$ roscd beginner_tutorials
```

 let's make a launch directory: 

```
$ mkdir launch
$ cd launch
```

#### 2.3  The Launch File

create a launch file called turtlemimic.launch:

```xml
<launch>    //the launch tag

  <group ns="turtlesim1">
    <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
  </group>

  <group ns="turtlesim2">
    <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
  </group>

  <node pkg="turtlesim" name="mimic" type="mimic">
    <remap from="input" to="turtlesim1/turtle1"/>
    <remap from="output" to="turtlesim2/turtle1"/>
  </node>

</launch>
```

[]: http://wiki.ros.org/cn/ROS/Tutorials/UsingRqtconsoleRoslaunch



#### 2.5  roslaunching

Now let's `roslaunch` the launch file: 

```livescript
$ roslaunch    beginner_tutorials      turtlemimic.launch
```

then we can send the `rostopic` command: 

```
$ rostopic pub /turtlesim1/turtle1/cmd_vel geometry_msgs/Twist -r 1 -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, -1.8]'
```

At the same times,we can use **rqt_graph** to understand.

## 八.Using rosed to edit files in ROS

### 1.Using rosed

 It allows you to directly edit a file within a package by using the package name 

```
$ rosed  [package_name]  [filename]
```

### 2.Tab

### 3.Editor

The more beginner-friendly editor `nano` is included with the default Ubuntu install. You can use it by editing your ~/.bashrc file to include: 

```
export EDITOR='nano -w'
```

```
export EDITOR='emacs -nw'
```

Open a new terminal and see if `EDITOR` is defined: 

```
$ echo $EDITOR
```

```
nano -w
```

or 

```
emacs -nw
```

## 九.Creating a ROS msg and srv

### 1.Introduction to msg and srv

[msg](http://wiki.ros.org/msg): msg  files are simple text files that *describe the fields of a ROS message*.  They are used to generate source code for messages in different  languages. 

Here is an example of a msg that uses a Header, a string primitive, and two other msgs :

```
  Header header
  string child_frame_id
  geometry_msgs/PoseWithCovariance pose
  geometry_msgs/TwistWithCovariance twist
```

[srv](http://wiki.ros.org/srv): an srv file describes a service. It is composed of two parts: a request and a response. The two parts are separated by a '---' line. Here is an example of a srv file:

```shell
int64 A
int64 B
---
int64 Sum
```

msg files are stored in the `msg` directory of a package, and srv files are stored in the `srv` directory.

### 2.Buliding messages,services

We cancreate a more complex msg file by adding multiple elements, **one per line** in the `./msg/xxx.msg`.

Then open ***package.xml***,and make sure these two lines are in it and [uncommented](http://www.htmlhelp.com/reference/wilbur/misc/comment.html):

```xml
  <build_depend>message_generation</build_depend>
 <build_export_depend>message_runtime</build_export_depend>
  <exec_depend>message_runtime</exec_depend>
```

Your messages services, or actions will probably include fields defined in other ROS messages, like [std_msgs](http://wiki.ros.org/std_msgs). Declare them like this:

```xml
<depend>std_msgs</depend>
```

For  ***CMakeLists.txt***, find the catkin packages for `message_generation` and any messages, services or actions you depend on.

Add the `message_generation` dependency to the `find_package` call which already exists in your ***CMakeLists.txt*** .

```cmake
find_package(catkin REQUIRED COMPONENTS
            #actionlib_msgs                           #for buliding actions
  			roscpp
 		    rospy
  			std_msgs
  			message_generation)                 #this is most important
```

Next, list your message definitions:

```cmake
 add_message_files(DIRECTORY msg
     FILES
     YourMessage1.msg)
# Uncomment it by removing the `#` symbols and then replace the stand in `Message*.msg` files with your `.msg` file.
```

Similarly, if you have a service to generate:

```cmake
add_service_files( DIRECTORY srv
                  FILES
                  YourService.srv)
```

We make sure that CMake knows when it has to reconfigure the project after you add other .msg files so we must ensure the `generate_messages()` function is called.

```cmake
generate_messages(
  DEPENDENCIES
  std_msgs
)
```

Make sure the `catkin_package()` command declares your message, service and action dependencies for other packages:

```cmake
catkin_package(CATKIN_DEPENDS message_runtime std_msgs)
```

```shell
   rosmsg show        Show message description
   rosmsg list             List all messages
   rosmsg md5          Display message md5sum
   rosmsg package  List messages in a package
   rosmsg packages List packages that contain messages
  
  	rossrv show      	Show service description
	rossrv info         	Alias for rossrv show
	rossrv list	              List all services
	rossrv md5	         Display service md5sum
	rossrv package	  List services in a package
	rossrv packages  List packages that contain services

```

### 

## 十. Writing a Simple Publisher and Subscriber (C++)

### 1. Writing the Publisher Node

Create a `package_name/src` directory in the beginner_tutorials package directory；This directory will contain any source files for your package.

### 2.Writing the Subscriber Node

This is also in the package `src` directory.

```cpp
int main(int argc, char *argv[])
{
	ros::init(argc,argv,"swiftpro_ctl_node");    //执行 ros 节点初始化
    ros::NodeHandle n;    //创建 ros 节点句柄
    //ros::Publisher pub = n.advertise<std_msgs::String>("position_write_topic",2);
    ros::Subscriber sub = n.subscribe("SwiftproState_topic",10,positionFD_callback);//订阅机械臂周期反馈位置信息

    std_msgs::String msg;
    std::string msg_front = "Hello 你好！"; //消息前缀

    ros::Rate loop_rate(10);//循环频率

    ros::Time last_time = ros::Time::now();
    while (ros::ok())
    {
        //使用 stringstream 拼接字符串与编号
//        std::stringstream ss;
//        ss << msg_front << count;
//        msg.data = ss.str();

        //发布消息
        //pub.publish(msg);
        swiftpro::position posSet,posNow;
//        posSet.x = 50;
//        posSet.y = 150;
//        posSet.z = 150;
//        position_control(posSet);
        //ros::Time current_time = ros::Time::now();
        //ROS_INFO("%f: x:%.2f y:%.2f z:%.2f", current_time.toSec(), positionFD.x, positionFD.y, positionFD.z);
        if (ros::Time::now() - last_time > ros::Duration(5.0))
            pump_control(false);
        else
            pump_control(true);
        
        ros::spinOnce();
        loop_rate.sleep();
}
```



### 3. Building your nodes

First,you should complete the relevant configuration in  `CMakeLists.txt` and `package.xml` for your message and services. 

Then,in the  bottom of`CMakeLists.txt`,add 

```cmake
add_executable(talker src/talker.cpp)
target_link_libraries(talker ${catkin_LIBRARIES})
add_dependencies(talker beginner_tutorials_generate_messages_cpp)

add_executable(listener src/listener.cpp)
target_link_libraries(listener ${catkin_LIBRARIES})
add_dependencies(listener beginner_tutorials_generate_messages_cpp)
```

This will create two executables, `talker` and `listener`, which by default will go into package directory of your [devel space](http://wiki.ros.org/catkin/workspaces#Development_.28Devel.29_Space), located by default at `~/catkin_ws/devel/lib/<package name>`.



## 十一.Examining the Simple Publisher and Subscriber

`$ roscore`

**catkin specific** If you are using catkin, make sure you have sourced your workspace's setup.sh file after calling `catkin_make` but before trying to use your applications:

```shell
# In your catkin workspace
$ cd ~/catkin_ws
$ source ./devel/setup.bash
```

___

```shell
$ rosrun beginner_tutorials    talker    
$ rosrun beginner_tutorials    listener 
```



## 十二.Writing a Simple Service and Client (C++)

### 1.Writing a Service Node

Make sure you have created the service needed in this tutorial, [creating the AddTwoInts.srv](http://wiki.ros.org/ROS/Tutorials/CreatingMsgAndSrv#Creating_a_srv) 



### 2.Writing the Client Node

http://wiki.ros.org/cn/ROS/Tutorials/WritingServiceClient%28c%2B%2B%29

### 3.Building your nodes

Again edit the beginner_tutorials `CMakeLists.txt`and add the following at the end:

```cmake
add_executable(add_two_ints_server src/add_two_ints_server.cpp)
target_link_libraries(add_two_ints_server ${catkin_LIBRARIES})
add_dependencies(add_two_ints_server beginner_tutorials_gencpp)

add_executable(add_two_ints_client src/add_two_ints_client.cpp)
target_link_libraries(add_two_ints_client ${catkin_LIBRARIES})
add_dependencies(add_two_ints_client beginner_tutorials_gencpp)
```

### 4. Running the nodes

```shell
$rosrun beginner_tutorials add_two_ints_server
```

```shell
$ rosrun beginner_tutorials add_two_ints_client 1 3
```



# Intermediate Level

## Creating a ROS package

`catkin_create_pkg package_name std_msgs rospy roscpp`

the `package.xml` file allows tools like [rospack](http://wiki.ros.org/rospack) to determine information about what your package depends upon.Now that your package has a manifest, ROS can find it(`rospack find`)

Now we need the `CMakeLists.txt`file so that [catkin_make](http://wiki.ros.org/catkin_make), which uses CMake for its more powerful flexibility when building across multiple platforms, builds the package.

## 新建软件包的一般步骤

- 在工作空间的`/src`目录下新建一个工作包，并加入所需要的依赖（也可是别的包生成的消息文件）

```shell
$ catkin_create_pkg package_name std_msgs rospy roscpp geomtry_message[...]
```

- 软件包路径下新建一个`msg`文件夹，创建自己的消息文件，如Num.msg:

```shell
string first_name
string last_name
```

确保package.xml中含有(实际上会自动添加)

```shell
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
```

在CMakeLists.txt中，在`find_package()`调用添加`message_generation`依赖项，修改`add_message_files()`并确保`generate_messages()`函数被调用，srv[参考](https://wiki.ros.org/cn/ROS/Tutorials/CreatingMsgAndSrv)：

```shell
find_package(catkin REQUIRED COMPONENTS
   ...
   message_generation #生成消息文件
   )
...
add_message_files(
  FILES
  Num.msg)   #加入自定义的消息文件
...
generate_messages(
  DEPENDENCIES  #调用消息
  std_msgs)  
...
catkin_package( ...  # 确保导出消息的运行依赖关系
  CATKIN_DEPENDS message_runtime ...
...)
```

- 软件包路径下新建的节点文件`node1Main.cpp`，在自动生成的CMakeLists文件中添加如下语句为节点生产可执行文件。

```bash
add_executable(nodename node1Main.cpp)
target_link_libraries(nodename ${catkin_LIBRARIES})
add_dependencies(nodename package_name_generate_messages_cpp)
```

## 封装launch文件

启动文件（Launch File）是 ROS 中一种同时启动多个节点的途径，还可以
自动启动 ROS Master 节点管理器，并且可以实现每个节点的各种配置，为多个
节点的操作提供便利。launch 文件采用 XML 的形式进行描述。XML 文件必须
包含一个根元素，launch 文件中的根元素采用<launch>标签定义，文件中的其他
内容都必须包含在这个标签中：

```
<launch>
……
</launch>
```

下面是一个简单的 launch 文件，包含一个根元素<launch>和两个节点元素<node>

```
<launch>
<node pkg=”package-name” type=”executable-name” name=”node-name” / >
<node pkg=”package-name” type=”executable-name” name=”node-name” / >
</launch>
```

在复杂的系统当中，launch文件往往有很多，这些launch文件之间也会存在依赖关系。如果需要直接复用一个已有launch文件中的内容，可以使用`<include>`标签包含其他launch文件，这和C语言中的include几乎是一样的。

```xml
<include file="$(find demo)/launch/demo.launch" ns="demo_namespace"/>
```

参数设置如下：

- <node>
  启动文件的核心是启动 ROS 节点，采用<node>标签定义，语法如下：

```xml
<node pkg=”package-name” type=”executable-name” name=”node-name” />
```

从上面的定义规则可以看出，在启动文件中启动一个节点需要三个属性：
pkg、type 和 name。①pkg：定义节点所在的功能包名称。②type：定义节点的
可执行文件名称。③name：定义节点运行的名称，将覆盖 init()赋予节点的名称。

- <param>
  parameter 是 ROS 系统运行的参数，存储在参数服务器中。在节点中可以使用

```cpp
nh.getParam("my_param", param_value)
```

提取，注意局部参数get时需要加上前缀，如`"/self_avoid_node/speed_forward"`；在 launch 文件中通过<param>加载 parameter，语法如下：

```xml
<param name="my_param" type="string" value="hello_world" />
```

- <arg>
  argument 类似于 launch 文件内部的局部变量，仅限于在 launch 文件中使用，
  便于 launch 文件的重构，与 ROS 节点内部的实现没有关系。设置 argument 使用
  <arg>标签，语法如下：

```xml
<arg name=”arg-name” default=”arg-value” / >
```

调用时可以使用`"$(arg arg-name)"`

- <remap>

  ROS提供一种重映射的机制，简单来说就是取别名，类似于C++中的别名机制，我们不需要修改别人功能包的接口，只需要将接口名称重映射一下，取个别名，我们的系统就认识了（接口的数据类型必须相同）。launch文件中的`<remap>`标签顾名思义重映射，emap标签里包含一个`original-name`和一个`new-name`，及原名称和新名称。比如turtlebot的键盘控制节点，发布的速度控制指令话题可能是`/turtlebot/cmd_vel`，但是我们自己的机器人订阅的速度控制话题是`/cmd_vel`，这个时候使用`<remap>`就可以轻松解决问题，将`/turtlebot /cmd_vel`重映射为`/cmd_vel`，我们的机器人就可以接收到速度控制指令了：

```xml
<remap from="/turtlebot/cmd_vel" to="/cmd_vel"/>
```





## Managing System dependencies

`rosdep install [package]`

`rosdep update`

## Roslaunch tips for large projects





---

# 遇到的问题

### roscore 无响应

原因：环境配置文件错误，IP地址没有配正确

措施：

```sh
$ ifconfig                        #查看自己的ip地址
$ gedit ~/.bashrc　　#修改
$ source ~/.bashrc     #source 　
```

#### rqt_graph 为空界面

```bash
rqt_graph --clear-config
```

#### bag 转换csv

`rostopic echo -b <BAGFILE> -p <TOPIC> > <output>.csv`

#### 换源

`sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.ustc.edu.cn/ros/ubuntu/ $DISTRIB_CODENAME main" > /etc/apt/sources.list.d/ros-latest.list'`

`sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654`

#### clion使用ROS时无法自动补全或CMake报错

使用命令行在工作空间打开clion，即可实现

