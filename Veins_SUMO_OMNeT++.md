- [Veins \& SUMO \& OMNeT++ 车联网仿真平台简介](#veins--sumo--omnet-车联网仿真平台简介)
  - [Veins](#veins)
  - [SUMO](#sumo)
  - [OMNeT++](#omnet)
- [SUMO相关操作](#sumo相关操作)
  - [SUMO地图替换](#sumo地图替换)
    - [1. 使用netconvert转换工具](#1-使用netconvert转换工具)
    - [2. 手动编写](#2-手动编写)
  - [TraCI接口](#traci接口)
  - [参考链接](#参考链接)
- [OMNeT++相关操作](#omnet相关操作)
  - [示例代码介绍](#示例代码介绍)
    - [Veins示例代码中各文件定义](#veins示例代码中各文件定义)
  - [常用功能代码编写](#常用功能代码编写)
  - [OMNeT++中链接OpenSSL库](#omnet中链接openssl库)
- [平台中可能遇到的问题](#平台中可能遇到的问题)
  - [Ubuntu磁盘扩容](#ubuntu磁盘扩容)
  - [安装中文输入法](#安装中文输入法)




## Veins & SUMO & OMNeT++ 车联网仿真平台简介

![](./image/Veins/image1.png)

### Veins

![](./image/Veins/image2_veins.gif)

Veins（Vehicles in Network Simulation）是一个用于运行车辆网络模拟的开源框架，包括一套全面的模型，能够模拟车辆通信系统，包括车对车（Vehicle-to-Vehicle，简称V2V）和车对基础设施（Vehicle-to-Infrastructure，简称V2I）的通信。Veins通过TCP套接字连接基于事件的网络模拟器（OMNeT++）和道路交通模拟器（SUMO）。

- Veins官网链接：[https://veins.car2x.org/](https://veins.car2x.org/)
- 目前车联网小分队成员常用的Veins仿真平台，下载用VM虚拟机打开即可。

  链接：[https://pan.quark.cn/s/afe7a9459aa0](https://pan.quark.cn/s/afe7a9459aa0)

  提取码：UTSW

### SUMO

![](./image/Veins/image10_SUMO_logo.jpg)

SUMO（Simulation of Urban Mobility），是开源、微观、多模态的城市交通模拟工具，用于模拟城市交通，如车辆，公共汽车，自行车和行人等。

- SUMO官网链接：[https://eclipse.dev/sumo/](https://eclipse.dev/sumo/)
- SUMO官方文档：[https://sumo.dlr.de/docs/index.html](https://sumo.dlr.de/docs/index.html)

### OMNeT++

![](./image/Veins/image11_OMNeT_logo.jpg)

Veins使用OMNeT++（Objective Modular Network Testbed）作为其网络模拟器。OMNeT++是一个可扩展的、模块化的、基于组件的C++仿真库和框架，主要用于构建网络模拟器，用来模拟计算机网络、多处理器和分布式系统等。OMNeT++提供了一个基于Eclipse的IDE，一个图形运行时环境，以及许多其他工具。有用于实时模拟、网络仿真、数据库集成的扩展，SystemC集成和其他几个功能。

- OMNeT++官网链接：[https://omnetpp.org/](https://omnetpp.org/)
- OMNeT++官方文档：[https://omnetpp.org/documentation/](https://omnetpp.org/documentation/)

## SUMO相关操作

SUMO仿真器跑起来需要有三个文件，分别是Network、Route以及SUMO configuration file。

- `***.net.xml`文件：路网构建、创建交通信号灯等，生成道路文件；
- `***.rou.xml`文件：生成车辆文件；
- `***.sumocfg`文件：将net.xml和rou.xml文件结合起来实现仿真。

### SUMO地图替换

SUMO中路网文件的编写可以手动编写，也可以用`netconvert`命令转换第三方来源中的复杂路网。总体包括道路、交叉口的id和位置信息、车道信息（数量、长度、最大速度、形状、功能等）、优先权信息、交通信号信息、交叉口信息等。下面介绍了两种生成SUMO路网文件的方法。

#### 1. 使用netconvert转换工具

通过在命令提示符（cmd）中输入netconvert指令，能够将多种第三方的路网文件转化为SUMO可读的文件，具体可转化的第三方来源有：OpenStreetMap（一种开源的地图引擎）、PTV Vissim、OpenDrive、MATsim、ArcView、Elmar Brockfelds unsplitted and splitted NavTeq-data、RoboCup Rescue League folders等。例如在linux系统下使用OpenStreetMap导入地图操作如下：

- `***.osm`文件生成

  首先进入OpenStreetMap官网（[https://www.openstreetmap.org/](https://www.openstreetmap.org/)），按下图步骤选择区域并导出`map.osm`文件；

  ![](./image/Veins/image3.png)

  将导出的文件复制到linux系统中，然后再利用sumo bin目录下的netconvert、polyconvert转换工具和sumo tools目录下的`randomTrips.py`工具生成相应的SUMO仿真文件。具体步骤如下：
- `***.net.xml`道路文件生成

  打开终端
  ![](./image/Veins/image29.jpg)
  输入以下内容生成道路文件
  ````
  netconvert --osm-files map.osm -o map.net.xml
  ````

    如果报错则需要配置临时环境变量，在终端输入以下内容配置临时变量

  ````
  export SUMO\_HOME=/home/veins/src/sumo-1.11.0
  ````

  netconvert可以将`***.osm`文件转换成`***.net.xml`文件，`--osm-files`即表示输入文件类型， `-o map.net.xml`是输出， `-o`即output的意思，map.net.xml即为输出文件。
- `***.rou.xml`车辆行为文件生成

  由于车辆的行为是多种多样的，所以可以利用SUMO中的`randomTrips.py`脚本根据道路状况随机化车辆行为生成相应的`***.rou.xml`文件。在终端输入以下内容将道路和行驶路径整合生成车辆行为文件

  ````
  /home/veins/src/sumo-1.11.0/tools/randomTrips.py -n map.net.xml -e 100 -l
  /home/veins/src/sumo-1.11.0/tools/randomTrips.py -n map.net.xml -r map.rou.xml -e 100 -l
  ````
- `***.poly.xml`地形文件生成

  因为在veins仿真过程中，将用到地形文件。polyconvert转换工具可以根据`***.net.xml`和`***.osm`文件生成相应的地形文件。在终端输入以下内容生成地形文件

  ````
  polyconvert --net-file map.net.xml --osm-files map.osm -o map.poly.xml
  ````
- `***.sumo.cfg`配置文件生成

  新建一个`map.sumo.cfg`文件，编写内容如下

  ````
  <?xml version="1.0" encoding="iso-8859-1"?>

  <configuration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="http://sumo.sf.net/xsd/sumoConfiguration.xsd">

  <input>
      <net-file value="map.net.xml"/>
      <route-files value="map.rou.xml"/>
      <additional-files value="map.poly.xml"/>
  </input>

  <time>
      <begin value="0"/>
      <end value="1000"/>
      <step-length value="0.1"/>
  </time>

  <report>
      <no-step-log value="true"/>
  </report>

  <gui_only>
      <start value="true"/>
  </gui_only>

  </configuration>
  ````

- 开启可视化界面

  在终端输入以下内容

  ````
  sumo-gui map.sumo.cfg
  ````

  此时会打开SUMO的GUI，按下图操作观察SUMO车辆行为仿真。

  ![](./image/Veins/image4.png)

  ![](./image/Veins/image5.png)

  ![](./image/Veins/image6.png)

  如果需要对地图进行小部分修改，可以点击`Edit->Open in netedit`。**注意**：修改之后，意味着`***.net.xml`道路文件发生变化，需要重新生成车流行为文件，避免报错。

  ![](./image/Veins/image7.png)

  ![](./image/Veins/image8.png)

  如果地图太大，边缘多余道路、地铁线等可以使用JSOM软件（[https://josm.openstreetmap.de/](https://josm.openstreetmap.de/)）进行地图编辑，之后重新生成车辆行为文件即可。
- 加载地图到Veins中

  复制路网文件（map.net.xml）、车辆行为文件（map.rou.xml）、地形文件（map.poly.xml）、配置文件（map.sumo.cfg）到OMNeT++示例项目的`Veins -> examples -> veins`路径下，同时修改`erlangen.launchd.xml`和`erlangen.sumo.cfg`中的相应内容，如下:

  ![](./image/Veins/image30.jpg)
  ![](./image/Veins/image31.jpg)  
  
  
  或者重新生成`map.launchd.xml`和`map.sumo.cfg`文件，并在`omnetpp.ini`文件中修改相应内容，如下：
  ![](./image/Veins/image32.jpg) 
  
  运行`omnetpp.ini`文件，并根据需求在`.ini`配置文件下更改相应内容。 
  
  ![](./image/Veins/image9.png)

  例如：运行后如遇到下述情况，则需要在`omnetpp.ini`文件中修改运行方框尺寸
  ![](./image/Veins/image33.jpg)

  ![](./image/Veins/image34.jpg) 

#### 2. 手动编写

- **路网编辑：**
打开netedit通过选择 File->New Network 创建一个新网络，并确保Network被选中。以生成一个九宫格地图（2k×2k）为例。

  - 进入netedit：`...\sumo\bin\netedit.exe`
  - 创建网络：选择 File->New Network 创建一个新网络
  - 创建节点和边并修改属性：edge mode->（创建多个节点打开chain mode 模式）->inspect mode修改属性
![](./image/Veins/image23.jpg)
![](./image/Veins/image24.jpg)
  - 添加反向车道：鼠标右键边->Edge operations->Add reverse direction for edge
![](./image/Veins/image25.jpg)
![](./image/Veins/image26.jpg)
  - 根据需求create TLS
  - 路网保存

    ![](./image/Veins/image27.jpg)

    将保存的`***.net.xml`文件复制到veins，之后的操作参照方法1，但是由于没有建筑物等，不用生成地形文件`***.poly.xml`，在`***.sumo.cfg`文件中删除`<additional-files value="***.poly.xml"/>`。

    最后，在终端输入`sumo-gui ***.sumo.cfg`，运行效果如下：
    ![](./image/Veins/image35.jpg)

### TraCI接口

TraCI (Traffic Control Interface) 是一个用于远程控制 SUMO (Simulation of Urban MObility) 交通模拟器的接口，通过一个 TCP/IP 连接与 SUMO 通信。通过 TraCI，用户可以在运行模拟的同时，从外部程序改变交通网络的状态，例如改变车辆的速度或路线，切换交通灯的状态等。这使得用户可以创建交互式的模拟，或者实现复杂的控制策略。

- SUMO中如何使用TraCI，官方文档：[https://sumo.dlr.de/docs/TraCI.html](https://sumo.dlr.de/docs/TraCI.html)

### 参考链接

- Veins官网链接：[https://veins.car2x.org/](https://veins.car2x.org/)
- SUMO官网链接：[https://eclipse.dev/sumo/](https://eclipse.dev/sumo/)
- SUMO官方用户文档：[https://sumo.dlr.de/docs/index.html](https://sumo.dlr.de/docs/index.html)
- SUMO教程：[https://blog.csdn.net/weixin_47786612/article/details/130164305](https://blog.csdn.net/weixin_47786612/article/details/130164305)
- SUMO学习入门：[https://zhuanlan.zhihu.com/p/157232558](https://zhuanlan.zhihu.com/p/157232558)
- SUMO学习入门（二）路网文件生成：[https://zhuanlan.zhihu.com/p/164777831](https://zhuanlan.zhihu.com/p/164777831)
- SUMO地图替换：[https://blog.csdn.net/nhgytftdtsres/article/details/80611723](https://blog.csdn.net/nhgytftdtsres/article/details/80611723)
- 地图编辑之JOSM：[https://blog.csdn.net/scy411082514/article/details/7493386](https://blog.csdn.net/scy411082514/article/details/7493386)
- 哔哩哔哩·SUMO软件基本教学：[[link]](https://www.bilibili.com/video/BV1H7411F76B/?from=search&seid=18074238600246103248&vd_source=f17606480b273d73e7f15a69fe00985e)
- SUMO中文视频教程：[[link]](https://space.bilibili.com/110602843?from=search&seid=6972657569966499773)

## OMNeT++相关操作

![](./image/Veins/image12.jpg)

### 示例代码介绍
#### Veins示例代码中各文件定义

- veins->examples->veins下各文件
  - results文件夹

    用于存储模拟运行后生成的所有输出和日志文件。
    
    - Scalar files (.sca)：这些文件包含了模拟结束时的统计信息，例如发送和接收的包的数量，延迟的平均值和标准差等。
    - Vector files (.vec)：这些文件包含了模拟过程中记录的时间序列数据，例如每个包的发送和接收时间，每个节点的位置等。
    - Eventlog files (.elog)：如果你启用了事件日志记录，这些文件会包含模拟过程中所有的事件详细信息。这些文件可以用 OMNeT++ 的 Sequence Chart 工具来查看。
    - Log files (.log)：这些文件包含了模拟过程中的日志信息，例如错误和警告信息。

  - antenna.xml
  
    用于定义和配置无线通信中的天线模型。（未用到）

  - config.xml
  
    用于配置模拟的无线信号传播和接收模型的物理特性。AnalogueModels 部分包含两个 AnalogueModel：SimplePathlossModel 和 SimpleObstacleShadowing。（基于RSSI的女巫攻击检测中用到过）

    SimplePathlossModel 有一个 alpha 参数，值为 2.0。alpha 通常用于描述路径损失模型中的路径损失指数，它是一个描述随着距离增加，信号强度如何衰减的参数。

    SimpleObstacleShadowing 是一个描述物体（如建筑物）对无线信号造成阴影效应的模型。这里定义了一种类型的障碍物 building，并给出了每次穿越和每米对信号强度造成的衰减。

    Decider 部分定义了一个类型为 Decider80211p 的决策器。决策器在无线通信中通常用于决定在给定的信号和噪声条件下，是否可以成功接收一个包。在这里，Decider80211p 有一个 centerFrequency 参数，值为 5.890e9，这个参数可能表示决策器监听的中心频率。
  
  - erlangen.launchd.xml

    用于配置 SUMO 模拟器的启动参数。通过修改这个文件，你可以改变模拟的输入、时间参数、报告设置和 GUI 行为，从而影响模拟的行为和结果。

  - erlangen.net.xml

    定义了网络文件，描述了道路网络的结构。

  - erlangen.poly.xml

    定义了附加文件，可能用于描述建筑物或其他地理特征。

  - erlangen.rou.xml

    定义了路线文件，描述了车辆的路线。

  - erlangen.sumo.cfg

    定义 SUMO 模拟的输入文件、模拟的时间参数、报告设置以及 GUI 的行为。\<input>：这部分定义了模拟的输入文件。\<time>：这部分定义了模拟时间的参数(开始时间、结束时间、时间步长)。\<report>：这部分定义了模拟报告的设置。\<gui_only>：这部分定义了 GUI 的行为。

  - omnetpp.ini

    用于配置模拟参数的文件。在这个文件中，你可以定义和配置各种模拟参数，例如模拟的持续时间，所使用的网络模型，模块参数，随机数生成器的种子，等等。

  - RSUExampleScenario.ned

    这个文件描述了一个名为 RSUExampleScenario 的网络，该网络扩展自 Scenario 类，并包含一个 RSU 类型的子模块。更改仿真场景RSU数量时需要修改此文件，源代码如下：

    ````Java
    import org.car2x.veins.nodes.RSU;
    import org.car2x.veins.nodes.Scenario;

    network RSUExampleScenario extends Scenario
    {
      submodules:
        rsu[1]: RSU {     // 更改仿真场景RSU数量时需要修改此处
          @display("p=150,140;i=veins/sign/yellowdiamond;is=vs");
        }
    }
    ````


- veins->src->veins->modules->applicaton->traci下各文件（主要在该目录下编写仿真代码）

  - TraCIDemo11p.ned

    定义了一个名为 TraCIDemo11p 的简单模块，用于模拟车辆的应用层功能。该模块可以进行处理和发送基于车载网络的消息。
    ````Java
    package org.car2x.veins.modules.application.traci;
    import org.car2x.veins.modules.application.ieee80211p.DemoBaseApplLayer;

    simple TraCIDemo11p extends DemoBaseApplLayer
    {
        @class(veins::TraCIDemo11p);
        @display("i=block/app2");
    }
    ````

  - TraCIDemo11p.h

    当汽车停止超过 10 秒时，它会向其他汽车发送一条包含阻塞道路 ID 的消息。 然后接收车辆将通过 TraCI 触发重新路线。 当 MAC 上启用 SCH 和 CCH 之间的信道切换时，消息将在 CCH 上的服务通告之后在服务信道上发送。

  ````c++
  #pragma once

  #include "veins/modules/application/ieee80211p/DemoBaseApplLayer.h"

  namespace veins {

  class VEINS_API TraCIDemo11p : public DemoBaseApplLayer {
  public:
      void initialize(int stage) override;  // 初始化

  protected:
      simtime_t lastDroveAt;
      bool sentMessage;
      int currentSubscribedServiceId;

  protected:
      void onWSM(BaseFrame1609_4* wsm) override;  // 处理 WSM 消息
      void onWSA(DemoServiceAdvertisment* wsa) override;  // 处理 WSA 消息

      void handleSelfMsg(cMessage* msg) override;   // 处理自消息
      void handlePositionUpdate(cObject* obj) override; // 车辆位置更新时相关处理
  };

  }
  ````


  - TraCIDemo11p.cc

    主要目的是演示如何在车载网络中使用和处理消息。该模块实现了一个简单的车辆通信协议，其中车辆会定期广播其位置信息，并且可以处理接收到的其他车辆的位置信息。代码解释如下：

  ````c++
  // 对于每个模块，initialize 方法会被调用多次，每个阶段（stage）分别对应不同的初始化任务。

  void TraCIDemo11p::initialize(int stage)
  {
      DemoBaseApplLayer::initialize(stage);   // 调用父类 DemoBaseApplLayer 的 initialize 方法
      if (stage == 0) {   // 阶段 0 通常用于设置模块的内部状态和参数
          sentMessage = false;  // 表示这个模块尚未发送任何消息
          lastDroveAt = simTime();  // simTime()用于读取当前仿真时间，表示最后一次驾驶的时间
          currentSubscribedServiceId = -1;  // 表示当前没有订阅任何服务
      }
  }
  ````

  ````c++
  // 该部分仿真过程中未使用，可以删除
  // onWSA 方法的作用是处理收到的 WSA（Wave Service Advertisement）服务广告消息。
  // 示例代码功能：当收到一个 WSA 消息时，如果当前没有订阅任何服务，或者当前提供的服务与 WSA 消息中的服务不同，则会改变服务频道，并订阅 WSA 消息中的服务

  void TraCIDemo11p::onWSA(DemoServiceAdvertisment* wsa)
  {
      // 检查当前是否已经订阅了某个服务。值为 -1，则表示当前没有订阅任何服务。
      if (currentSubscribedServiceId == -1) {   
          //改变当前的服务频道
          mac->changeServiceChannel(static_cast<Channel>(wsa->getTargetChannel()));
          // 将 WSA 消息中的 PSID （Provider Service Identifier）设置为当前订阅的服务 ID。
          currentSubscribedServiceId = wsa->getPsid();  
          // 检查当前提供的服务 ID 是否与 WSA 消息中的 PSID 相同。
          if (currentOfferedServiceId != wsa->getPsid()) {
              stopService();  // 停止当前的服务
              // 启动新的服务。
              startService(static_cast<Channel>(wsa->getTargetChannel()), wsa->getPsid(), "Mirrored Traffic Service");
          }
      }
  }
  ````

  ````c++
  // onWSM 方法的作用是处理收到的 WSM（Wave Short Message）消息。
  // 示例代码功能：当收到一个 WSM 时，如果这是第一次收到消息，则会发送一条新的消息；同时，如果路线 ID 不以 ':' 开头，还会改变路线

  void TraCIDemo11p::onWSM(BaseFrame1609_4* frame)
  {
      TraCIDemo11pMessage* wsm = check_and_cast<TraCIDemo11pMessage*>(frame);

      // 表示车辆接收到了一个消息时在 OMNeT++ 的 GUI 中改变模块的颜色。
      findHost()->getDisplayString().setTagArg("i", 1, "green");

      // 检查当前的路线 ID 是否以 ':' 开头。如果不是，就调用 traciVehicle->changeRoute 方法改变路线。
      if (mobility->getRoadId()[0] != ':') traciVehicle->changeRoute(wsm->getDemoData(), 9999);
      if (!sentMessage) {
          sentMessage = true;
          // repeat the received traffic update once in 2 seconds plus some random delay
          wsm->setSenderAddress(myId);  // 设置消息的发送者地址
          wsm->setSerial(3);    // 设置消息的发送者序列号
          scheduleAt(simTime() + 2 + uniform(0.01, 0.2), wsm->dup());
      }
  }
  ````

  ````c++
  // 用于处理自发的消息。
  // 示例代码功能：到一个 TraCIDemo11pMessage 时，会发送一个复制的消息，然后增加消息的序列号，如果序列号大于或等于3，则停止服务并删除这条消息；否则，安排一个事件，在未来的某个时间再次发送这条消息。对于不是 TraCIDemo11pMessage 的消息，将其交给父类处理。

  void TraCIDemo11p::handleSelfMsg(cMessage* msg)
  {
      if (TraCIDemo11pMessage* wsm = dynamic_cast<TraCIDemo11pMessage*>(msg)) {
          // send this message on the service channel until the counter is 3 or higher.
          // this code only runs when channel switching is enabled
          sendDown(wsm->dup());
          wsm->setSerial(wsm->getSerial() + 1);
          if (wsm->getSerial() >= 3) {
              // stop service advertisements
              stopService();
              delete (wsm);
          }
          else {
              scheduleAt(simTime() + 1, wsm);
          }
      }
      else {
          DemoBaseApplLayer::handleSelfMsg(msg);
      }
  }
  ````

  ````c++
  // 位置更新时被调用
  // 示例代码功能：当位置更新时，根据车辆的速度和是否已经发送过消息来决定是否需要发送一个新的消息，以及怎么发送这个消息。

  void TraCIDemo11p::handlePositionUpdate(cObject* obj)
  {
      DemoBaseApplLayer::handlePositionUpdate(obj);

      // stopped for for at least 10s?
      if (mobility->getSpeed() < 1) {
          if (simTime() - lastDroveAt >= 10 && sentMessage == false) {
              findHost()->getDisplayString().setTagArg("i", 1, "red");
              sentMessage = true;

              TraCIDemo11pMessage* wsm = new TraCIDemo11pMessage();
              populateWSM(wsm);
              wsm->setDemoData(mobility->getRoadId().c_str());  // 设置消息内容为当前的路线 ID

              // host is standing still due to crash
              if (dataOnSch) {
                  startService(Channel::sch2, 42, "Traffic Information Service");
                  // started service and server advertising, schedule message to self to send later
                  scheduleAt(computeAsynchronousSendingTime(1, ChannelType::service), wsm);
              }
              else {
                  // send right away on CCH, because channel switching is disabled
                  sendDown(wsm);  // 发送消息
              }
          }
      }
      else {
          lastDroveAt = simTime();
      }
  }
  ````

  - TraCIDemo11pMessage_m.cc (一般用不到)

  - TraCIDemo11pMessage_m.h（一般用不到）

  - TraCIDemoRSU11p.cc

  ````c++
  #include "veins/modules/application/traci/TraCIDemoRSU11p.h"

  #include "veins/modules/application/traci/TraCIDemo11pMessage_m.h"

  using namespace veins;

  Define_Module(veins::TraCIDemoRSU11p);

  void TraCIDemoRSU11p::onWSA(DemoServiceAdvertisment* wsa)
  {
      // if this RSU receives a WSA for service 42, it will tune to the chan
      if (wsa->getPsid() == 42) {
          mac->changeServiceChannel(static_cast<Channel>(wsa->getTargetChannel()));
      }
  }

  void TraCIDemoRSU11p::onWSM(BaseFrame1609_4* frame)
  {
      TraCIDemo11pMessage* wsm = check_and_cast<TraCIDemo11pMessage*>(frame);

      // this rsu repeats the received traffic update in 2 seconds plus some random delay
      sendDelayedDown(wsm->dup(), 2 + uniform(0.01, 0.2));
  }
  ````

  - TraCIDemoRSU11p.h

  ````c++
  #pragma once

  #include "veins/modules/application/ieee80211p/DemoBaseApplLayer.h"

  namespace veins {

  /**
  * Small RSU Demo using 11p
  */
  class VEINS_API TraCIDemoRSU11p : public DemoBaseApplLayer {
  protected:
      void onWSM(BaseFrame1609_4* wsm) override;
      void onWSA(DemoServiceAdvertisment* wsa) override;
  };

  } // namespace veins
  ````

  - MyVeinsApp.cc
    
  ````c++
        // Initializing members and pointers of your application goes here
        EV << "Initializing " << par("appName").stringValue() << std::endl;
  ````

  - MyVeinsApp.h

  ````c++
  #pragma once

  #include "veins/veins.h"

  #include "veins/modules/application/ieee80211p/DemoBaseApplLayer.h"

  using namespace omnetpp;

  namespace veins {

  class VEINS_API MyVeinsApp : public DemoBaseApplLayer {
  public:
      void initialize(int stage) override;
      void finish() override;

  protected:
      void onBSM(DemoSafetyMessage* bsm) override;
      void onWSM(BaseFrame1609_4* wsm) override;
      void onWSA(DemoServiceAdvertisment* wsa) override;

      void handleSelfMsg(cMessage* msg) override;
      void handlePositionUpdate(cObject* obj) override;
  };

  } // namespace veins
  ````

  - MyVeinsApp.ned

    定义了一个名为 MyVeinsApp 的简单模块，用于模拟车辆的应用层功能。这个模块可以进行处理和发送基于车载网络的消息。同时，该模块有一个参数 appName，可以在实例化该模块时进行设置，以标识或描述该模块的作用。
  ````Java
  package org.car2x.veins.modules.application.traci;
  import org.car2x.veins.modules.application.ieee80211p.DemoBaseApplLayer;

  //
  // network description file for your Veins Application. Add parameters here
  //
  simple MyVeinsApp extends DemoBaseApplLayer
  {
      @class(veins::MyVeinsApp);
      string appName = default("My first Veins App!");
  }
  ````



  - TraCIDemo11pMessage.msg

    定义了一个 Veins 中的包，这个包可以在车载网络模拟中用于传输数据。
  ````Java
  import veins.base.utils.Coord;
  import veins.modules.messages.BaseFrame1609_4;
  import veins.base.utils.SimpleAddress;

  namespace veins;

  packet TraCIDemo11pMessage extends BaseFrame1609_4 {
      string demoData;
      LAddress::L2Type senderAddress = -1;
      int serial = 0;
  }
  ````

  - TraCIDemoRSU11p.ned

    定义了一个名为 TraCIDemoRSU11p 的简单模块，用于模拟路侧单元（RSU）的功能。这个模块可以用于处理和发送基于车载网络的消息。源代码如下：

  ````Java
  package org.car2x.veins.modules.application.traci;
  import org.car2x.veins.modules.application.ieee80211p.DemoBaseApplLayer;

  simple TraCIDemoRSU11p extends DemoBaseApplLayer
  {
      @class(veins::TraCIDemoRSU11p);   // 用于指定与该模块相关联的 C++ 类
      @display("i=block/app2");   // 指定在 OMNeT++ GUI 中显示模块的图标。
  }
  ````

### 常用功能代码编写
- 发送 WSM 消息
  新建一条WSM消息并广播，代码如下：

  ````c++
  TraCIDemo11pMessage* newWSM = new TraCIDemo11pMessage();
  populateWSM(newWSM);
  std::string newWSM_str = "A new WSM type message.";
  newWSM->setSenderAddress(myId);
  newWSM->setDemoData(newWSM_str.data());
  sendDown(newWSM->dup());
  std::cout << this->myId << ": " << newWSM->getDemoData() << std::endl;
  ````  
  效果如下：

  ![](./image/Veins/image17.jpg)

- 接收 WSM 消息
  
  接收WSM消息，代码如下：

  ````c++
  void TraCIDemo11p::onWSM(BaseFrame1609_4* frame)
  {
      TraCIDemo11pMessage* wsm = check_and_cast<TraCIDemo11pMessage*>(frame);

      std::string revWSM_str = wsm->getDemoData();
      std::cout << "Car[" << this->myId << "] recieve the car[" 
                << wsm->getSenderAddress() << "] message:" << revWSM_str << std::endl;
  }
  ````  
   效果如下：

  ![](./image/Veins/image18.jpg)

- 设置多个RSU
  
  在设计我们的仿真场景时，我们可能会涉及到多个RSU，需要在 `veins->examples->veins->RSUExampleScenario.ned` 文件中设置RSU数量，并且在 `veins->examples->veins->omnetpp.ini` 文件中设置每个RSU相对于地图中的位置。例如在示例场景中简单设置2个RSU，代码如下：

  ````ini
  # veins->examples->veins->omnetpp.ini 文件中

  ##########################################################
  #                       RSU SETTINGS                     #
  #                                                        #
  #                                                        #
  ##########################################################
  *.rsu[0].mobility.x = 2000
  *.rsu[0].mobility.y = 2000
  *.rsu[0].mobility.z = 3

  *.rsu[1].mobility.x = 1500
  *.rsu[1].mobility.y = 1600
  *.rsu[1].mobility.z = 3

  *.rsu[*].applType = "TraCIDemoRSU11p"
  *.rsu[*].appl.headerLength = 80 bit
  *.rsu[*].appl.sendBeacons = false
  *.rsu[*].appl.dataOnSch = false
  *.rsu[*].appl.beaconInterval = 1s
  *.rsu[*].appl.beaconUserPriority = 7
  *.rsu[*].appl.dataUserPriority = 5
  *.rsu[*].nic.phy80211p.antennaOffsetZ = 0 m
  ```` 

  ````Java
  // veins->examples->veins->RSUExampleScenario.ned 文件中

  network RSUExampleScenario extends Scenario
  {
    submodules:
        rsu[2]: RSU {
            @display("p=150,140;i=veins/sign/yellowdiamond;is=vs");
        }
  }
  ````  
  效果如下：

  ![image19](image/Veins/image19.jpg)

- 获取节点位置
  
  在`veins->src->veins->modules->application->traci->TraCIDemo11p.cc`或`MyVeinsApp.cc`中可以通过如下指令获取车辆位置。例如在`TraCIDemo11p.cc`中通过函数`void TraCIDemo11p::handlePositionUpdate(cObject* obj)`控制车辆在每次位置更新时输出位置，代码如下:

  ````c++
  std::cout << "Car[" << this->myId << "] curPosition" << this->curPosition << std::endl;
  std::cout << "Car[" << this->myId << "] curPosition.x" << this->curPosition.x << std::endl;
  std::cout << "Car[" << this->myId << "] curPosition.y" << this->curPosition.y << std::endl;
  std::cout << "Car[" << this->myId << "] curPosition.z" << this->curPosition.z << std::endl;
  ````  

  效果如下：

  ![](./image/Veins/image20.png)

- 获取节点速度

  首先需在`omnetpp.ini`文件中对 Mobility 部分进行如下修改，否则车辆节点获取不到车辆速度，导致输出速度为(0,0,0)：
  ![](./image/Veins/image16.jpg)

  获取速度代码与获取位置类似，代码如下：

  ````c++
  std::cout << "Car[" << this->myId << "] curSpeed " << this->curSpeed << std::endl;
  std::cout << "Car[" << this->myId << "] curSpeed.x " << this->curSpeed.x << std::endl;
  std::cout << "Car[" << this->myId << "] curSpeed.y " << this->curSpeed.y << std::endl;
  std::cout << "Car[" << this->myId << "] curSpeed.z " << this->curSpeed.z << std::endl;
  ````  
  效果如下

  ![](./image/Veins/image21.png)

- 获取消息的接收功率
  
  在进行Sybil攻击检测仿真时，计算接收信号强度（RSSI）需要用到消息的接收功率，获取接收功率代码如下：

  ````c++
  // 头文件
  #include "veins/base/phyLayer/PhyToMacControlInfo.h"
  #include "veins/modules/phy/DeciderResult80211.h"

  // 代码
  double curRecvPower_dBm = check_and_cast<DeciderResult80211*>(check_and_cast<PhyToMacControlInfo*>(rewsm->getControlInfo())->getDeciderResult())->getRecvPower_dBm();  //  rewsm 表示接收的一条WSM消息
  std::cout << "curRecvPower_dBm = " << curRecvPower_dBm << " dBm." << std::endl;
  ````  

  效果如下：

  ![](./image/Veins/image22.png)



### OMNeT++中链接OpenSSL库

- 在linux中安装OpenSSL库
  参考链接：CSDN博客·Linux环境下安装OpenSSL（源码方式安装）：[[link]](https://blog.csdn.net/weixin_39274753/article/details/107958283)
- 在OMNeT++中链接外部库：`-lssl`和`-lcrypto`，具体操作如下图所示
 
  ![](./image/Veins/image13.png)

  ![](./image/Veins/image14.png)

  ![](./image/Veins/image15.png)


## 平台中可能遇到的问题

### Ubuntu磁盘扩容

- CSDN博客·Ubuntu磁盘扩容：[link](https://blog.csdn.net/qq_45853229/article/details/124595300?ydreferer=aHR0cHM6Ly9jbi5iaW5nLmNvbS8=)

### 安装中文输入法

- 知乎·Debian10 更换软件源 & 配置中文环境 & 安装中文输入法：[[link]](https://zhuanlan.zhihu.com/p/106775707)
- CSDN博客·Linux下安装中文输入法：[[link]](https://blog.csdn.net/yanhanhui1/article/details/115128309)
- CSDN博客·Ubuntu 20.04安装搜狗输入法[[link]](https://blog.csdn.net/code_change_era/article/details/113834432)


[def]: #安装中文输入法

ovo