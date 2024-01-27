# ROS学习笔记—DJZ

## 1.核心概念

**节点**（执行单元Node）：执行具体任务、进程的可执行文件；

**节点管理器**（控制中心ROS Master）:为节点提供命名与服务，跟踪记录话题，提供参数服务器；

**话题**（topic）：使用发布/订阅模型，数据从发送者传输到订阅者，异步通信；

**消息**（Message）：话题数据，包括ROS提供的标准类型和用户自定义模型；

**服务**（Service）：使用客户端/服务器模型，客户端发请求，服务器处理后返回应答数据，同步通信

**参数**（全局共享字典Parameter）：存储和检索运行时的参数

**功能包**、**元功能包**、**功能包清单**

## 2.命令行工具

```shell
roscore 			    #启动ros master
rosrun <包名> <节点>	  #运行节点
rqt_<功能>			   #可视化工具
rosnode list		    #列出node节点
rosnode info <节点名>    #查看节点信息
rostopic list			#查看话题列表
rostopic pub <话题名> <数据结构> <具体数据>  
#发布数据给指定话题，pub后加-r表示循环执行，加每秒执行的次数，例如：rostopic pub -r 10 /turtle1/cmd_vel geometry_msgs/Twist 
rostopic echo <话题名>		#可以看到某个话题下的消息
rosmsg show <话题的内容> #显示该msg的参数等信息
rosservice list 		#显示提供的服务列表
rosbag record -a -O <包名> #将系统中所有话题数据保存
rosbag call <包名>		#复现保存的话题数据
```



## 3.创建工作空间和功能包

### (1).创建工作空间

- **src**：代码空间（source space），放置功能包
- **build**：编译空间（build space），放置编译中间文件，不用管
- **devel**：开发空间（Development space），放置编译生成的可执行文件、库
- **install**：安装空间（install space），一般用不到

**相关命令**

创建工作空间

```shell
$ mkdir -p ~/<工作空间名>/src
$ cd ~/<工作空间名>/src
$ catkin_init_workspace
```

编译工作空间

```shell
$ cd ~/<工作空间名>/
$ catkin_make
```

设置环境变量

```shell
$ source devel/setup.bash
```

检查环境变量

```shell
$ echo $ROS_PACKAGE_PATH
```

### (2).创建功能包

创建功能包,同一个工作空间下不可以有同名功能包

```shell
$ cd ~/<工作空间名>/src
$ catkin_create_pkg test_pkg std_msgs rospy roscpp 
// catkin_creare_pkg <功能包名> <依赖1> <依赖2> ……
```

编译功能包

```shell
$ cd ~/<工作空间名>
$ catkin_make
$ source ~/<工作空间名>/devel/setup.bash
```

如果出现`Unable to find either executable ‘empy’ or Python module ‘em’… try`,则使用下面的命令：

```shell
$ catkin_make -DPYTHON_EXECUTABLE=/usr/bin/python3
```

运行功能包程序需要首先设置环境变量，即设置`setup.bash`,`echo`命令可以检查环境变量

```shell
$ echo $ROS_PAKAGE_PATH
```

## 4.话题Topic 与消息Message



- 话题Topic是节点之间进行持续通讯的形式
- Topic中通讯的信息叫做消息Message
- 消息会按照一定的频率持续发送
- 消息发送方是publisher，接收方是subscriber
- ROS节点网络中可以有多个Topic
- 一个话题的发布者和订阅者可以有多个

## 5. publisher和subscriber的编程实现

### (1).发布者publisher的编程实现(c++)

- 明确消息的类型
- 在ROS.index上查找常见库中该类型的变量名（如std_msgs）
- 在代码文件中include消息类型对应的头文件
- 在main函数中通过NodeHandle发布一个话题并得到消息发送对象
- 生成发送的消息包并进行发送数据的赋值
- 调用消息发送对象的publish()函数将消息包发送到话题中
- 例子：

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"

