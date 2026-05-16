
# ROS 2 机器人操作系统课程教学设计（14讲）

## 课程定位与理念

本课程的核心思想：**用最轻量的环境，做最直观的理解**。

- 使用 **Ubuntu Server 22.04（命令行版）+ 远程图形显示**。
- 每节课一个核心道理 + 一张示意图 + 一个可操作例子。
- 最终大作业：做一个 **基于 ROS 2 的仿真数字孪生场景**。

---

## 第一讲：我们为什么要学 ROS 2？

### 本讲道理
ROS 2 不是一门新语言，而是一个**通信中介**——它让机器人的各个零件（程序）能够互相“对话”。

### 示意图
```
[摄像头程序]  →  ROS 2 话题  →  [图像处理程序]
      ↓                         ↓
    “我拍到人了”              “我来画个框”
```

### 通俗讲解
想象一下，造一台机器人，需要写100个程序：控制电机的、看摄像头的、做决策的。如果没有 ROS 2，每个程序之间要约定好怎么传数据，复杂又易错。

ROS 2 就像微信群——每个程序进群，想说话就“发布消息”，想听别人说话就“订阅消息”。程序之间不用互相认识，只需要知道群名称（话题名）。

**ROS 1 和 ROS 2 的区别**：ROS 1 是上个时代的微信群，只能局域网用，群主（Master）挂了群就没了。ROS 2 是新一代微信群，分布式、不用群主、更安全、还能跨互联网通信。

### 本节课例子：装好环境并跑通第一个“群聊”

#### 环境安装步骤（详细）

**方案一：本地虚拟机（推荐学生首选）**

1. 下载 VirtualBox（免费）：https://www.virtualbox.org/
2. 下载 Ubuntu Server 22.04 LTS ISO：https://ubuntu.com/download/server
3. VirtualBox 新建虚拟机：
   - 名称：ROS2_Humble_Server
   - 类型：Linux，版本：Ubuntu (64-bit)
   - 内存：至少 4GB
   - 硬盘：20GB 动态分配
4. 启动虚拟机，选择下载的 ISO，安装过程：
   - 语言选 English
   - 键盘布局选 Chinese
   - 网络自动获取
   - **重点：** 在 Software Selection 界面，按空格取消所有选项（不装桌面），直接 Done
   - 设置用户名和密码（例如用户名：student，密码：ros2025）
5. 安装完成后重启，登录命令行。

#### 安装 ROS 2 Humble（Ubuntu 22.04）

在命令行执行以下命令（**逐条复制执行**）：

```bash
# 1. 确保系统支持 UTF-8
sudo apt update
sudo apt install -y locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

# 2. 添加 ROS 2 软件源
sudo apt install -y software-properties-common
sudo add-apt-repository universe -y
sudo apt update
sudo apt install -y curl
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

# 3. 更新包列表
sudo apt update

# 4. 安装 ROS 2 Humble 基础版（最轻量，适合命令行）
sudo apt install -y ros-humble-ros-base

# 5. 安装开发工具
sudo apt install -y python3-rosdep python3-colcon-common-extensions
sudo rosdep init
rosdep update

# 6. 将 ROS 2 环境变量写入 bashrc
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc

# 7. 安装 colcon 编译工具的自动补全（可选，方便上课用）
echo "source /usr/share/colcon_cd/function/colcon_cd.sh" >> ~/.bashrc
echo "export _colcon_cd_root=/opt/ros/humble/" >> ~/.bashrc
source ~/.bashrc
```

#### 如何从 Windows 远程看 ROS 2 图形界面？

ROS 2 有些工具（如 rviz2 图形仿真）需要显示窗口。既然 Ubuntu 是命令行的，我们可以把图形“转发”到 Windows 上显示。

**方案：MobaXterm（免费，功能强大）**

1. 在 Windows 上下载 MobaXterm Home Edition：https://mobaxterm.mobatek.net/
2. 打开 MobaXterm，点击 Session → SSH
3. Remote host：虚拟机的 IP 地址（在虚拟机里用 `ip a` 查看）
4. 勾选 **X11-Forwarding**（关键步骤！）
5. 输入用户名密码登录

现在，在 MobaXterm 终端里运行 ROS 2 图形程序，窗口会自动弹出在 Windows 上。

#### 第一个例子：让两个节点说话
你说得对！第一讲的小乌龟例子我需要用 ROS 2 的方式重写。ROS 2 的小乌龟包叫 `turtlesim`，用法和 ROS 1 一样，但启动命令略有不同。我现在把第一讲的小乌龟部分补全，替换掉之前的 talker/listener demo。

---

## 第一讲补充：用 ROS 2 跑小乌龟

### 安装小乌龟包

```bash
sudo apt install -y ros-humble-turtlesim
```

### 启动小乌龟

打开**三个终端**（在 MobaXterm 里按 Ctrl+Shift+N 新建标签页）。

**终端1**：启动小乌龟模拟器节点

```bash
ros2 run turtlesim turtlesim_node
```

会弹出一个蓝色背景、中间有只小乌龟的窗口。

**终端2**：启动键盘控制节点

```bash
ros2 run turtlesim turtle_teleop_key
```

把光标放在这个终端里，按键盘的**方向键（↑↓←→）**，小乌龟就会移动。

**终端3**：观察话题数据（可选，验证通信）

```bash
ros2 topic echo /turtle1/pose
```

你会看到小乌龟的实时位置（x, y, theta）不断刷新。

---

### 和 ROS 1 的区别

| | ROS 1 | ROS 2 |
|------|-------|-------|
| 启动命令 | `rosrun turtlesim turtlesim_node` | `ros2 run turtlesim turtlesim_node` |
| 不需要 roscore | 必须先开 `roscore` | **不需要**，节点自动发现 |
| 话题名 | `/turtle1/cmd_vel` | `/turtle1/cmd_vel`（一样） |

---

### 让学生探索的几个小实验

```bash
# 1. 查看话题类型
ros2 topic type /turtle1/cmd_vel
# 输出：geometry_msgs/msg/Twist

# 2. 查看话题信息（几个发布者、几个订阅者）
ros2 topic info /turtle1/cmd_vel

# 3. 手动发命令让小乌龟原地转圈
ros2 topic pub /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0}, angular: {z: 1.5}}" -r 10

# 4. 查看节点列表
ros2 node list
# 应该看到 /turtlesim 和 /teleop_turtle

# 5. 查看节点的详细信息
ros2 node info /turtlesim
```

---

### 调整小乌龟的颜色（演示参数）

```bash
# 查看小乌龟节点有哪些参数
ros2 param list /turtlesim

# 修改背景颜色为红色
ros2 param set /turtlesim background_r 255
ros2 param set /turtlesim background_g 100
ros2 param set /turtlesim background_b 100
```

颜色立即生效，不用重启节点——这就是参数的妙用。
ROS 2 中，我们不用 `roscore` 了，节点之间直接通信。

**终端1**：启动一个“说者”节点（发布者）

```bash
# 这个节点会持续发布消息到话题 /chatter
# 先安装 demo 包
sudo apt install -y ros-humble-demo-nodes-cpp
# 运行发布者（C++ 实现）
ros2 run demo_nodes_cpp talker
```

**终端2**：启动一个“听者”节点（订阅者）

```bash
# 这个节点会订阅 /chatter 话题，接收消息
ros2 run demo_nodes_cpp listener
```

你会看到 listener 终端不断打印出 "I heard: Hello World: xxx"。

### 下课思考
- 为什么关掉 talker 终端，listener 就不收到新消息了？（因为说话的“程序”没了）
- ROS 2 不需要 `roscore`，这意味着什么？（分布式，没有单点故障）

---

## 第二讲：ROS 2 的核心——节点与话题

### 本讲道理
ROS 2 世界里，每个独立运行的程序叫**节点（Node）**，节点之间通过**话题（Topic）**传递消息。

### 示意图
```
[节点A]  --发布-->  话题 /chatter   --订阅-->  [节点B]
(发布者)            (消息中转站)               (订阅者)
```

### 通俗讲解
就像 B 站直播：主播是**发布者**，直播间是**话题**，观众是**订阅者**。

