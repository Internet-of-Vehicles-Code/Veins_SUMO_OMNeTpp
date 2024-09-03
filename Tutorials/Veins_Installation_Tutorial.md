## Veins简易安装教程
刚入门的小伙伴推荐通过该方式安装，方便快速上手。需要下载一个虚拟机`VMware Workstation Pro`和`Veins 5.2-i1`，下载方式如下：

- VMware Workstation Pro可以通过官网[（https://www.vmware.com/cn.html）](https://www.vmware.com/cn.html)下载，也可以通过微信公共号【软件共享管家】等下载，激活密钥可以在网上白嫖；

<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/VM.png" alt="Alt text" style="width: 90%;">
</div>

- Veins 5.2-i1通过官网（[https://veins.car2x.org/download/](https://veins.car2x.org/download/)）下载即可。

<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/Veins.png" alt="Alt text" style="width: 90%;">
</div>

下载完成后右键，使用VMware Workstation Pro打开

<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/Veins2.png" alt="Alt text" style="width: 90%;">
</div>

设置存放路径，点击`导入`
<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/Veins3.png" alt="Alt text" style="width: 90%;">
</div>

此时可能会出现导入失败提示，点击`重试`即可
<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/Veins4.png" alt="Alt text" style="width: 90%;">
<img src="images/Veins_Installation_Tutorial_images/Veins5.png" alt="Alt text" style="width: 90%;">
</div>

导入成功后，点击`开启虚拟机`，选择`否`
<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/Veins6.png" alt="Alt text" style="width: 90%;">
<img src="images/Veins_Installation_Tutorial_images/Veins7.png" alt="Alt text" style="width: 90%;">
</div>

初始密码默认为：veins
<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/Veins8.png" alt="Alt text" style="width: 90%;">
</div>

至此，Veins就安装完成啦~
<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/Veins9.png" alt="Alt text" style="width: 90%;">
</div>

接下来我们来运行一下示例代码测试，点击`Activities`->`OMNeT++IDE`

<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/omnetpp.png" alt="Alt text" style="width: 90%;">
</div>

根据下图运行，`OK`->`Yes`->`OK`->`RUN`
<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/omnetpp2.png" alt="Alt text" style="width: 90%;">
<img src="images/Veins_Installation_Tutorial_images/omnetpp3.png" alt="Alt text" style="width: 90%;">
</div>

此时会出现如下错误，Could not connect to TraCI server; error message: 111: Connection refused -- in module (veins::TraCIScenarioManagerLaunchd) RSUExampleScenario.manager (id=6), at t=0s, event #1
<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/error1.png" alt="Alt text" style="width: 90%;">
</div>

这是因为我们没有打开 Veins 模拟框架中用于自动化地管理和启动 SUMO 的一个组件`veins_lanchd`，如下图打开即可
<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/veins_lanchd.png" alt="Alt text" style="width: 90%;">
<img src="images/Veins_Installation_Tutorial_images/veins_lanchd2.png" alt="Alt text" style="width: 90%;">
</div>

注意，运行过程中`veins_lanchd`不能关闭。我们再次点击运行，最终效果如下，说明我们的veins安装是没有问题的。

<div style="text-align: center;">
<img src="images/Veins_Installation_Tutorial_images/veins10.png" alt="Alt text" style="width: 90%;">
</div>

## Veins自定义版本安装教程
随着研究的不断深入，上述版本可能并不适用所有项目，所以需要自定义Veins版本安装。安装教程可参考👉[Ubuntu 下 Veins5.2 安装教程](https://github.com/Yrongovo/Veins5.2-Ubuntu18.04-Installation-Guide)，这里就不再赘述了。


至此，本教程结束。👏 👍