int main(int argc,char **argv)
{   
    ros::init(argc,argv,"chao_node");
    printf("Hello World!\n");

    ros::NodeHandle nh;
    ros::Publisher pub;
    pub = nh.advertise<std_msgs::String>("test", 10);

    ros::Rate loop_rate(10);

    while(ros::ok())
    {
        std_msgs::String msg;
        msg.data = "This is a test";
        pub.publish(msg);
        loop_rate.sleep();
    }
    return 0;
}
```

- 修改Cmakelist文件
- 编译并运行发布者

### (2).发布者publisher的编程实现(python)

与C++实现的过程相同，区别仅仅在于语法的差别

例如：

```python
#!/usr/bin/env python3
#conding = utf-8

import rospy
from std_msgs.msg import String

if __name__=="__main__":
    rospy.init_node("chao_node")
    rospy.logwarn("init_done")

    pub = rospy.Publisher("test1",String,queue_size=10)

    rate = rospy.Rate(10)
    while not rospy.is_shutdown():
        rospy.loginfo("test test test")
        msg = String()
        msg.data = "This is a test"
        pub.publish(msg)
        rate.sleep()

```

### (3).订阅者subscriber的编程实现(c++)

- 确定话题名称和消息类型
- 在代码文件中include<ros.h>以及消息类型对应的头文件
- 在main函数中通过Nodeandle订阅话题并设置消息接受回调函数
- 定义回调函数，对接收到的消息包进行处理
- main函数中执行ros::spinOnce(),让回调函数能够相应接到的消息包
- rqt_graph可以分析Topic的关系

例如：

```cpp
#include "ros/ros.h"
#include "std_msgs/String.h"

void chao_callback(std_msgs::String msg)
{
    ROS_INFO(msg.data.c_str());
}
void yao_callback(std_msgs::String msg)
{
    ROS_WARN(msg.data.c_str());
}

int main(int argc,char **argv)
{
    ros::init(argc,argv,"ma_node");

    ros::NodeHandle nh;
    ros::Subscriber sub;
    ros::Subscriber sub_2;
    sub = nh.subscribe<std_msgs::String>("test", 10, chao_callback);
    sub_2 = nh.subscribe<std_msgs::String>("test1", 10, yao_callback);
    while(ros::ok())
    {
        ros::spinOnce();
    }
    return 0;
}
```

### (4).订阅者subscriber的编程实现(python)

与C++实现的过程相同，区别仅仅在于语法的差别

例如：

```python
#!/usr/bin/env python3
#conding = utf-8

import rospy
from std_msgs.msg import String

def chaocallback(msg):
    rospy.loginfo(msg.data)

def yaocallback(msg):
    rospy.logwarn(msg.data)

if __name__ == "__main__":
    rospy.init_node("ma_node")

    sub = rospy.Subscriber("test1",String,chaocallback,queue_size=10)
    sub2 = rospy.Subscriber("test2",String,yaocallback,queue_size=10)

    rospy.spin()
```



## 6.使用launch文件启动多个ROS节点

使用`xml`语法，格式如下：

```xml
<launch>
    <node pkg="ssr_pkg" type="yao_node" name="yao_node"/>
    <node pkg="ssr_pkg" type="chao_node" name="chao_node"/>
    <node pkg="atr_pkg" type="ma_node" name="ma_node" output="screen"/>
    # 属性分别为包名、节点名、节点指派名称