ROS 1 需要 `roscore` 做中转，ROS 2 基于 DDS（数据分发服务），节点之间直连，不需要中心节点。DDS 就像一个自动发现机制：发布者说“我要发消息到话题 X”，订阅者说“我要听话题 X”，DDS 自动帮它们配对，直接通信。

### 本节课例子：自己写两个 Python 节点——一个发消息，一个收消息

#### 步骤1：创建工作空间

```bash
# ROS 2 的工作空间结构稍有不同
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
# ROS 2 用 colcon 编译，不是 catkin_make
colcon build --symlink-install
echo "source ~/ros2_ws/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

**解释**：
- `colcon build`：ROS 2 的编译命令，替代了 ROS 1 的 `catkin_make`
- `--symlink-install`：用符号链接安装，修改 Python 脚本后不用重新编译

#### 步骤2：创建功能包

```bash
cd ~/ros2_ws/src
# ROS 2 创建包的命令和 ROS 1 区别很大
ros2 pkg create my_first_pkg --build-type ament_python --dependencies rclpy std_msgs
cd ~/ros2_ws
colcon build --symlink-install
```

**解释**：
- `ros2 pkg create`：创建新包
- `--build-type ament_python`：指定用 Python 构建
- `--dependencies rclpy std_msgs`：依赖 `rclpy`（ROS 2 的 Python 客户端库）和 `std_msgs`（标准消息类型）

#### 步骤3：编写发布者节点（talker.py）

```bash
cd ~/ros2_ws/src/my_first_pkg/my_first_pkg
# 注意：ROS 2 的 Python 节点放在包名的子目录里
nano talker.py
```

粘贴以下代码（**注意缩进**）：

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
ROS 2 发布者节点：持续发布消息到 /chatter 话题
"""

import rclpy                           # ROS 2 的 Python 客户端库
from rclpy.node import Node            # 节点基类
from std_msgs.msg import String        # 字符串消息类型


class Talker(Node):
    """
    发布者节点类，继承自 Node
    """
    def __init__(self):
        """
        构造函数：初始化节点
        """
        # 调用父类构造函数，给节点起名字 'talker'
        super().__init__('talker')
        
        # 创建发布者
        # 参数1：消息类型（String）
        # 参数2：话题名称（'chatter'）
        # 参数3：队列长度（10），表示最多缓存10条消息
        self.publisher = self.create_publisher(String, 'chatter', 10)
        
        # 创建定时器，每 0.5 秒调用一次 timer_callback 函数
        # 0.5 秒 = 2Hz 发布频率
        self.timer = self.create_timer(0.5, self.timer_callback)
        
        # 计数器，用于区分每条消息
        self.count = 0
        
        # 打印日志，表示节点已启动
        self.get_logger().info('发布者节点已启动')
    
    def timer_callback(self):
        """
        定时器回调函数：每次被调用时发布一条消息
        """
        # 创建一条 String 类型的消息
        msg = String()
        # data 字段是消息的实际内容
        msg.data = '你好 ROS 2! 第 %d 次打招呼' % self.count
        
        # 发布消息到话题
        self.publisher.publish(msg)
        
        # 打印日志
        self.get_logger().info('发布: "%s"' % msg.data)
        
        # 计数器加 1
        self.count += 1


def main(args=None):
    """
    主函数：ROS 2 节点的入口
    """
    # 初始化 ROS 2
    rclpy.init(args=args)
    
    # 创建节点实例
    talker = Talker()
    
    try:
        # spin() 让节点保持运行，不断执行回调
        # 相当于一个无限循环，直到按 Ctrl+C
        rclpy.spin(talker)
    except KeyboardInterrupt:
        # 用户按 Ctrl+C
        pass
    finally:
        # 销毁节点
        talker.destroy_node()
        # 关闭 ROS 2
        rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤4：编写订阅者节点（listener.py）

```bash
nano listener.py
```

粘贴以下代码：

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
ROS 2 订阅者节点：订阅 /chatter 话题，接收消息
"""

import rclpy
from rclpy.node import Node
from std_msgs.msg import String


class Listener(Node):
    """
    订阅者节点类，继承自 Node
    """
    def __init__(self):
        """
        构造函数：初始化节点
        """
        # 调用父类构造函数，给节点起名字 'listener'
        super().__init__('listener')
        
        # 创建订阅者
        # 参数1：消息类型（String）
        # 参数2：话题名称（'chatter'）
        # 参数3：收到消息时调用的回调函数
        # 参数4：队列长度（10）
        self.subscription = self.create_subscription(
            String, 'chatter', self.listener_callback, 10
        )
        
        # 打印日志
        self.get_logger().info('订阅者节点已启动，等待消息...')
    
    def listener_callback(self, msg):
        """
        收到消息时的回调函数
        参数 msg：接收到的消息对象，msg.data 是消息内容
        """
        self.get_logger().info('收到: "%s"' % msg.data)


def main(args=None):
    """
    主函数
    """
    # 初始化 ROS 2
    rclpy.init(args=args)
    
    # 创建节点实例
    listener = Listener()
    
    try:
        # spin() 让节点保持运行
        rclpy.spin(listener)
    except KeyboardInterrupt:
        pass
    finally:
        listener.destroy_node()
        rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤5：修改 setup.py 注册节点

```bash
cd ~/ros2_ws/src/my_first_pkg
nano setup.py
```

找到 `entry_points` 部分，修改为：

```python
entry_points={
    'console_scripts': [
        'talker = my_first_pkg.talker:main',
        'listener = my_first_pkg.listener:main',
    ],
},
```

**解释**：
- `entry_points` 是 Python 包的入口点配置
- `'talker = my_first_pkg.talker:main'` 意思是：命令行执行 `talker` 时，运行 `my_first_pkg.talker` 模块的 `main` 函数
- 这样我们就可以用 `ros2 run my_first_pkg talker` 来启动节点了

#### 步骤6：编译并运行

```bash
cd ~/ros2_ws
# 编译
colcon build --symlink-install

# 刷新环境变量
source ~/.bashrc

# 终端1：运行发布者
ros2 run my_first_pkg talker

# 终端2：运行订阅者
ros2 run my_first_pkg listener
```

#### 步骤7：一些有用的调试命令

```bash
# 查看所有运行中的节点
ros2 node list

# 查看某个节点的详细信息
ros2 node info /talker

# 查看所有话题
ros2 topic list

# 查看某个话题上流动的数据
ros2 topic echo /chatter

# 查看话题的信息（消息类型、发布者数量等）
ros2 topic info /chatter

# 手动发布一条消息到话题
ros2 topic pub /chatter std_msgs/msg/String "data: '手动发的消息'"
```

### 下课思考
- 关掉 talker，listener 不会崩溃，只是不收到新消息——这叫**松耦合**
- ROS 2 没有 roscore，节点怎么找到彼此？（通过 DDS 的自动发现机制）

---

## 第三讲：服务——一问一答的通信方式

### 本讲道理
话题是“广播”，**服务（Service）** 是“问答”——客户端发请求，服务端返回响应。

### 示意图
```
[客户端]  --请求-->  服务 /add_two_ints  --返回-->  [客户端]
 “1+2=？”           (服务端计算)                  “=3”
```

### 通俗讲解
- **话题**：像收音机广播，谁爱听谁听，持续不断
- **服务**：像打电话，必须拨号（请求）→ 对方接听并回答（响应）→ 挂断
- **动作**：像叫外卖，下单 → 商家接单 → 骑手配送中 → 送达

服务适用于需要**立即得到结果**的操作，比如“查询传感器状态”、“让机械臂移到指定位置”。

### 本节课例子：写一个加法计算服务

#### 步骤1：创建功能包

```bash
cd ~/ros2_ws/src
ros2 pkg create my_service_pkg --build-type ament_python --dependencies rclpy example_interfaces
cd ~/ros2_ws
colcon build --symlink-install
```

**解释**：
- `example_interfaces`：ROS 2 提供的示例接口包，包含 `AddTwoInts` 服务定义，我们就用这个现成的，省去自定义服务文件的步骤

#### 步骤2：编写服务端（server.py）

```bash
cd ~/ros2_ws/src/my_service_pkg/my_service_pkg
nano server.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
ROS 2 服务端：提供加法计算服务
"""

import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts   # 导入服务类型


class AddTwoIntsServer(Node):
    """
    服务端节点类
    """
    def __init__(self):
        super().__init__('add_two_ints_server')
        
        # 创建服务
        # 参数1：服务类型（AddTwoInts）
        # 参数2：服务名称（'add_two_ints'）
        # 参数3：收到请求时调用的回调函数
        self.srv = self.create_service(AddTwoInts, 'add_two_ints', self.handle_add_two_ints)
        
        self.get_logger().info('加法服务已就绪，等待请求...')
    
    def handle_add_two_ints(self, request, response):
        """
        服务回调函数
        参数 request：客户端的请求，包含 a 和 b 两个字段
        参数 response：要返回的响应，包含 sum 字段
        """
        # 计算两数之和
        response.sum = request.a + request.b
        
        # 打印日志
        self.get_logger().info('收到请求: %d + %d = %d' % (request.a, request.b, response.sum))
        
        # 返回响应
        return response


def main(args=None):
    rclpy.init(args=args)
    server = AddTwoIntsServer()
    rclpy.spin(server)
    server.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤3：编写客户端（client.py）

```bash
nano client.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
ROS 2 客户端：调用加法服务
"""

