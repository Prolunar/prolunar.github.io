---
title: 关于RVC的一些经验
date: 2023-08-11 01:47:51
categories: [技术]
tags: [RVC,变声,AI]
---
# 一、RVC

### 1、资源下载：

RVC开源github发布地址：

 https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI

NVIDIA Broadcast（输入声音降噪）：

https://www.nvidia.cn/geforce/broadcasting/broadcast-app/

Voicemeeter Potato（虚拟声卡）：

https://vb-audio.com/Voicemeeter/potato.htm

### 2、使用说明：

RVC解压后点击`go-realtime-gui.bat`启动程序，一般来说会用<font color=#FF0000>花儿不哭</font>大佬版本，其他版本基本都是通过整合<font color=#FF0000>花儿不哭</font>版本制作的，如<font color=#FF0000>“小日子”</font>版。我这里用的是原版。

![](https://cdn.jsdelivr.net/gh/Prolunar/BlogImg@main/img/202311192246387.png)

- .pth文件：模型文件，用哪个选哪个就行。

- .index文件：索引，没太大用。😋 甚至可以选个公用的，越好的模型越不需要index。

- 输入设备：选择你的麦克风，注意选以<font color=#00FFFF>MME</font>结尾的。

- 输出设备：选择跳线软件的Input端，比如用VM的话就是选<font color=#00FFFF>VoiceMeeter Input (VB-Audio Voi)</font>，注意选以<font color=#00FFFF>MME</font>结尾的。

- 响应阈值：大于这个分贝的声音才会被处理，一般调-48~-60

- 音调设置：一般男变女+12，女变男-12，声音越低需要越高，自己调

- Index Rate:偏左接近底模，偏右接近数据集，没太大用。😋

- 响度因子：简单来说就是调音量，看着调。

- 高音算法：

  占用<font color=#8B00FF>CPU</font>  pm:对唱歌优化好，较快 harvest:对低音优化好，较慢

  占用<font color=#8B00FF>GPU</font>  crepe:效果好，占用较多负载 rmvpe:较快，占用少

- 采样长度：单位为秒，数值越低延迟越低，负载越高。

- harvest进程数：

  进程数会音响音质，如果对音质有要求，进程数不建议大于4。

  如果index=0，进程数不会太影响CPU占用率。

  如果index不等于0，则CPU占用率会随着进程数几何倍数增加。

  以13700K为例，index=0，8进程，0.12采样，低延迟CPU占用不到30%，但是音质会略微有嘶哑感

  index=0.01，0.12采样，8进程 cpu占用100% ，4进程占用50%。

  决定音高算法最高占用多少系统线程（最多吃多少比例CPU） 如果有其他任务也要吃CPU，会影响你的工作（比如打游戏），则不宜拉太高； 调低了推理延迟会增大，如果推理延迟不能满足需求则应拉高采样长度，或者更换音高算法； 如果推理延迟已经能满足变声延迟的需求，则建议该选项进行微调，越低越好。

- 淡入淡出长度：影响不大，看着拉

- 额外推理时长：数字越大效果越好，负载越小，一般至少设置<font color=#00FFFF>1</font>。占用<font color=#8B00FF>GPU</font>

### 3、输入输出通道设置：

懒得写了，一图流：

![](https://cdn.jsdelivr.net/gh/Prolunar/BlogImg@main/img/202311192351072.png)

稍微进阶：

![](https://cdn.jsdelivr.net/gh/Prolunar/BlogImg@main/img/202311192328199.png)

跳线：独立声卡走机架设置，后面独立篇章详细写。

# 二、训练

MDX23C发布地址：

https://github.com/jarredou/MVSEP-MDX23-Colab_v2

### 这里只讨论AI翻唱，不讨论炼丹

点击`go-web.bat`启动

![](https://cdn.jsdelivr.net/gh/Prolunar/BlogImg@main/img/202311200002007.png)

人声分离：一般来说用<font color=#00FFFF>3hp</font>（有人声就分离）和<font color=#00FFFF>5hp</font>（带和声的情况下只分离主人声）

目前<font color=#00FFFF>3hp</font>被<font color=#00FFFF>MDX23C</font>薄纱

在[这个网站](https://mvsep.com/zh)可以免费试用<font color=#00FFFF>MDX23C</font>

### 去混响：

回到分离人声界面，选择混响模型，首推<font color=#00FFFF>DeEchoDeReverb</font>

可以使用<font color=#00FFFF>MDX23C</font>👉<font color=#00FFFF>去混响</font>👉<font color=#00FFFF>HP5</font>

### 让AI唱起来！

回到推理界面，选择好模型对应的pth文件与index文件

算法首推rmvpe，其次crepe，点击转换即可

# 三、跳线

这个之后再讲吧，太多了，写不动了。



> 参考：
>
> [RVC小白教程-风妈胎教版](https://www.bilibili.com/read/cv24861810/)
>
> [AI声音类项目汇总以及详细教程](https://docs.qq.com/doc/DRVZldlZOckptT0pJ)
>
> [RVC AI翻唱个人经验分享](https://www.bilibili.com/video/BV1K94y1G7F1)