</launch>
```

- 为节点添加`output = "screen"`属性，可以让节点信息输出在终端中(ROS_WARN不受该属性控制)
- 为节点添加`launch-prefix="gonme-terminal -e"`属性，可以让节点单独运行在一个独立终端中

## 7. rosservice和rosparam

### (1)ros服务命令

```shell
$ rosservice list 	#查看服务列表
$ rosservice type 	#查看服务的类型
$ rosservice call	#调用服务
$ rosservice find	#根据消息类型获取话题
$ rosservice info 	#获取服务话题详情
```

### (2)ROS参数命令

```shell
$ rosparam set            #设定参数
$ rosparam get            #获取参数值
$ rosparam load           #从文件中加载参数
$ rosparam dump           #把参数发送到文件中
$ rosparam delete         #删除参数
$ rosparam list           #列出参数
```

## 8.客户端client和服务端server的编程实现

### (1)客户端client的编程实现

- 初始化ROS节点
- 创建Client实例
- 发布服务请求数据
- 等待Server处理后的应答结果

C++实例：

```cpp
#include <ros/ros.h>
#include <turtlesim/Spawn.h>

int main(int argc, char** argv)
{
    // 初始化ROS节点
	ros::init(argc, argv, "turtle_spawn");

    // 创建节点句柄
	ros::NodeHandle node;

    // 发现/spawn服务后，创建一个服务客户端，连接名为/spawn的service
	ros::service::waitForService("/spawn");
	ros::ServiceClient add_turtle = node.serviceClient<turtlesim::Spawn>("/spawn");

    // 初始化turtlesim::Spawn的请求数据
	turtlesim::Spawn srv;
	srv.request.x = 2.0;
	srv.request.y = 2.0;
	srv.request.name = "turtle2";

    // 请求服务调用
	ROS_INFO("Call service to spwan turtle[x:%0.6f, y:%0.6f, name:%s]", 
			 srv.request.x, srv.request.y, srv.request.name.c_str());

	add_turtle.call(srv);

	// 显示服务调用结果
	ROS_INFO("Spwan turtle successfully [name:%s]", srv.response.name.c_str());

	return 0;
};
```

python实例：

```python
import sys
import rospy
from turtlesim.srv import Spawn

def turtle_spawn():
	# ROS节点初始化
    rospy.init_node('turtle_spawn')

	# 发现/spawn服务后，创建一个服务客户端，连接名为/spawn的service
    rospy.wait_for_service('/spawn')
    try:
        add_turtle = rospy.ServiceProxy('/spawn', Spawn)

		# 请求服务调用，输入请求数据
        response = add_turtle(2.0, 2.0, 0.0, "turtle2")
        return response.name
    except rospy.ServiceException, e:
        print "Service call failed: %s"%e

if __name__ == "__main__":
	#服务调用并显示调用结果
    print "Spwan turtle successfully [name:%s]" %(turtle_spawn())
```

### (2)服务端server的编程实现

- 初始化ROS节点
- 创建server实例
- 循环等待服务请求，进入回调函数
- 在回调函数中完成服务功能的处理，并反馈应答数据

C++实例：

```cpp
#include <ros/ros.h>
#include <geometry_msgs/Twist.h>
#include <std_srvs/Trigger.h>

ros::Publisher turtle_vel_pub;
bool pubCommand = false;

// service回调函数，输入参数req，输出参数res
bool commandCallback(std_srvs::Trigger::Request  &req,
         			std_srvs::Trigger::Response &res)
{
	pubCommand = !pubCommand;

    // 显示请求数据
    ROS_INFO("Publish turtle velocity command [%s]", pubCommand==true?"Yes":"No");

	// 设置反馈数据
	res.success = true;
	res.message = "Change turtle command state!";

    return true;
}

int main(int argc, char **argv)
{
    // ROS节点初始化
    ros::init(argc, argv, "turtle_command_server");

    // 创建节点句柄
    ros::NodeHandle n;

    // 创建一个名为/turtle_command的server，注册回调函数commandCallback
    ros::ServiceServer command_service = n.advertiseService("/turtle_command", commandCallback);

	// 创建一个Publisher，发布名为/turtle1/cmd_vel的topic，消息类型为geometry_msgs::Twist，队列长度10
	turtle_vel_pub = n.advertise<geometry_msgs::Twist>("/turtle1/cmd_vel", 10);

    // 循环等待回调函数
    ROS_INFO("Ready to receive turtle command.");

	// 设置循环的频率
	ros::Rate loop_rate(10);

	while(ros::ok())
	{
		// 查看一次回调函数队列
    	ros::spinOnce();
		
		// 如果标志为true，则发布速度指令
		if(pubCommand)
		{
			geometry_msgs::Twist vel_msg;
			vel_msg.linear.x = 0.5;
			vel_msg.angular.z = 0.2;
			turtle_vel_pub.publish(vel_msg);
		}

		//按照循环频率延时
	    loop_rate.sleep();
	}

    return 0;
}
```

python实例：

```python
import rospy
import thread,time
from geometry_msgs.msg import Twist
from std_srvs.srv import Trigger, TriggerResponse