import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts
import sys


class AddTwoIntsClient(Node):
    """
    客户端节点类
    """
    def __init__(self):
        super().__init__('add_two_ints_client')
        
        # 创建客户端
        # 参数1：服务类型
        # 参数2：服务名称
        self.client = self.create_client(AddTwoInts, 'add_two_ints')
        
        # 等待服务端上线（最多等5秒）
        if not self.client.wait_for_service(timeout_sec=5.0):
            self.get_logger().error('服务不可用，请先启动服务端')
            raise RuntimeError('服务不可用')
    
    def send_request(self, a, b):
        """
        发送请求到服务端
        参数 a, b：要相加的两个整数
        """
        # 创建请求对象
        request = AddTwoInts.Request()
        request.a = a
        request.b = b
        
        # 异步发送请求
        # call_async() 返回一个 Future 对象
        future = self.client.call_async(request)
        
        # 等待结果（rclpy.spin_until_future_complete 会阻塞直到完成）
        rclpy.spin_until_future_complete(self, future)
        
        # 获取响应
        response = future.result()
        return response


def main(args=None):
    rclpy.init(args=args)
    client = AddTwoIntsClient()
    
    # 从命令行参数读取要相加的数
    a = int(sys.argv[1]) if len(sys.argv) > 1 else 5
    b = int(sys.argv[2]) if len(sys.argv) > 2 else 3
    
    # 发送请求
    response = client.send_request(a, b)
    
    # 打印结果
    client.get_logger().info('%d + %d = %d' % (a, b, response.sum))
    
    client.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤4：修改 setup.py

```bash
cd ~/ros2_ws/src/my_service_pkg
nano setup.py
```

找到 `entry_points` 部分：

```python
entry_points={
    'console_scripts': [
        'add_two_ints_server = my_service_pkg.server:main',
        'add_two_ints_client = my_service_pkg.client:main',
    ],
},
```

#### 步骤5：编译并运行

```bash
cd ~/ros2_ws
colcon build --symlink-install
source ~/.bashrc

# 终端1：启动服务端
ros2 run my_service_pkg add_two_ints_server

# 终端2：运行客户端（5+3）
ros2 run my_service_pkg add_two_ints_client 5 3

# 运行客户端（100+200）
ros2 run my_service_pkg add_two_ints_client 100 200
```

#### 步骤6：命令行调试服务

```bash
# 查看所有服务
ros2 service list

# 查看服务类型
ros2 service type /add_two_ints

# 手动调用服务
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 10, b: 20}"
```

---

## 第四讲：动作——带反馈的长时间任务

### 本讲道理
服务是“一次问答”，**动作（Action）** 是“任务 + 中间汇报进度 + 最终结果 + 可随时取消”。

### 示意图
```
[客户端]  发送目标 →  [服务端执行]  ← 定期反馈进度 →  [客户端显示]
         ← 最终结果 ─
```

### 通俗讲解
- **话题** = 收音机广播（持续发）
- **服务** = 打电话问时间（一问一答）
- **动作** = 叫外卖（下单 → 商家接单 → 骑手取货 → 配送中 → 送达，每个环节都能看到）

动作适用于：导航、机械臂运动、长时间任务等需要知道进度和可随时取消的场景。

### 本节课例子：实现一个“进度条”动作

#### 步骤1：创建功能包

```bash
cd ~/ros2_ws/src
ros2 pkg create my_action_pkg --build-type ament_python --dependencies rclpy action_msgs example_interfaces
cd ~/ros2_ws
colcon build --symlink-install
```

#### 步骤2：编写动作服务端

```bash
cd ~/ros2_ws/src/my_action_pkg/my_action_pkg
nano action_server.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
ROS 2 动作服务端：模拟一个带进度的任务
"""

import rclpy
from rclpy.node import Node
from rclpy.action import ActionServer, GoalResponse, CancelResponse
from example_interfaces.action import Fibonacci    # 用斐波那契数列做例子
import time


class FibonacciServer(Node):
    """
    斐波那契数列的动作服务端
    收到目标 order（要计算的项数）后，逐步计算并反馈进度
    """
    def __init__(self):
        super().__init__('fibonacci_server')
        
        # 创建动作服务器
        # 参数1：节点引用
        # 参数2：动作类型
        # 参数3：动作名称
        # 参数4：收到目标时的回调
        self.action_server = ActionServer(
            self,
            Fibonacci,
            'compute_fibonacci',
            execute_callback=self.execute_callback,
            goal_callback=self.goal_callback,
            cancel_callback=self.cancel_callback
        )
        
        self.get_logger().info('斐波那契动作服务端已启动')
    
    def goal_callback(self, goal_request):
        """
        收到新目标时调用，可以在这里决定接受还是拒绝
        goal_request.order 是要计算的项数
        """
        self.get_logger().info('收到目标: 计算 %d 项' % goal_request.order)
        
        # 检查目标是否合理
        if goal_request.order <= 0:
            self.get_logger().warn('拒绝目标: 项数必须大于0')
            return GoalResponse.REJECT
        
        return GoalResponse.ACCEPT
    
    def cancel_callback(self, goal_handle):
        """
        收到取消请求时调用
        """
        self.get_logger().info('收到取消请求')
        return CancelResponse.ACCEPT
    
    def execute_callback(self, goal_handle):
        """
        执行任务的回调函数
        这是动作的核心，在这里完成实际工作
        goal_handle 提供了任务的目标、反馈和结果接口
        """
        # 获取目标参数
        order = goal_handle.request.order
        
        # 创建反馈消息
        feedback_msg = Fibonacci.Feedback()
        feedback_msg.sequence = []
        
        # 计算斐波那契数列
        a, b = 0, 1
        
        for i in range(order):
            # 检查是否被取消
            if goal_handle.is_cancel_requested:
                self.get_logger().info('任务被取消')
                goal_handle.canceled()
                return Fibonacci.Result()
            
            # 计算下一个斐波那契数
            feedback_msg.sequence.append(a)
            a, b = b, a + b
            
            # 发布反馈（当前进度）
            goal_handle.publish_feedback(feedback_msg)
            self.get_logger().info('进度: %d/%d, 当前值: %d' % (i+1, order, feedback_msg.sequence[-1]))
            
            # 模拟计算耗时（每步耗时1秒）
            time.sleep(1.0)
        
        # 任务完成，返回结果
        goal_handle.succeed()
        
        # 创建结果消息
        result = Fibonacci.Result()
        result.sequence = feedback_msg.sequence
        
        self.get_logger().info('任务完成，数列: %s' % str(result.sequence))
        return result


def main(args=None):
    rclpy.init(args=args)
    server = FibonacciServer()
    rclpy.spin(server)
    server.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤3：编写动作客户端

```bash
nano action_client.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
ROS 2 动作客户端：请求计算斐波那契数列
"""

import rclpy
from rclpy.node import Node
from rclpy.action import ActionClient
from example_interfaces.action import Fibonacci


class FibonacciClient(Node):
    """
    动作客户端
    """
    def __init__(self):
        super().__init__('fibonacci_client')
        
        # 创建动作客户端
        self.action_client = ActionClient(self, Fibonacci, 'compute_fibonacci')
        
        self.get_logger().info('等待动作服务器上线...')
        self.action_client.wait_for_server()
        self.get_logger().info('已连接到动作服务器')
    
    def send_goal(self, order):
        """
        发送目标到动作服务器
        参数 order：要计算的项数
        """
        # 创建目标消息
        goal_msg = Fibonacci.Goal()
        goal_msg.order = order
        
        # 发送目标
        # send_goal_async 返回一个 Future
        self.get_logger().info('发送目标: 计算 %d 项' % order)
        future = self.action_client.send_goal_async(
            goal_msg,
            feedback_callback=self.feedback_callback
        )
        
        # 等待目标被接受或拒绝
        rclpy.spin_until_future_complete(self, future)
        goal_handle = future.result()
        
        if not goal_handle.accepted:
            self.get_logger().error('目标被拒绝')
            return
        
        self.get_logger().info('目标已被接受，开始执行...')
        
        # 等待最终结果
        result_future = goal_handle.get_result_async()
        rclpy.spin_until_future_complete(self, result_future)
        result = result_future.result()
        
        self.get_logger().info('最终结果: %s' % str(result.result.sequence))
    
    def feedback_callback(self, feedback_msg):
        """
        收到反馈时的回调
        """
        self.get_logger().info('反馈: %s' % str(feedback_msg.feedback.sequence))


def main(args=None):
    rclpy.init(args=args)
    client = FibonacciClient()
    # 计算 5 项
    client.send_goal(5)
    client.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤4：修改 setup.py

```python
entry_points={
    'console_scripts': [
        'fibonacci_server = my_action_pkg.action_server:main',
        'fibonacci_client = my_action_pkg.action_client:main',
    ],
},
```

#### 步骤5：编译并运行

```bash
cd ~/ros2_ws
colcon build --symlink-install

# 终端1：启动服务端
ros2 run my_action_pkg fibonacci_server

# 终端2：运行客户端
ros2 run my_action_pkg fibonacci_client
```

#### 步骤6：命令行调试动作

```bash
# 查看所有动作
ros2 action list

# 查看动作详情
ros2 action info /compute_fibonacci

# 手动发送动作目标
ros2 action send_goal /compute_fibonacci example_interfaces/action/Fibonacci "{order: 5}" --feedback
```

---

## 第五讲：参数与启动文件

### 本讲道理
**参数（Parameter）** 是节点的配置信息，**启动文件（Launch File）** 是一键启动多个节点的脚本。

### 示意图
```
[节点A]                [launch 文件]
  参数:                   同时启动:
  - speed=1.5            - 节点A（带参数）
  - name="robot1"        - 节点B
                         - 节点C
```

### 通俗讲解
- **参数**：就像手机的设置菜单——亮度、音量、WiFi 密码。每个 ROS 2 节点都可以有自己的参数，运行中还能修改。
- **Launch 文件**：ROS 1 用 XML 格式，ROS 2 支持 Python 脚本写 launch 文件，更灵活、更强大。

### 本节课例子：用 launch 文件启动多个节点并配置参数

#### 步骤1：创建功能包

```bash
cd ~/ros2_ws/src
ros2 pkg create my_launch_pkg --build-type ament_python --dependencies rclpy std_msgs
cd ~/ros2_ws
colcon build --symlink-install
```

#### 步骤2：创建一个带参数的节点

```bash
cd ~/ros2_ws/src/my_launch_pkg/my_launch_pkg
nano param_node.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
ROS 2 参数演示节点：读取和修改参数
"""

