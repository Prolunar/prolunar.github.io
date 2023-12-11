---
title: 如何使用VPS自制梯子
date: 2023-12-11 01:36:52
categories: [技术]
tags: [VPS,梯子]
---
# 一、前期准备

### VPS

vps这里选择比较便宜的！主打一个性价比

我选择了第一个好像是黑五优惠的，感觉性价比不错

| RackNerd                     |              |
| ---------------------------- |---------------------------- |
| 1x vCPU                      | 1x vCPU |
| 768 MB RAM                | 2GB RAM |
| 15 GB SSD                    | 30GB SSD |
| 1 TB 月流量                  | 2.5T 月流量 |
| 1 Gbps 带宽                  | 1 Gbps 带宽 |
| 1 IPv4                | 1 IPv4 |
| $10.18 /年 | $16.98/年 |

测测速度看看哪个节点快就选哪个，我这测试感觉芝加哥挺快的

选完节点点击1000MB那个下载就完事了：[测速网站](https://lg-lax02.racknerd.com/)

其他还可以考虑像<font color=#00FFFF>CloudCone</font>这样的，但感觉不如上一个性价比



### 寻找 TLS1.3/H2 的网站

目标网站最低标准：国外网站，支持 TLSv1.3 与 H2，域名非跳转用

加分项：IP 相近（更像，且延迟低），Server Hello 后的握手消息一起加密（如 dl.google.com），有 OCSP Stapling

配置加分项：禁回国流量，TCP/80、UDP/443 也转发（REALITY 对外表现即为端口转发，目标 IP 冷门或许更好）

可以使用这个网站：[点击访问](https://v2rayssr.com/go?url=https://www.ssllabs.com/projects/index.html) ，来寻找 `TLS1.3 / X25519 / H2` 的指向站点

> 本来是想用vless+ws+tls+cdn中转这个方案的，可惜最后cdn是中转好了，节点怎么都ping不通，放弃了，还是使用Reality吧，Reality方案好像是用别人的域名做伪装，我找了个亚特兰大附近的域名。

# 二、设置

## 连接VPS

买完之后<font color=#00FFFF>RackNerd</font>会给邮箱里发控制面板的账号密码，以及root密码，用于ssh登录

我这边选的系统是 Ubuntu 20.04，别的像debian\centos也可以，反正后台可以改系统。

使用远程连接软件接入vps：我这边选择使用[NxShell](https://github.com/nxshell/nxshell/blob/main/README-zh.md)，这软件比较好看

连接上ssh就可以进下一步了



### 更新及安装组件

Debian/Ubuntu系统执行以下命令

```sh
apt update -y && apt install curl wget -y
```

CentOS系统执行以下命令

```sh
yum update -y && yum install curl wget -y
```



### x-ui面板

输入一键脚本

```sh
bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/master/install.sh)
```

### x-ui管理命令

x-ui - 显示管理菜单 (功能更多)
x-ui start - 启动 x-ui 面板
x-ui stop - 停止 x-ui 面板
x-ui restart - 重启 x-ui 面板
x-ui status - 查看 x-ui 状态
x-ui enable - 设置 x-ui 开机自启
x-ui disable - 取消 x-ui 开机自启
x-ui log - 查看 x-ui 日志
x-ui v2-ui - 迁移本机器的 v2-ui 账号数据至 x-ui
x-ui update - 更新 x-ui 面板
x-ui install - 安装 x-ui 面板
x-ui uninstall - 卸载 x-ui 面板

连接ssh输入x-ui也可查看命令,访问 x-ui 面板的地址为 `http://ip:端口/面板url根路径`



### warp解锁v4 v6

使用warp-go脚本解锁，不难

```sh
wget -N https://gitlab.com/fscarmen/warp/-/raw/main/warp-go.sh && bash warp-go.sh
```




### 部署 Reality 节点

不难，注意以下几点就行：

协议：vless

添加用户：flow选择xtls-rprx-vision

勾选reality，目标网站与可选域名填上之前筛选的网站

# 三、一键端

最为推荐的一种方式，简单方便不费心，而且可以很无脑的生成sing-box，clash-meta，v2rayN，nekoray等通用链接格式。

hysteria 2 +reality + vmess 一键脚本：

```sh
bash <(curl -fsSL https://github.com/liuoqu444/sing-box-reality-hysteria2/raw/main/reality_hy2_ws.sh)
```

关闭防火墙

```sh
sudo ufw disable 
```

**clash verge下载：**https://github.com/zzzgydi/clash-verge/releases

替换clash-meta.exe：https://github.com/MetaCubeX/Clash.Meta/releases

------

**v2rasyn下载：**https://github.com/2dust/v2rayN/releases

内核替换：https://github.com/apernet/hysteria/releases/

# 失败部分



这部分做了半天没成功，放弃了

最后选择使用<font color=#00FFFF>Vless + Reality协议 + Vision流控</font>



### 域名

还是主打性价比，<font color=#00FFFF>namesilo</font>六个数字的XYZ结尾域名1美元搞定

后面听说<font color=#00FFFF>spaceship</font>也很便宜



### cdn中转

需要先将域名托管到<font color=#00FFFF>Cloudflare</font>平台，可以帮助我们隐藏自己的真实IP地址，防止被墙。

在注册完<font color=#00FFFF>Cloudflare</font>账号后，登录点击添加站点，输入我们注册的域名

下面的计划选择免费就好，激活站点后把我们域名的DNS改成提供的DNS

修改之后大概半小时左右就会生效，邮箱会接到邮件，同时再回到<font color=#00FFFF>Cloudflare</font>会显示：

**好消息！Cloudflare 正在保护您的站点**

这时就托管成功了，可以在DNS这栏选择添加二级域名，前面随意，把小黄云关掉就是停止中转，这时ping地址是原始ip，开启的话就会看到Cloudflare的地址了。

在SSL/TLS中把灵活改成完全就好了，中转设置完毕。

> [NameSilo域名解析和DNS服务器修改教程](https://blog.naibabiji.com/tutorial/namesilo-yu-ming-jie-xi-geng-huan-dns-jiao-cheng.html)
>
> [Racknerd控制面板使用教程](https://www.pigji.com/875.html)