pubCommand = False;
turtle_vel_pub = rospy.Publisher('/turtle1/cmd_vel', Twist, queue_size=10)

def command_thread():	
	while True:
		if pubCommand:
			vel_msg = Twist()
			vel_msg.linear.x = 0.5
			vel_msg.angular.z = 0.2
			turtle_vel_pub.publish(vel_msg)
			
		time.sleep(0.1)

def commandCallback(req):
	global pubCommand
	pubCommand = bool(1-pubCommand)

	# 显示请求数据
	rospy.loginfo("Publish turtle velocity command![%d]", pubCommand)

	# 反馈数据
	return TriggerResponse(1, "Change turtle command state!")

def turtle_command_server():
	# ROS节点初始化
    rospy.init_node('turtle_command_server')

	# 创建一个名为/turtle_command的server，注册回调函数commandCallback
    s = rospy.Service('/turtle_command', Trigger, commandCallback)

	# 循环等待回调函数
    print "Ready to receive turtle command."

    thread.start_new_thread(command_thread, ())
    rospy.spin()

if __name__ == "__main__":
    turtle_command_server()
```

## 9.自定义消息类型

### (1)生成自定义消息的步骤

- 创建新软件包，依赖项`message_generation` `message_runtime`
- 软件包添加msg目录，新建自定义消息文件，以`.msg`结尾 
- 在CMakeLists.txt中，将新建的`.msg`文件加入`add_message_files()`
- 在CMakeLists.txt中，去掉`generate_messages()`注释符号，将依赖的其他信息包名称添加进去
- 在CMakeLists.txt中，将`message_runtime`加入`catkin_package()`的`CATKIN_DEPENDS`
- 在`package.xml`中，将`message_generation ` `message_runtime`加入`<build_depend>`和`<exec_depend>`
- 编译软件包，生成新消息类型

### (2)自定义消息在C++节点的应用

- 在节点代码中，先`include`新消息类型的头文件
- 在发布或订阅话题时，将话题中的消息类型设置为新的消息类型
- 按照新的消息结构，对消息包进行赋值发送或读取解析
- 在CMakeLists.txt中`find_package()`添加新消息包名称作为依赖项
- 在节点的编译规则中，添加一条`add_dependencies()`,将`<新消息软件包名称>_generate_messages_cpp`作为依赖项
- 在`package.xml`中，将新消息包添加到`<build_depend>`和`<exec_depend>`中

### (3)自定义消息在python节点的应用

操作与C++部分类似

## 10. ROS service 的编程实现

### (1).C++的ROS service实现

- 编写client节点

```cpp
/**
 * 该例程将请求/spawn服务，服务数据类型turtlesim::Spawn
 */

#include <ros/ros.h>
#include <turtlesim/Spawn.h>