import rclpy
from rclpy.node import Node


class ParamNode(Node):
    """
    参数节点
    """
    def __init__(self):
        super().__init__('param_node')
        
        # 声明参数（declare 参数名, 默认值）
        # 这些参数可以在启动时设置，也可以在运行时修改
        self.declare_parameter('robot_name', 'my_robot')
        self.declare_parameter('max_speed', 1.0)
        self.declare_parameter('camera_on', True)
        
        # 创建定时器，每秒打印一次当前参数
        self.timer = self.create_timer(1.0, self.timer_callback)
        
        self.get_logger().info('参数节点已启动')
    
    def timer_callback(self):
        """
        定时读取并打印所有参数
        """
        # 读取参数
        robot_name = self.get_parameter('robot_name').value
        max_speed = self.get_parameter('max_speed').value
        camera_on = self.get_parameter('camera_on').value
        
        self.get_logger().info(
            '机器人[%s]: 最高速度=%.1f m/s, 摄像头=%s' % 
            (robot_name, max_speed, '开' if camera_on else '关')
        )


def main(args=None):
    rclpy.init(args=args)
    node = ParamNode()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤3：编写 Python 启动文件

```bash
cd ~/ros2_ws/src/my_launch_pkg
mkdir launch
cd launch
nano my_launch.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
ROS 2 Launch 文件：同时启动多个节点
"""

from launch import LaunchDescription
from launch_ros.actions import Node
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration


def generate_launch_description():
    """
    生成启动描述
    Launch 文件的核心就是这个函数
    """
    
    # 声明一个启动参数（可以在命令行传入）
    robot_name_arg = DeclareLaunchArgument(
        'robot_name',                    # 参数名
        default_value='turtle_bot',      # 默认值
        description='机器人的名字'
    )
    
    max_speed_arg = DeclareLaunchArgument(
        'max_speed',
        default_value='2.0',
        description='机器人的最高速度'
    )
    
    # 创建第一个节点：参数节点
    param_node = Node(
        package='my_launch_pkg',                # 包名
        executable='param_node',                # 可执行文件名（和 setup.py 里对应）
        name='param_node_1',                    # 节点名（可以自定义，覆盖代码里的名字）
        output='screen',                        # 输出到屏幕
        parameters=[                            # 设置参数
            {'robot_name': LaunchConfiguration('robot_name')},  # 使用启动参数
            {'max_speed': LaunchConfiguration('max_speed')},
            {'camera_on': True},
        ]
    )
    
    # 创建第二个节点：发布者（复用第二讲的）
    # 如果还没编译，可以先注释掉
    # talker_node = Node(
    #     package='my_first_pkg',
    #     executable='talker',
    #     name='talker_1',
    #     output='screen',
    # )
    
    # 创建第三个节点：订阅者
    # listener_node = Node(
    #     package='my_first_pkg',
    #     executable='listener',
    #     name='listener_1',
    #     output='screen',
    # )
    
    # 返回启动描述
    # 包含所有要启动的节点和参数
    return LaunchDescription([
        robot_name_arg,
        max_speed_arg,
        param_node,
        # talker_node,
        # listener_node,
    ])
```

**Launch 文件关键点解释**：
- `LaunchDescription`：启动描述，包含所有要启动的东西
- `DeclareLaunchArgument`：声明启动参数，可以在命令行传入
- `Node`：定义一个要启动的节点
- `parameters`：给节点设置参数
- `LaunchConfiguration`：引用启动参数的值

#### 步骤4：修改 setup.py 添加 launch 文件

```bash
cd ~/ros2_ws/src/my_launch_pkg
nano setup.py
```

在 `setup()` 函数中添加 `data_files`：

```python
from setuptools import setup
from glob import glob
import os

package_name = 'my_launch_pkg'

setup(
    name=package_name,
    version='0.0.0',
    packages=[package_name],
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
        # 添加 launch 文件
        (os.path.join('share', package_name, 'launch'), glob('launch/*.py')),
    ],
    # ...其余不变
    entry_points={
        'console_scripts': [
            'param_node = my_launch_pkg.param_node:main',
        ],
    },
)
```

#### 步骤5：编译并运行

```bash
cd ~/ros2_ws
colcon build --symlink-install
source ~/.bashrc

# 用 launch 文件启动（不带参数，用默认值）
ros2 launch my_launch_pkg my_launch.py

# 用 launch 文件启动（带参数）
ros2 launch my_launch_pkg my_launch.py robot_name:=super_bot max_speed:=3.5
```

#### 步骤6：运行时操作参数

```bash
# 查看某个节点的参数列表
ros2 param list /param_node_1

# 获取某个参数的值
ros2 param get /param_node_1 robot_name

# 设置参数（运行时修改）
ros2 param set /param_node_1 max_speed 5.0

# 保存当前参数到文件
ros2 param dump /param_node_1 > params.yaml
```

