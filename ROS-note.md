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





# ROS2相关

基本操作参考官方例程(https://docs.ros.org/en/rolling/Tutorials.html)，动手学ros2(https://fishros.com/d2lros2/#/)、ros2翻译官方教程(https://fishros.org/doc/ros2/humble/Tutorials/Beginner-CLI-Tools/Configuring-ROS2-Environment.html)和[帖子](https://foooor.com/ROS2/01-ROS%E7%AE%80%E4%BB%8B.html)。

## 常见的依赖问题

### 使用`tf_transformations`会报错与Numpy2.0的版本问题

`sudo apt install  ros-humble-tf-transformations` 其依赖`transforms3d`的版本过低，升级即可：

```bash
# 展示当前版本
pip show transforms3d
# 安装最新版本（推荐）
pip install --upgrade transforms3d
```



## 建立乌龟跟随示例

### 准备工作

新建工作空间、功能包、以及相关的节点代码等：

```bash
mkdir -p ~/ws_ros2/src
cd ws_ros2/src/
#创建功能包 ros2 pkg create --build-type ament_cmake(python)  <package_name>
ros2 pkg create follow_cpp --build-type ament_cmake --dependencies rclcpp tf2 tf2_ros geometry_msgs turtlesim
ros2 pkg create follow_py --build-type ament_python --dependencies rclpy tf_transformations tf2_ros geometry_msgs turtlesim
# 编译 使用symlink-install选项以便后续修改代码后无需重新编译
colcon build --packages-select your_package_name --symlink-install
source install/local_setup.bash
ros2 run ....
```

---

新建launch文件夹，并为别配置CMkaeLists.txt与setup.py文件，使其能检索到launch文件；同时确保可执行文件的添加与动态链接库的链接等：

对于CPP需要调整`CMakeLists.txt`以及`package.xml`

```cmake
# CMkaeLists.txt 添加sub_cpp_node节点对于库
# add the executable for each node
add_executable(sub_cpp_node 
               src/subscriber_lambda_function.cpp)
target_link_libraries(sub_cpp_node PUBLIC rclcpp::rclcpp ${std_msgs_TARGETS})
install(TARGETS
  sub_cpp_node
  DESTINATION lib/${PROJECT_NAME})
# 引入launch文件夹
install(DIRECTORY launch
  DESTINATION share/${PROJECT_NAME}
)
# package.xml 检查依赖，建立包指明时可以自动生成
<depend>rclcpp</depend>  
<exec_depend>ros2launch</exec_depend>
<depend>foooor_interface</depend>

```

**python软件包**需要在`setup.py` 内设置**entry point**以及检查`setup.cfg`的路径与`package.xml`中依赖、。

```python
# setup.py 需添加from glob import glob
data_files=[ ...
#一般只需要py的launch
    ('share/' + package_name, glob('launch/*.launch.py')),
    ]
#指定节点对应的可执行文件名称为 talker_py_node
entry_points={
        'console_scripts': [
                'talker_py_node = follow_py.publisher_member_function:main',
        ],
},
# package.xml 可指定依赖在建立包时自动生成,部分缺失依赖需要自己添加
<exec_depend>rclpy</exec_depend>
<exec_depend>std_msgs</exec_depend>
<exec_depend>ros2launch</exec_depend>
<depend>foooor_interface</depend>
...
# 检查setup.cfg，可执行文件放在 lib 中
[develop]
script_dir=$base/lib/follow_py
[install]
install_scripts=$base/lib/follow_py
```

---

### 自定义msg&srv(interface)

参考[fishros链接](https://fishros.org/doc/ros2/humble/Tutorials/Beginner-Client-Libraries/Custom-ROS2-Interfaces.html)，自定义的``.msg``和``.srv``文件需要放在一个单独的package中来编译，其编译结果最终会在`install/mine_interfaces/include`与`install/mine_interfaces/local/lib/python3.10/dist-packages`文件夹中。

```bash
# 创建接口编译软件包 mine_interfaces为包名，包类型必须ament_cmake
ros2 pkg create mine_interfaces --build-type ament_cmake --dependencies rosidl_default_generators geometry_msgs
```

修改`CMakeLists.txt`与`package.xml`，接口依赖于`rosidl_default_generators`来生成特定语言的代码，所以您需要在其上声明构建工具依赖项。rosidl_default_runtime是一个运行时或执行阶段的依赖项，以便稍后能够使用这些接口。rosidl_interface_packages是您的软件包“mine_interfaces”应关联的依赖组的名称，使用`<member_of_group>`标签声明。

```cmake
find_package(geometry_msgs REQUIRED) # 依赖的别的msg包
find_package(rosidl_default_generators REQUIRED) # 生成msg的软件包

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/Num.msg"
  "msg/Sphere.msg"
  "srv/AddThreeInts.srv"
  DEPENDENCIES geometry_msgs # Add packages that above messages depend on, in this case geometry_msgs for Sphere.msg
)
###############################
<depend>geometry_msgs</depend>
<buildtool_depend>rosidl_default_generators</buildtool_depend>
<exec_depend>rosidl_default_runtime</exec_depend>
<member_of_group>rosidl_interface_packages</member_of_group>
```

编译即可

```bash
colcon build --packages-select mine_interfaces
source install/setup.bash
```

不过最佳做法是在专门的接口包中声明接口，有时在一个包中声明、创建和使用接口会更方便。请注意，目前接口**只能在 CMake 包**中定义。 但是可以在 CMake 包中包含 Python 库和节点(使用 [ament_cmake_python](https://github.com/ament/ament_cmake/tree/humble/ament_cmake_python) ), 所以可以在**同一个包中定义接口并实现 Python 节点**。参考(https://blog.csdn.net/DIANZI520SUA/article/details/137510511)、(https://fishros.org/doc/ros2/humble/Tutorials/Beginner-Client-Libraries/Single-Package-Define-And-Use-Interface.html)

### 给出python实现的代码

两个节点以及一个launch文件：

```python
################"""    sim_launch.py   """############################
from launch import LaunchDescription # 导入LaunchDescription类
from launch_ros.actions import Node # 导入Node类，用于定义ROS2节点
from launch.actions import DeclareLaunchArgument, TimerAction
from launch.substitutions import LaunchConfiguration

def generate_launch_description():
    # 显式定义可配置的参数，在节点内可通过declare_parameter与get_parameter获取, 也可以隐式定义
    new_turtle_name_arg = DeclareLaunchArgument('new_turtle_name',default_value='t2',description='新乌龟名称')
    parent_turtle_arg = DeclareLaunchArgument('parent_turtle_name',default_value='t1',description='父乌龟的名称')
    # 创建乌龟模拟器节点，命名空间便是节点名称与话题前加的额外前缀  
    sim_node = Node(package="turtlesim", executable="turtlesim_node", name=LaunchConfiguration('parent_turtle_name'), 
                    namespace="sim_ns",output='screen',
                    remappings=[# 使用相对名称，节点内的'turtle1/pose'话题实际为'sim_ns/t1/pose'
                    ('turtle1/cmd_vel', 't1/cmd_vel'),
                    ('turtle1/pose', 't1/pose'),
                    ('turtle1/color_sensor', 't1/color_sensor')
                     ]) # 重映射话题名称，注意带命名空间
    # 创建生成乌龟的节点
    spawner_node = Node(
        package="follow_py",  
        executable="turtle_new_py_node",
        name="turtle_spawner",
        namespace="sim_ns",
        output='screen',
        parameters=[{
                    'parent_turtle': LaunchConfiguration('parent_turtle_name'),
                    'tf_publish_rate': 10.0,  # TF发布频率(Hz) # 常值参数定义
                    'new_turtle_name': LaunchConfiguration('new_turtle_name'),
                    'x': LaunchConfiguration('turtle_x', default='1.0'), # 隐式声明参数
                    'y': LaunchConfiguration('turtle_y', default='1.0'),
                    'theta': LaunchConfiguration('turtle_theta', default='0.0') 
        }]
    )
    # 创建控制节点
    control_node = Node(
        package="follow_py",
        executable="turtle_control_node",
        name="turtle_control",
        namespace="sim_ns",
        output='screen',
        parameters=[{
            'parent_turtle': LaunchConfiguration('parent_turtle_name'),
            'new_turtle_name': LaunchConfiguration('new_turtle_name'),
            'kp_linear': LaunchConfiguration('kp_linear',default='0.8'),
            'kp_angular': LaunchConfiguration('kp_angular',default='1.0'),
            'max_speed': LaunchConfiguration('max_speed',default='2.0'),
            'random_walk_interval': LaunchConfiguration('random_walk_interval', default='1.0')
        }]
    )
    # 创建rqt_tf_tree节点，用于可视化TF树 cmd : ros2 run rqt_tf_tree rqt_tf_tree  --force-discover
    rqt_tf_tree_node = Node(package='rqt_tf_tree', executable='rqt_tf_tree',name='rqt_tf_tree',
                            output='screen',
                            parameters=[{
                                'force_discover': 'true',  # 强制发现所有TF变换
                            }])

    # 延迟启动控制节点
    delayed_control_node = TimerAction(
        period=3.0,  # 延迟3秒
        actions=[control_node]
    )

    # 返回LaunchDescription对象，包含要启动的节点列表
    return LaunchDescription([
        new_turtle_name_arg,parent_turtle_arg,
        sim_node,spawner_node,delayed_control_node
        # rqt_tf_tree_node,
    ])
################"""    setup.py   """################################
from setuptools import find_packages, setup
from glob import glob
package_name = 'follow_py'
setup(
    name=package_name,
    version='0.0.0',
    packages=find_packages(exclude=['test']),
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
        ('share/' + package_name, glob("launch/*_launch.py")),

    ],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='thorn',
    maintainer_email='2432317262@qq.com',
    description='TODO: python package for demo',
    license='TODO: License declaration',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
            'turtle_new_py_node = follow_py.new_turtle:main',
            # 'turtle_dynamic_tf_pub_node = follow_py.tf_py.dynamic_tf_pub:main',
            'turtle_control_node = follow_py.control_turtle:main',
        ],
    },
)
################"""   package.xml   """################################
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>follow_py</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="2432317262@qq.com">thorn</maintainer>
  <license>TODO: License declaration</license>

  <depend>rclpy</depend>
  <depend>tf_transformations</depend>
  <depend>tf2_ros</depend>
  <depend>geometry_msgs</depend>
  <depend>turtlesim</depend>
  <exec_depend>ros2launch</exec_depend>

  <test_depend>ament_copyright</test_depend>
  <test_depend>ament_flake8</test_depend>
  <test_depend>ament_pep257</test_depend>
  <test_depend>python3-pytest</test_depend>
  <export>
    <build_type>ament_python</build_type>
  </export>
</package>

```

节点代码为

```
######################  new_turtle.py  ######################
#!/usr/bin/env python3
"""
turtle_manager_node.py - 乌龟管理与相对TF发布节点

该节点集成了以下功能：
1. 生成新的乌龟
2. 订阅新老乌龟的位姿信息
3. 计算并发布新乌龟相对于老乌龟的TF变换

使用ROS2参数系统进行配置：
- parent_turtle: 参考乌龟名称
- new_turtle_name: 新乌龟名称
- x, y, theta: 新乌龟的初始位置和朝向

启动后，节点将：
1. 解析参数
2. 调用/spawn服务生成新乌龟
3. 订阅新老乌龟的位姿
4. 定期计算相对位置并发布TF变换
"""

import rclpy
from rclpy.node import Node
from turtlesim.srv import Spawn  # 生成乌龟的服务接口
from turtlesim.msg import Pose   # 乌龟位姿消息
from geometry_msgs.msg import TransformStamped  # TF变换消息
import tf_transformations  # 用于四元数和欧拉角转换
import tf2_ros  # TF2库
import math    # 数学计算
import threading  # 多线程支持
from rclpy.executors import SingleThreadedExecutor  # 单线程执行器

class TurtleManager(Node):
    """乌龟管理与相对TF发布节点"""
    
    def __init__(self):
        """节点初始化"""
        # 调用父类构造函数，设置节点名称为'turtle_spawner'
        super().__init__('turtle_spawner')
        
        # 1. 声明和获取节点参数
        # 参数允许在启动时配置节点行为
        self.declare_parameter('parent_turtle')  # 参考乌龟名称
        self.declare_parameter('new_turtle_name')  # 新乌龟名称
        self.declare_parameter('x', 5.0)  # 新乌龟初始X坐标
        self.declare_parameter('y', 5.0)  # 新乌龟初始Y坐标
        self.declare_parameter('theta', 0.0)  # 新乌龟初始朝向(弧度)
        
        # 获取参数值
        self.parent_turtle = self.get_parameter('parent_turtle').value
        self.new_turtle_name = self.get_parameter('new_turtle_name').value
        self.x = self.get_parameter('x').value
        self.y = self.get_parameter('y').value
        self.theta = self.get_parameter('theta').value
        # 2. 初始化内部状态
        self.parent_pose = None  # 存储参考乌龟的位姿
        self.child_pose = None   # 存储新乌龟的位姿
        # 3. 创建服务客户端 - 用于生成新乌龟
        # 连接到turtlesim的/spawn服务
        self.spawn_client = self.create_client(Spawn, 'spawn') # 注意不是/spawn
        # 4. 创建TF广播器 - 用于发布相对TF变换
        self.broadcaster = tf2_ros.TransformBroadcaster(self)
        # 5. 订阅参考乌龟的位姿
        # 当参考乌龟移动时，会触发回调函数更新其位姿
        self.parent_sub = self.create_subscription(Pose,  # 消息类型
            f'{self.parent_turtle}/pose',  # 话题名称
            self.parent_pose_callback,  # 回调函数
            10  # 队列大小
        )
        # 6. 创建定时器 - 定期发布TF变换
        # 设置发布频率为10Hz (0.1秒间隔)
        self.tf_timer = self.create_timer(0.1, self.publish_relative_tf)
        # 7. 生成新乌龟
        self.spawn_new_turtle()

        self.get_logger().info("乌龟管理节点已启动")
    
    def spawn_new_turtle(self):
        """生成新乌龟的方法"""
        # 1. 等待服务可用
        # 循环等待直到/sim_ns/spawn服务可用
        while not self.spawn_client.wait_for_service(timeout_sec=1.0):
            self.get_logger().info('等待spawn服务可用...')
        # 2. 创建服务请求
        request = Spawn.Request()
        request.x = self.x  # 设置X坐标
        request.y = self.y  # 设置Y坐标
        request.theta = self.theta  # 设置初始朝向
        request.name = self.new_turtle_name  # 设置乌龟名称
        # 3. 异步调用服务 使用异步调用避免阻塞主线程
        future = self.spawn_client.call_async(request)
        # 4. 使用独立线程处理服务响应,创建新线程处理服务响应，避免阻塞主线程
        thread = threading.Thread(target=self.handle_spawn_response, args=(future,))
        thread.start()
    
    def handle_spawn_response(self, future):
        # 1. 创建独立执行器,使用单线程执行器处理服务响应
        executor = SingleThreadedExecutor()
        executor.add_node(self)  # 将当前节点添加到执行器
        # 2. 等待服务完成
        executor.spin_until_future_complete(future)
        # 3. 处理服务结果
        if future.done():
            try:
                response = future.result()# 获取服务响应
                if response:
                    self.get_logger().info(f'成功生成乌龟: {response.name}')
                    # 4. 订阅新乌龟的位姿
                    self.child_sub = self.create_subscription(Pose, f'{self.new_turtle_name}/pose',  
                                                              self.child_pose_callback, 10)
                else:
                    self.get_logger().error('生成乌龟失败: 服务返回空')
            except Exception as e:
                self.get_logger().error(f'生成乌龟失败: {str(e)}')# 处理服务调用异常
        else:
            self.get_logger().error('服务调用超时')# 服务调用超时
    
    def parent_pose_callback(self, msg):
        self.parent_pose = msg # 存储参考乌龟的最新位姿
    
    def child_pose_callback(self, msg):
        self.child_pose = msg # 存储新乌龟的最新位姿
    
    def publish_relative_tf(self):
        """发布新乌龟相对于参考乌龟的TF变换"""
        # 1. 检查位姿数据是否可用
        if self.parent_pose is None or self.child_pose is None:
            # 如果任一乌龟的位姿尚未收到，跳过本次发布
            return
        # 2. 计算相对位置
        # 计算新乌龟相对于参考乌龟的X和Y偏移
        dx = self.child_pose.x - self.parent_pose.x
        dy = self.child_pose.y - self.parent_pose.y
        # 3. 计算相对角度
        # 计算新乌龟相对于参考乌龟的角度差
        dtheta = self.child_pose.theta - self.parent_pose.theta
        # 4. 创建TransformStamped消息
        tf_ = TransformStamped()
        # 5. 设置消息头
        tf_.header.stamp = self.get_clock().now().to_msg()  # 当前时间戳
        tf_.header.frame_id = self.parent_turtle  # 父坐标系(第一只乌龟)，为原点
        tf_.child_frame_id = f"{self.new_turtle_name}_relative"  # 子坐标系(相对位置)
        # 6. 设置平移部分
        tf_.transform.translation.x = dx  # X方向偏移
        tf_.transform.translation.y = dy  # Y方向偏移
        tf_.transform.translation.z = 0.0  # Z方向无偏移(2D平面)
        
        # 7. 设置旋转部分
        # 将相对角度(欧拉角)转换为四元数
        q = tf_transformations.quaternion_from_euler(0, 0, dtheta)
        tf_.transform.rotation.x = q[0]  # 四元数X分量
        tf_.transform.rotation.y = q[1]  # 四元数Y分量
        tf_.transform.rotation.z = q[2]  # 四元数Z分量
        tf_.transform.rotation.w = q[3]  # 四元数W分量
        
        # 8. 发布TF变换
        self.broadcaster.sendTransform(tf_)
        
        # 9. 记录日志(每秒最多一次)
        self.get_logger().info(f'{tf_}', throttle_duration_sec=1.0)  # 节流控制，每秒最多记录一次


def main(args=None):
    """主函数"""
    # 1. 初始化ROS2 Python客户端库
    rclpy.init(args=args)
    
    try:
        # 2. 创建节点实例
        node = TurtleManager()
        # 3. 进入事件循环
        # 节点会持续运行，处理回调函数
        rclpy.spin(node)
    except KeyboardInterrupt:
        # 4. 处理键盘中断(Ctrl+C)
        pass
    finally:
        # 5. 清理资源
        node.destroy_node()  # 销毁节点
        rclpy.shutdown()     # 关闭ROS2


if __name__ == '__main__':
    # 程序入口
    main()

######################  control_turtle.py  ######################
#!/usr/bin/env python3
"""
turtle_control_node.py - 乌龟控制与跟踪节点

该节点集成了以下功能：
1. 控制第一只乌龟随机走动
2. 计算第二只乌龟的跟踪控制指令
3. 发布两只乌龟的速度指令
4. 实时显示跟踪状态

使用ROS2参数系统进行配置：
- parent_turtle: 第一只乌龟名称
- new_turtle_name: 第二只乌龟名称
- kp_linear: 线性跟踪比例系数
- kp_angular: 角度跟踪比例系数
- max_speed: 最大速度限制
"""

import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist
import random
import math

from tf2_ros import Buffer, TransformListener  # 导入 TF2 缓冲区和监听器
from tf2_geometry_msgs import PointStamped  # 导入带有坐标系信息的点消息类型
from rclpy.duration import Duration  # 导入用于设置超时时间的 Duration

class TurtleControlNode(Node):
    """乌龟控制与跟踪节点"""
    
    def __init__(self):
        """节点初始化"""
        super().__init__('turtle_control_node')
        # 1. 声明和获取节点参数
        self.declare_parameter('parent_turtle')  # 第一只乌龟名称
        self.declare_parameter('new_turtle_name')  # 第二只乌龟名称
        self.declare_parameter('kp_linear', 0.5)  # 线性跟踪比例系数
        self.declare_parameter('kp_angular', 2.0)  # 角度跟踪比例系数
        self.declare_parameter('max_speed', 2.0)  # 最大速度限制
        self.declare_parameter('random_walk_interval', 1.0)  # 随机走动间隔(秒)
        
        self.parent_turtle = self.get_parameter('parent_turtle').value
        self.new_turtle_name = self.get_parameter('new_turtle_name').value
        self.kp_linear = self.get_parameter('kp_linear').value
        self.kp_angular = self.get_parameter('kp_angular').value
        self.max_speed = self.get_parameter('max_speed').value
        self.random_walk_interval = self.get_parameter('random_walk_interval').value

        # 2.创建一个 TF2 缓冲区和 TransformListener 对象，监听坐标转换关系,使用tf变换得到
        self.tf_buffer  = Buffer()
        self.tf_listener  = TransformListener(self.tf_buffer, self)  # 将当前节点作为监听器的依赖
        
        # 3. 创建速度发布器
        self.parent_cmd_pub = self.create_publisher(Twist, f'{self.parent_turtle}/cmd_vel', 10)
        self.child_cmd_pub = self.create_publisher(Twist,  f'{self.new_turtle_name}/cmd_vel', 10)
        self.get_logger().info(f'订阅节点 {self.new_turtle_name}/cmd_vel')

        # 5. 创建定时器
        # 随机走动定时器
        self.random_walk_timer = self.create_timer(
            self.random_walk_interval, 
            self.random_walk
        )
        
        # 跟踪控制定时器
        self.tracking_timer = self.create_timer(
            0.1,  # 10Hz控制频率
            self.track_parent
        )
        
        # 6. 记录初始化完成
        self.get_logger().info("乌龟控制节点已启动")
        self.get_logger().info(f"控制参数: kp_linear={self.kp_linear}, kp_angular={self.kp_angular}")
    
    def random_walk(self):
        """控制第一只乌龟随机走动"""
        # if self.parent_pose is None:
        #     self.get_logger().warn("父乌龟位姿未知，无法执行随机走动")
        #     return
        
        # 创建随机速度指令
        cmd = Twist()
        
        # 随机选择前进或转向
        if random.random() > 0.5:
            # 前进
            cmd.linear.x = random.uniform(0.5, self.max_speed)
            cmd.angular.z = random.uniform(-0.5, 0.5)
        else:
            # 转向
            cmd.linear.x = random.uniform(0, 0.5)
            cmd.angular.z = random.uniform(-2.0, 2.0)
    
        # 发布速度指令
        self.parent_cmd_pub.publish(cmd)
        # 记录日志
        self.get_logger().info(f"随机控制: {self.parent_turtle} "
            f"线速度={cmd.linear.x:.2f}, 角速度={cmd.angular.z:.2f}",throttle_duration_sec=1.0)
    
    def track_parent(self):
        """控制第二只乌龟跟踪第一只乌龟"""
        # 获取相对TF变换，第一只为父坐标系
        trans = self.tf_buffer.lookup_transform(
            self.parent_turtle,  # 目标坐标系 (父乌龟)
            f"{self.new_turtle_name}_relative",  # 源坐标系 (相对坐标系)
            rclpy.time.Time()  # 获取从源到目标最新变换
        )
        
        # 从变换中提取相对位置和角度
        dx = trans.transform.translation.x
        dy = trans.transform.translation.y
        
        # 从四元数中提取偏航角 (绕Z轴旋转)
        q = trans.transform.rotation
        # 四元数转换为欧拉角
        siny_cosp = 2 * (q.w * q.z + q.x * q.y)
        cosy_cosp = 1 - 2 * (q.y * q.y + q.z * q.z)
        yaw = math.atan2(siny_cosp, cosy_cosp)
        
        # 计算距离和角度
        distance = math.sqrt(dx**2 + dy**2)
        angle_diff = -1*yaw  # 角度差就是偏航角
        angle_diff = math.atan2(math.sin(angle_diff), math.cos(angle_diff))
        
        # 创建速度指令
        cmd = Twist()
        
        # 比例控制
        cmd.linear.x = min(self.kp_linear * distance, self.max_speed)
        cmd.angular.z = self.kp_angular * angle_diff
        
        # 发布速度指令
        self.child_cmd_pub.publish(cmd)
        
        # 记录日志
        self.get_logger().info(
            f"跟踪控制: {self.new_turtle_name} -> {self.parent_turtle}\n"
            f"  距离={distance:.2f}, 角度差={math.degrees(angle_diff):.1f}°\n"
            f"  线速度={cmd.linear.x:.2f}, 角速度={cmd.angular.z:.2f}",
            throttle_duration_sec=0.5
        )


def main(args=None):
    """主函数"""
    rclpy.init(args=args)
    try:
        # 创建节点实例
        node = TurtleControlNode()
        # 进入事件循环
        rclpy.spin(node)
    
    except KeyboardInterrupt:
        pass
    finally:
        # 清理资源
        node.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()
    
```



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