int main(int argc, char** argv)
{
    // 初始化ROS节点
	ros::init(argc, argv, "turtle_spawn");

    // 创建节点句柄
	ros::NodeHandle node;

    // 发现/spawn服务后，创建一个服务客户端，连接名为/spawn的service
	ros::service::waitForService("/spawn");//阻塞型函数
	ros::ServiceClient add_turtle = node.serviceClient<turtlesim::Spawn>("/spawn");

    // 初始化turtlesim::Spawn的请求数据
	turtlesim::Spawn srv;
	srv.request.x = 2.0;
	srv.request.y = 2.0;
	srv.request.name = "turtle2";

    // 请求服务调用
	ROS_INFO("Call service to spwan turtle[x:%0.6f, y:%0.6f, name:%s]", 
			 srv.request.x, srv.request.y, srv.request.name.c_str());

	add_turtle.call(srv); //阻塞型函数

	// 显示服务调用结果
	ROS_INFO("Spwan turtle successfully [name:%s]", srv.response.name.c_str());

	return 0;
};
```

设置需要编译的代码和生成的可执行文件
设置链接库

```shell
//配置CMakeLists.txt
add_executable(turtle_spawn src/turtle_spawn.cpp)
target_link_libraries(turtle_spawn ${catkin_LIBRARIES})
```

- 编写server节点

```cpp
//代码
/**
 * 该例程将执行/turtle_command服务，服务数据类型std_srvs/Trigger
 */
 
#include <ros/ros.h>
#include <geometry_msgs/Twist.h>
#include <std_srvs/Trigger.h>

ros::Publisher turtle_vel_pub;
bool pubCommand = false;

// service回调函数，输入参数req，输出参数res
bool commandCallback(std_srvs::Trigger::Request  &req,
         			std_srvs::Trigger::Response &res)
{
	pubCommand = !pubCommand;

    // 显示请求数据
    ROS_INFO("Publish turtle velocity command [%s]", pubCommand==true?"Yes":"No");

	// 设置反馈数据
	res.success = true;
	res.message = "Change turtle command state!"

    return true;
}

int main(int argc, char **argv)
{
    // ROS节点初始化
    ros::init(argc, argv, "turtle_command_server");

    // 创建节点句柄
    ros::NodeHandle n;

    // 创建一个名为/turtle_command的server，注册回调函数commandCallback
    ros::ServiceServer command_service = n.advertiseService("/turtle_command", commandCallback);

	// 创建一个Publisher，发布名为/turtle1/cmd_vel的topic，消息类型为geometry_msgs::Twist，队列长度10
	turtle_vel_pub = n.advertise<geometry_msgs::Twist>("/turtle1/cmd_vel", 10);

    // 循环等待回调函数
    ROS_INFO("Ready to receive turtle command.");

	// 设置循环的频率
	ros::Rate loop_rate(10);

	while(ros::ok())
	{
		// 查看一次回调函数队列
    	ros::spinOnce();
		
		// 如果标志为true，则发布速度指令
		if(pubCommand)
		{
			geometry_msgs::Twist vel_msg;
			vel_msg.linear.x = 0.5;
			vel_msg.angular.z = 0.2;
			turtle_vel_pub.publish(vel_msg);
		}

		//按照循环频率延时
	    loop_rate.sleep();
	}

    return 0;
}

```

- 在CMakelist中添加语句

```shell
//配置CMakeLists.txt
add_executable(turtle_command_server src/turtle_command_server.cpp)
target_link_libraries(turtle_command_server ${catkin_LIBRARIES})
```

### (2).python的ROS service实现

- 创建客户端代码(python)

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# 该例程将请求/spawn服务，服务数据类型turtlesim::Spawn

import sys
import rospy
from turtlesim.srv import Spawn

def turtle_spawn():
	# ROS节点初始化
    rospy.init_node('turtle_spawn')

	# 发现/spawn服务后，创建一个服务客户端，连接名为/spawn的service
    rospy.wait_for_service('/spawn')
    try:
        add_turtle = rospy.ServiceProxy('/spawn', Spawn)

		# 请求服务调用，输入请求数据
        response = add_turtle(2.0, 2.0, 0.0, "turtle2")
        return response.name
    except rospy.ServiceException, e:
        print "Service call failed: %s"%e

if __name__ == "__main__":
	#服务调用并显示调用结果
    print "Spwan turtle successfully [name:%s]" %(turtle_spawn())
```