---

## 第六讲：TF2 坐标变换——机器人身体的骨架

### 本讲道理
机器人有多个部件（底盘、激光雷达、机械臂），**TF2** 告诉我们它们之间的相对位置关系。

### 示意图
```
世界坐标系(map)
       │
       └── 机器人基座坐标系(base_link)
                │
                ├── 激光雷达坐标系(laser)
                │      (位于base_link前方0.2m)
                │
                └── 摄像头坐标系(camera)
                       (位于base_link顶部0.3m)
```

### 通俗讲解
TF2 是机器人的“户口本”——记录了每个部件在哪个位置、朝哪个方向。

坐标系变换的核心思路：在激光雷达看来“前方1米有障碍物”，要把这个信息告诉底盘，就需要 TF2 来换算坐标——把障碍物在激光雷达坐标系里的位置，转换成在世界坐标系里的位置。

### 本节课例子：发布静态和动态 TF2 变换

#### 步骤1：安装 TF2 相关包

```bash
sudo apt install -y ros-humble-tf2-tools ros-humble-tf-transformations
```

#### 步骤2：创建功能包

```bash
cd ~/ros2_ws/src
ros2 pkg create my_tf_pkg --build-type ament_python --dependencies rclpy tf2_ros geometry_msgs tf_transformations
cd ~/ros2_ws
colcon build --symlink-install
```

#### 步骤3：编写静态 TF2 广播者

```bash
cd ~/ros2_ws/src/my_tf_pkg/my_tf_pkg
nano static_tf_broadcaster.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
静态 TF2 广播者：发布世界坐标系到机器人基座的固定变换
"""

import rclpy
from rclpy.node import Node
from geometry_msgs.msg import TransformStamped   # 变换消息
from tf2_ros import StaticTransformBroadcaster   # 静态 TF2 广播者
import tf_transformations                        # 四元数等数学工具


class StaticTFBroadcaster(Node):
    """
    发布一个静态的 TF2 变换：map → base_link
    """
    def __init__(self):
        super().__init__('static_tf_broadcaster')
        
        # 创建静态 TF2 广播者
        self.broadcaster = StaticTransformBroadcaster(self)
        
        # 发布一次静态变换
        self.publish_static_transform()
        
        self.get_logger().info('静态 TF2 已发布')
    
    def publish_static_transform(self):
        """
        发布一个静态变换
        """
        # 创建 TransformStamped 消息
        t = TransformStamped()
        
        # 时间戳
        t.header.stamp = self.get_clock().now().to_msg()
        
        # 父坐标系（参考坐标系）
        t.header.frame_id = 'map'
        
        # 子坐标系（被描述的坐标系）
        t.child_frame_id = 'base_link'
        
        # 平移：机器人在世界坐标系的 (2.0, 1.0, 0.0) 位置
        t.transform.translation.x = 2.0
        t.transform.translation.y = 1.0
        t.transform.translation.z = 0.0
        
        # 旋转：绕 Z 轴旋转 45 度
        # euler_from_quaternion 的逆操作：用欧拉角生成四元数
        quat = tf_transformations.quaternion_from_euler(0.0, 0.0, 0.785)  # 0.785弧度 = 45度
        t.transform.rotation.x = quat[0]
        t.transform.rotation.y = quat[1]
        t.transform.rotation.z = quat[2]
        t.transform.rotation.w = quat[3]
        
        # 广播变换
        self.broadcaster.sendTransform(t)


def main(args=None):
    rclpy.init(args=args)
    node = StaticTFBroadcaster()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤4：编写动态 TF2 广播者（模拟转动的激光雷达）

```bash
nano dynamic_tf_broadcaster.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
动态 TF2 广播者：模拟激光雷达绕 Z 轴旋转
"""

import rclpy
from rclpy.node import Node
from geometry_msgs.msg import TransformStamped
from tf2_ros import TransformBroadcaster   # 动态 TF2 广播者
import tf_transformations
import math


class DynamicTFBroadcaster(Node):
    """
    动态发布 base_link → laser 的变换（激光雷达转动）
    """
    def __init__(self):
        super().__init__('dynamic_tf_broadcaster')
        
        # 创建动态 TF2 广播者
        self.broadcaster = TransformBroadcaster(self)
        
        # 激光雷达在 base_link 前方 0.2 米
        self.laser_offset_x = 0.2
        
        # 旋转角度变量
        self.angle = 0.0
        
        # 创建定时器，每 0.1 秒发布一次（10Hz）
        self.timer = self.create_timer(0.1, self.timer_callback)
        
        self.get_logger().info('动态 TF2 广播者已启动')
    
    def timer_callback(self):
        """
        定时发布变换
        """
        t = TransformStamped()
        
        t.header.stamp = self.get_clock().now().to_msg()
        t.header.frame_id = 'base_link'
        t.child_frame_id = 'laser'
        
        # 激光雷达的位置
        t.transform.translation.x = self.laser_offset_x
        t.transform.translation.y = 0.0
        t.transform.translation.z = 0.1
        
        # 激光雷达绕 Z 轴旋转（模拟扫描）
        self.angle += 0.1  # 每次累加 0.1 弧度
        
        # 欧拉角转四元数
        quat = tf_transformations.quaternion_from_euler(0.0, 0.0, self.angle)
        t.transform.rotation.x = quat[0]
        t.transform.rotation.y = quat[1]
        t.transform.rotation.z = quat[2]
        t.transform.rotation.w = quat[3]
        
        # 广播
        self.broadcaster.sendTransform(t)


def main(args=None):
    rclpy.init(args=args)
    node = DynamicTFBroadcaster()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤5：编写 TF2 监听者（坐标转换）

```bash
nano tf_listener.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
TF2 监听者：将激光雷达坐标系中的点转换到世界坐标系
"""

import rclpy
from rclpy.node import Node
from tf2_ros import Buffer, TransformListener
from geometry_msgs.msg import PointStamped


class TFListener(Node):
    """
    TF2 监听者：监听 TF2 变换并进行坐标转换
    """
    def __init__(self):
        super().__init__('tf_listener')
        
        # 创建 TF2 缓存和监听者
        # Buffer 缓存收到的变换信息
        self.tf_buffer = Buffer()
        # TransformListener 订阅 /tf 和 /tf_static 话题
        self.tf_listener = TransformListener(self.tf_buffer, self)
        
        # 创建定时器，每秒执行一次坐标转换
        self.timer = self.create_timer(1.0, self.timer_callback)
        
        self.get_logger().info('TF2 监听者已启动')
    
    def timer_callback(self):
        """
        定时执行坐标转换
        """
        # 创建一个点：在激光雷达坐标系中，正前方 1 米处
        point_in_laser = PointStamped()
        point_in_laser.header.stamp = self.get_clock().now().to_msg()
        point_in_laser.header.frame_id = 'laser'
        point_in_laser.point.x = 1.0
        point_in_laser.point.y = 0.0
        point_in_laser.point.z = 0.0
        
        try:
            # 将点从 laser 坐标系转换到 map 坐标系
            # 参数1：要转换的点
            # 参数2：目标坐标系
            # 参数3：超时时间
            point_in_map = self.tf_buffer.transform(point_in_laser, 'map', rclpy.time.Duration(seconds=1.0))
            
            self.get_logger().info(
                'laser 前方 1m 处，在世界坐标中: (%.2f, %.2f, %.2f)' % 
                (point_in_map.point.x, point_in_map.point.y, point_in_map.point.z)
            )
        except Exception as e:
            self.get_logger().warn('TF 转换失败: %s' % str(e))


def main(args=None):
    rclpy.init(args=args)
    node = TFListener()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 步骤6：修改 setup.py

```python
entry_points={
    'console_scripts': [
        'static_tf = my_tf_pkg.static_tf_broadcaster:main',
        'dynamic_tf = my_tf_pkg.dynamic_tf_broadcaster:main',
        'tf_listener = my_tf_pkg.tf_listener:main',
    ],
},
```

#### 步骤7：编译和运行

```bash
cd ~/ros2_ws
colcon build --symlink-install
source ~/.bashrc

# 终端1：静态
ros2 run my_tf_pkg static_tf

# 终端2：动态
ros2 run my_tf_pkg dynamic_tf

# 终端3：监听者
ros2 run my_tf_pkg tf_listener
```

#### 步骤8：命令行工具查看 TF2

```bash
# 查看当前 TF2 树
ros2 run tf2_tools view_frames

# 生成 frames.pdf 文件，下载到 Windows 查看
# 实时查看某个变换
ros2 run tf2_ros tf2_echo map base_link
```

---

## 第七讲：Gazebo 仿真入门——给机器人一个虚拟世界

### 本讲道理
**Gazebo** 是机器人的“模拟器”——物理引擎 + 传感器仿真 + 3D 环境。

### 示意图
```
[ROS 2 控制程序]  ←→  [Gazebo 仿真世界]
        发送速度指令        物理计算、碰撞检测
        读取传感器数据       生成摄像头/激光数据
```

### 通俗讲解
如果没有 Gazebo，你的机器人程序只能对着空气发指令，不知道对错。

Gazebo 就像一个逼真的游戏引擎：有重力、有碰撞、有光影。你写的控制程序发给 Gazebo 里的虚拟机器人，它能真实地移动、碰撞、感知环境。

### 本节课例子：在 Gazebo 中运行一个移动机器人

#### 步骤1：安装 Gazebo（ROS 2 Humble 默认用 Gazebo 11）

```bash
# 安装 Gazebo
sudo apt install -y ros-humble-gazebo-ros-pkgs

# 确保 Gazebo 本身已安装
sudo apt install -y gazebo11
```

#### 步骤2：安装 TurtleBot4 仿真包（ROS 2 版的 TurtleBot）

```bash
sudo apt install -y ros-humble-turtlebot4-desktop ros-humble-turtlebot4-simulator
```

#### 步骤3：启动 Gazebo 仿真

```bash
# 设置 TurtleBot4 需要的环境变量
export TURTLEBOT4_MODEL=standard

# 启动 Gazebo 带 TurtleBot4 的简单世界
ros2 launch turtlebot4_ignition_bringup turtlebot4_ignition.launch.py
```

**注意**：如果上面命令报错，说明包名可能有差异。可以用备选方案：

```bash
# 备选：直接用 Gazebo 经典版加 TurtleBot3
sudo apt install -y ros-humble-turtlebot3-gazebo
export TURTLEBOT3_MODEL=burger

ros2 launch turtlebot3_gazebo empty_world.launch.py
```

#### 步骤4：用键盘控制机器人

另开一个终端：
```bash
ros2 run turtlebot3_teleop teleop_keyboard
```

按键盘控制机器人在 Gazebo 中移动。

#### 步骤5：查看机器人话题

```bash
# 查看雷达扫描数据
ros2 topic echo /scan

# 查看摄像头图像
ros2 topic echo /camera/image_raw

# 发布速度指令让机器人走圆形
ros2 topic pub /cmd_vel geometry_msgs/msg/Twist "{linear: {x: 0.1}, angular: {z: 0.5}}" -r 10
```

---

## 第八讲：URDF——用代码描述机器人

### 本讲道理
**URDF（Unified Robot Description Format）** 是机器人的“3D 说明书”——用 Xacro/XML 描述机器人的形状、关节、物理属性。

### 示意图
```
<robot name="my_robot">
    <link name="base">         <!-- 身体 -->
    <link name="wheel_left">   <!-- 左轮 -->
    <joint name="wheel_left_joint">  <!-- 连接身体和左轮的关节（转动） -->
</robot>
```

### 通俗讲解
如果 Gazebo 是舞台，URDF 就是演员的“定妆照”——定义了机器人长什么样、有几个关节、每个部件多重、什么颜色。

ROS 2 中的 `robot_state_publisher` 会读取 URDF，并发布 TF2 变换。

### 本节课例子：创建一个两轮差速机器人

#### 步骤1：创建功能包

```bash
cd ~/ros2_ws/src
ros2 pkg create my_robot_description --build-type ament_python --dependencies rclpy
cd ~/ros2_ws
colcon build --symlink-install
```

#### 步骤2：创建 URDF 文件（用 Xacro）

```bash
cd ~/ros2_ws/src/my_robot_description
mkdir urdf launch rviz
cd urdf
nano simple_robot.urdf.xacro
```

```xml
<?xml version="1.0"?>
<robot name="simple_robot" xmlns:xacro="http://www.ros.org/wiki/xacro">
    
    <!-- ====== 定义颜色 ====== -->
    <material name="blue">
        <color rgba="0.0 0.0 1.0 1.0"/>
    </material>
    <material name="black">
        <color rgba="0.0 0.0 0.0 1.0"/>
    </material>
    
    <!-- ====== 底盘 link（刚体部件） ====== -->
    <link name="base_link">
        <!-- 视觉：长什么样 -->
        <visual>
            <geometry>
                <!-- 长方体：长0.4m 宽0.2m 高0.1m -->
                <box size="0.4 0.2 0.1"/>
            </geometry>
            <material name="blue"/>
        </visual>
        
        <!-- 碰撞检测：和视觉一样的外形 -->
        <collision>
            <geometry>
                <box size="0.4 0.2 0.1"/>
            </geometry>
        </collision>
        
        <!-- 惯性：质量和转动惯量 -->
        <inertial>
            <mass value="1.0"/>  <!-- 1kg -->
            <inertia ixx="0.01" ixy="0.0" ixz="0.0" 
                     iyy="0.01" iyz="0.0" izz="0.01"/>
        </inertial>
    </link>
    
    <!-- ====== 左轮 link ====== -->
    <link name="wheel_left">
        <visual>
            <geometry>
                <!-- 圆柱：半径0.05m 长0.02m -->
                <cylinder radius="0.05" length="0.02"/>
            </geometry>
            <material name="black"/>
        </visual>
        <collision>
            <geometry>
                <cylinder radius="0.05" length="0.02"/>
            </geometry>
        </collision>
        <inertial>
            <mass value="0.1"/>
            <inertia ixx="0.0001" ixy="0.0" ixz="0.0" 
                     iyy="0.0001" iyz="0.0" izz="0.0001"/>
        </inertial>
    </link>
    
    <!-- ====== 左轮关节（连接底盘和左轮） ====== -->
    <joint name="wheel_left_joint" type="continuous">
        <parent link="base_link"/>
        <child link="wheel_left"/>
        <!-- 原点在底盘左侧 y=0.15 -->
        <!-- 绕 Y 轴转 90 度，让圆柱竖起来 -->
        <origin xyz="0.0 0.15 0.0" rpy="0.0 1.5708 0.0"/>
        <axis xyz="0.0 1.0 0.0"/>
    </joint>
    
    <!-- ====== 右轮 ====== -->
    <link name="wheel_right">
        <visual>
            <geometry>
                <cylinder radius="0.05" length="0.02"/>
            </geometry>
            <material name="black"/>
        </visual>
        <collision>
            <geometry>
                <cylinder radius="0.05" length="0.02"/>
            </geometry>
        </collision>
        <inertial>
            <mass value="0.1"/>
            <inertia ixx="0.0001" ixy="0.0" ixz="0.0" 
                     iyy="0.0001" iyz="0.0" izz="0.0001"/>
        </inertial>
    </link>
    
    <joint name="wheel_right_joint" type="continuous">
        <parent link="base_link"/>
        <child link="wheel_right"/>
        <origin xyz="0.0 -0.15 0.0" rpy="0.0 1.5708 0.0"/>
        <axis xyz="0.0 1.0 0.0"/>
    </joint>
    
</robot>
```

**Xacro 语法解释**：
- `<link>`：定义机器人的一个部件（刚体）
- `<joint>`：定义两个部件之间的连接关系
  - `type="continuous"`：连续旋转关节（轮子）
  - `type="fixed"`：固定关节（不动的连接）
  - `type="revolute"`：有限角度的旋转关节（机械臂关节）
- `origin xyz`：子部件相对于父部件的位置
- `origin rpy`：Roll-Pitch-Yaw 角度（弧度制）
- `<inertial>`：物理属性，Gazebo 仿真时用到

#### 步骤3：创建 launch 文件显示 URDF

```bash
cd ~/ros2_ws/src/my_robot_description/launch
nano display_robot.launch.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
启动文件：加载 URDF 并在 RViz 中显示
"""

from launch import LaunchDescription
from launch_ros.actions import Node
from launch.substitutions import PathJoinSubstitution
from launch_ros.substitutions import FindPackageShare


def generate_launch_description():
    """
    加载 URDF，启动 robot_state_publisher 和 joint_state_publisher_gui
    """
    
    # 找到 URDF 文件的路径
    urdf_file = PathJoinSubstitution([
        FindPackageShare('my_robot_description'),
        'urdf',
        'simple_robot.urdf.xacro'
    ])
    
    # robot_state_publisher：读取 URDF 并发布 TF
    # 这是 ROS 2 自带的标准节点
    robot_state_pub = Node(
        package='robot_state_publisher',
        executable='robot_state_publisher',
        name='robot_state_publisher',
        parameters=[{'robot_description': urdf_file}]
    )
    
    # joint_state_publisher_gui：可以拖动滑条控制关节角度
    # 需要先安装：sudo apt install ros-humble-joint-state-publisher-gui
    joint_state_pub = Node(
        package='joint_state_publisher_gui',
        executable='joint_state_publisher_gui',
        name='joint_state_publisher_gui'
    )
    
    # RViz 2：ROS 2 的可视化工具
    rviz = Node(
        package='rviz2',
        executable='rviz2',
        name='rviz2',
        output='screen'
    )
    
    return LaunchDescription([
        robot_state_pub,
        joint_state_pub,
        rviz,
    ])
```

#### 步骤4：修改 setup.py 添加文件

```bash
cd ~/ros2_ws/src/my_robot_description
nano setup.py
```

```python
from setuptools import setup
from glob import glob
import os

package_name = 'my_robot_description'

setup(
    name=package_name,
    version='0.0.0',
    packages=[package_name],
    data_files=[
        ('share/ament_index/resource_index/packages', ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
        # 添加 urdf 和 launch 文件
        (os.path.join('share', package_name, 'urdf'), glob('urdf/*.xacro')),
        (os.path.join('share', package_name, 'launch'), glob('launch/*.py')),
    ],
    install_requires=['setuptools'],
    zip_safe=True,
    entry_points={
        'console_scripts': [],
    },
)
```

#### 步骤5：安装必要工具并编译

```bash
sudo apt install -y ros-humble-joint-state-publisher-gui
cd ~/ros2_ws
colcon build --symlink-install
source ~/.bashrc
```

#### 步骤6：运行

```bash
ros2 launch my_robot_description display_robot.launch.py
```

在 RViz 2 中：
1. 点击 "Add" → 选择 "RobotModel"
2. 将 "Fixed Frame" 设为 "base_link"
3. 用 joint_state_publisher_gui 的滑条控制轮子转动

---

## 第九讲：MoveIt 2——机械臂运动规划

### 本讲道理
**MoveIt 2** 是机械臂的“大脑”——给定目标位置，自动计算出一条无碰撞的轨迹。

### 示意图
```
[目标位姿] → MoveIt 2 → [轨迹点序列] → 控制器 → 机械臂运动
              ↑
         碰撞检测
       (避开障碍物)
```

### 通俗讲解
MoveIt 2 像一个经验丰富的机械臂操作员：你告诉他“把夹爪放到那个位置”，他自动算出每个关节该转多少度，还能保证不撞到东西。

### 本节课例子：用 MoveIt 2 Python API 控制虚拟机械臂

#### 步骤1：安装 MoveIt 2

```bash
sudo apt install -y ros-humble-moveit
```

#### 步骤2：创建一个使用 MoveIt 2 的简单 Python 节点

```bash
cd ~/ros2_ws/src
ros2 pkg create my_moveit_demo --build-type ament_python --dependencies rclpy moveit_msgs
cd ~/ros2_ws
colcon build --symlink-install
```

```bash
cd ~/ros2_ws/src/my_moveit_demo/my_moveit_demo
nano moveit_python_api.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
MoveIt 2 Python API 演示：规划并执行机械臂运动
（这是一个概念演示，实际需要配合具体机械臂的 MoveIt 配置包使用）
"""

import rclpy
from rclpy.node import Node
from geometry_msgs.msg import PoseStamped, Quaternion, Point
import math


class MoveItDemo(Node):
    """
    MoveIt 2 演示节点
    展示规划一个运动轨迹的基本流程
    （实际使用时需要替换为具体机械臂的 MoveGroupInterface）
    """
    def __init__(self):
        super().__init__('moveit_demo')
        
        self.get_logger().info('MoveIt 2 演示节点已启动')
        self.get_logger().info('')
        self.get_logger().info('============================')
        self.get_logger().info('MoveIt 2 的核心概念:')
        self.get_logger().info('1. Planning Group: 运动规划组（如"手臂"、"夹爪"）')
        self.get_logger().info('2. Planning Scene: 规划场景（机器人 + 环境）')
        self.get_logger().info('3. Motion Plan: 运动规划（轨迹 + 时间）')
        self.get_logger().info('4. Execute: 执行（发送轨迹到控制器）')
        self.get_logger().info('============================')
        self.get_logger().info('')
        
        # 演示如何构造一个目标位姿
        self.demonstrate_pose_construction()
    
    def demonstrate_pose_construction(self):
        """
        演示如何构造目标位姿
        """
        # 创建一个目标位姿
        target_pose = PoseStamped()
        target_pose.header.frame_id = 'base_link'
        
        # 位置：x=0.3m, y=0.0m, z=0.5m
        target_pose.pose.position = Point(x=0.3, y=0.0, z=0.5)
        
        # 朝向：四元数 (x, y, z, w)
        # 绕 Z 轴旋转 45 度
        angle = math.radians(45)
        target_pose.pose.orientation = Quaternion(
            x=0.0,
            y=0.0,
            z=math.sin(angle / 2),
            w=math.cos(angle / 2)
        )
        
        self.get_logger().info('目标位姿示例:')
        self.get_logger().info('  位置: x=%.2f, y=%.2f, z=%.2f' % 
            (target_pose.pose.position.x, target_pose.pose.position.y, target_pose.pose.position.z))
        self.get_logger().info('  朝向: x=%.2f, y=%.2f, z=%.2f, w=%.2f' %
            (target_pose.pose.orientation.x, target_pose.pose.orientation.y,
             target_pose.pose.orientation.z, target_pose.pose.orientation.w))
        self.get_logger().info('')
        self.get_logger().info('实际使用时，你需要:')
        self.get_logger().info('1. 安装具体机械臂的 MoveIt 配置包')
        self.get_logger().info('2. 用 MoveGroupInterface 连接 planning group')
        self.get_logger().info('3. 设置位姿目标并调用 plan() 和 execute()')


def main(args=None):
    rclpy.init(args=args)
    node = MoveItDemo()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

#### 运行概念演示

```bash
ros2 run my_moveit_demo moveit_python_api
```

**对于真正的机械臂实操**，建议使用现成的 UR5 仿真：

```bash
# 安装 UR 机械臂的 ROS 2 驱动
sudo apt install -y ros-humble-ur

# 启动 UR5 的 MoveIt 2 仿真
ros2 launch ur_moveit_config ur_moveit.launch.py ur_type:=ur5
```

在 RViz 中拖动机械臂末端的箭头设置目标位姿，点击 "Plan & Execute"。

---

## 第十讲：ROS 2 Navigation——让机器人自己导航

### 本讲道理
**Navigation 2（Nav2）** 是移动机器人的“导航系统”——给定地图和目标点，自动规划路径并躲避障碍。

### 示意图
```
[全局规划器] → 全局路径
[局部规划器] → 速度指令 → 底盘
       ↑
[代价地图] (融合传感器数据)
```

### 通俗讲解
Nav2 像手机导航 App：
- **全局规划器** = 高德地图规划路线
- **局部规划器** = 开车时躲避突然出现的行人
- **代价地图** = 实时路况信息

### 本节课例子：在 Gazebo 中让 TurtleBot3 自主导航

#### 步骤1：安装 TurtleBot3 相关包

```bash
sudo apt install -y ros-humble-turtlebot3 ros-humble-turtlebot3-gazebo ros-humble-turtlebot3-navigation2
```

#### 步骤2：启动仿真和导航的完整 launch 文件

```bash
export TURTLEBOT3_MODEL=burger

# 启动 Gazebo
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
```

另开终端：
```bash
# 启动导航
ros2 launch turtlebot3_navigation2 navigation2.launch.py use_sim_time:=True map:=$HOME/map.yaml
```

如果还没有地图，先用 SLAM 建图：

```bash
# 终端1：Gazebo
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py

# 终端2：SLAM（同步定位与建图）
ros2 launch turtlebot3_cartographer cartographer.launch.py use_sim_time:=True

# 终端3：键盘控制机器人到处走
ros2 run turtlebot3_teleop teleop_keyboard

# 终端4：保存地图
ros2 run nav2_map_server map_saver_cli -f ~/map
```

#### 步骤3：理解 Nav2 的核心概念

```bash
# 查看导航相关的节点
ros2 node list | grep nav

# 查看发布的代价地图
ros2 topic echo /global_costmap/costmap
```

---

## 第十一讲：ROS 2 Bridge——让网页也能控制 ROS 2

### 本讲道理
**ROS 2 bridge/web** 是 ROS 2 和外部世界的“桥梁”——让浏览器也能收发 ROS 2 消息。

### 示意图
```
[网页前端] ←→ rosbridge_server(WebSocket) ←→ ROS 2 节点
 (JavaScript)          (端口9090)             (Python/C++)
```

### 通俗讲解
想在手机上监控机器人？rosbridge 把 ROS 2 消息转成 JSON，通过 WebSocket 发送，任何网页都能接收。

### 本节课例子：网页显示小乌龟位置

#### 步骤1：安装 rosbridge

```bash
sudo apt install -y ros-humble-rosbridge-server
```

#### 步骤2：安装并启动小乌龟

```bash
sudo apt install -y ros-humble-turtlesim
ros2 run turtlesim turtlesim_node
```

另开终端：
```bash
# 启动 rosbridge
ros2 launch rosbridge_server rosbridge_websocket_launch.xml
```

#### 步骤3：创建网页监控界面

在 Windows 上创建 `turtle_monitor.html`：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>小乌龟位置监控 - ROS 2</title>
    <script src="https://cdn.jsdelivr.net/npm/roslib@1/build/roslib.min.js"></script>
</head>
<body>
    <h1>小乌龟实时位置 (ROS 2)</h1>
    <p>X: <span id="x">0.0</span></p>
    <p>Y: <span id="y">0.0</span></p>
    <p>Theta: <span id="theta">0.0</span></p>
    
    <script>
        // 连接到 rosbridge（替换为你的虚拟机 IP）
        var ros = new ROSLIB.Ros({
            url: 'ws://192.168.56.101:9090'
        });
        
        ros.on('connection', function() {
            console.log('已连接到 ROS 2');
            document.body.style.backgroundColor = '#e8f5e9';
        });
        
        ros.on('error', function(error) {
            console.log('连接错误:', error);
            document.body.style.backgroundColor = '#ffebee';
        });
        
        ros.on('close', function() {
            console.log('连接已关闭');
            document.body.style.backgroundColor = '#eeeeee';
        });
        
        // 订阅小乌龟位姿话题
        var poseListener = new ROSLIB.Topic({
            ros: ros,
            name: '/turtle1/pose',
            messageType: 'turtlesim/msg/Pose'
        });
        
        poseListener.subscribe(function(message) {
            document.getElementById('x').textContent = message.x.toFixed(3);
            document.getElementById('y').textContent = message.y.toFixed(3);
            document.getElementById('theta').textContent = message.theta.toFixed(3);
        });
    </script>
</body>
</html>
```

用浏览器打开这个 HTML 文件，就能实时看到小乌龟位置。

---

## 第十二讲：Docker——一键部署 ROS 2 环境

### 本讲道理
**Docker** 是“集装箱”——把 ROS 2 环境和所有依赖打包，到处都能运行。

### 示意图
```
[你的代码] + [ROS 2 环境] → Docker 镜像 → 任何电脑都能跑
```

### 通俗讲解
“老师，我环境又崩了！”——用 Docker，这个问题永远消失。

### 本节课例子：创建 ROS 2 Humble Docker 镜像

#### 创建 Dockerfile

```bash
mkdir ~/ros2_docker
cd ~/ros2_docker
nano Dockerfile
```

```dockerfile
FROM osrf/ros:humble-desktop-full

# 安装额外工具
RUN apt-get update && apt-get install -y \
    python3-pip \
    ros-humble-turtlesim \
    && rm -rf /var/lib/apt/lists/*

# 创建工作空间
RUN mkdir -p /root/ros2_ws/src
WORKDIR /root/ros2_ws
RUN /bin/bash -c "source /opt/ros/humble/setup.bash && colcon build --symlink-install"

# 环境变量
RUN echo "source /opt/ros/humble/setup.bash" >> /root/.bashrc
RUN echo "source /root/ros2_ws/install/setup.bash" >> /root/.bashrc

CMD ["bash"]
```

#### 构建和使用

```bash
docker build -t my_ros2:humble .
docker run -it my_ros2:humble
```

---

## 第十三讲：数字孪生基础——打通虚拟与现实

### 本讲道理
**数字孪生** = 虚拟模型 + 实时数据同步 + 双向控制。

### 示意图
```
[物理世界]  ←传感器数据→  [数字模型]  ←控制指令→  [物理世界]
 (真实机器人)                (ROS 2/Gazebo)            (真实机器人)
```

### 通俗讲解
数字孪生就像一面“魔镜”：镜子里有个一模一样的机器人，真实机器人做什么，镜子里就做什么；你在镜子上点一下，真实机器人也跟着动。

### 核心要点
1. **数字模型**：Gazebo 中的 URDF 模型
2. **数据同步**：传感器数据通过 ROS 2 话题同步
3. **双向控制**：`/cmd_vel` 同时发给物理和虚拟机器人

---

## 第十四讲：大作业——完整数字孪生项目

### 大作业要求

**项目名称：智能仓储机器人数字孪生系统（ROS 2 版）**

在 Gazebo 中构建仓储环境，控制机器人完成：
1. 从起点自主导航到货架
2. “抓取”货物
3. 运送货物到卸货区

### 技术要求
- 使用 URDF/Xacro 描述机器人模型
- 使用 Gazebo 进行物理仿真
- 使用 Nav2 实现自主导航
- 使用 MoveIt 2 控制机械臂（可选）
- 使用 rosbridge 制作网页监控界面（加分）
- 使用 Docker 打包项目（加分）

### 评分标准

| 项目 | 分值 | 说明 |
|------|------|------|
| 环境搭建 | 20分 | Gazebo 仿真环境正常启动 |
| 机器人模型 | 20分 | URDF 模型完整 |
| 自主导航 | 25分 | Nav2 路径规划与执行 |
| 任务完成度 | 20分 | 完整仓储流程 |
| 代码质量 | 10分 | 注释清晰 |
| 报告文档 | 5分 | 设计思路和运行说明 |

---

## 附录：ROS 1 vs ROS 2 常用命令对照

| 功能 | ROS 1 命令 | ROS 2 命令 |
|------|-----------|-----------|
| 编译 | `catkin_make` | `colcon build` |
| 运行节点 | `rosrun pkg node` | `ros2 run pkg executable` |
| 启动文件 | `roslaunch pkg file.launch` | `ros2 launch pkg file.launch.py` |
| 查看节点 | `rosnode list` | `ros2 node list` |
| 查看话题 | `rostopic list` | `ros2 topic list` |
| 查看话题数据 | `rostopic echo /topic` | `ros2 topic echo /topic` |
| 查看服务 | `rosservice list` | `ros2 service list` |
| 查看参数 | `rosparam list` | `ros2 param list` |
| TF | `tf` | `tf2` |
| 可视化 | `rviz` | `rviz2` |
| 建图 | `gmapping` | `slam_toolbox / cartographer` |
| 导航 | `move_base` | `nav2` |

---

## 课程总结

通过这14讲，从零基础到能够：
1. 理解 ROS 2 的通信机制（话题、服务、动作）
2. 编写和调试 ROS 2 节点
3. 使用 Gazebo 进行物理仿真
4. 描述和可视化机器人模型
5. 实现自主导航和机械臂控制
6. 构建数字孪生系统原型

这门课的灵魂：**轻量环境 + 生动比喻 + 可操作例子 + 完整项目**。

