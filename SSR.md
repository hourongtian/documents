![img](https:////note.youdao.com/src/A272DA0189B94FE4A85592168E551D0E)

连接成功后，会出现如上图所示，之后就可以复制粘贴代码部署了。

CentOS6 / Debian6 / Ubuntu14 ShadowsocksR一键部署管理脚本：



yum -y install wget

wget -N --no-check-certificate https://raw.githubusercontent.com/hombo125/doubi/master/ssr.sh && chmod +x ssr.sh && bash ssr.sh

备用脚本：

yum -y install wget

wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh && chmod +x ssr.sh && bash ssr.sh

-------------------代码分割线----------------

复制上面的代码到VPS服务器里，按回车键，脚本会自动安装，以后只需要运行这个快捷命令就可以出现下图的界面进行设置，快捷管理命令为：bash ssr.sh

![img](https:////note.youdao.com/src/3AAE7007905A4C70A6A935D26A08C607)

如上图出现管理界面后，输入数字1来安装SSR服务端。如果输入1后不能进入下一步，那么请退出xshell，重新连接vps服务器，然后输入快捷管理命令bash ssr.sh再尝试。

![img](https:////note.youdao.com/src/2498EAC428A348358528566AE22FE3AE)

根据上图提示，依次输入自己想设置的端口和密码（密码建议用复杂点的字母组合，端口号为40-65535之间的数字），回车键用于确认

注：关于端口的设置，总的网络总端口有6万多个，理论上可以任意设置但是有的地区需要设置特殊的端口才有效，一些特殊的端口比如80,143,443,1433,3306,3389,8080。

![img](https:////note.youdao.com/src/676ABBB7ABC94527BC8F8B3496A8FA35)

如上图，选择想设置的加密方式，比如10，按回车键确认

接下来是选择协议插件，如下图：

![img](https:////note.youdao.com/src/A585AE87116E4388AE83E0D4C4A0B171)

![img](https:////note.youdao.com/src/E3E989740148442F8C5149955D225CF2)

选择并确认后，会出现上图的界面，提示你是否选择兼容原版，这里的原版指的是SS客户端（SS客户端没有协议和混淆的选项），可以根据需求进行选择，演示选择ÿ

之后进行混淆插件的设置。

注意：。如果协议是原点，那么混淆也必须是纯;如果协议不是原点，那么混淆可以是任意的有的地区需要把混淆设置成平纹才好用因为混淆不总是有效果，要看各地区的策略，有时候不混淆（plain）让其看起来像随机数据更好。（特别注意：tls 1.2_ticket_auth容易受到干扰！请选择除tls开头以外的其他混淆!!!）

![img](https:////note.youdao.com/src/CB03494B8D5048489CB5638887B318CA)

进行混淆插件的设置后，会依次提示你对设备数，单线程限速和端口总限速进行设置，默认值是不进行限制，个人使用的话，选择默认即可，即直接敲回车键。

注意：关于限制设备数，这个协议必须是非原版且不兼容原版才有效，也就是必须使用SSR协议的情况下，才有效！

![img](https:////note.youdao.com/src/20C7C938E2A647EFA700A095921F2506)

之后代码就正式自动部署了，到下图所示的位置，提示你下载文件，输入为：y

![img](https:////note.youdao.com/src/CE7B2C43DDFA4B5C95025339FCF6B4F6)

耐心等待一会，出现下面的界面即部署完成：

![img](https:////note.youdao.com/src/30F2CAC766A1404A96F1920D06D06B70)

![img](https:////note.youdao.com/src/80FF3FCF86744BDD9CBA9A13BB793AB8)

根据上图就可以看到自己设置的SSR账号信息，包括IP，端口，密码，加密方式，协议插件，混淆插件，这些信息需要填入你的SSR客户端。如果之后想修改账号信息，直接输入快捷管理命令：bash ssr.sh进入管理界面，选择相应的数字来进行一键修改。例如：

![img](https:////note.youdao.com/src/CF84B2EEF9F14F6096216284C70E5F39)

![img](https:////note.youdao.com/src/C316AD1830064E5E96512424589393DF)

脚本演示结束。

此脚本是开机自动启动，部署一次即可。最后可以重启服务器确保部署生效（一般情况不重启也可以）。重启需要在命令栏里输入reboot，输入命令后稍微等待一会服务器就会自动重启，一般重启过程需要2〜5分钟，重启过程中XSHELL会自动断开连接，等VPS重启好后才可以用XSHELL软件进行连接。如果部署过程中卡在某个位置超过10分钟，可以用XSHELL软件断开，然后重新连接你的IP，再复制代码进行部署。



第三步：一键加速VPS服务器

此加速教程为谷歌BBR加速，Vultr的服务器框架可以装BBR加速，加速后对速度的提升很明显，所以推荐部署加速脚本。该加速方法是开机自动启动，部署一次就可以了。

按照第二步的步骤，连接服务器ip，登录成功后，在命令栏里粘贴以下代码：

【谷歌BBR加速教程】

yum -y install wget

wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh

chmod +x bbr.sh

./bbr.sh

把上面整个代码复制后粘贴进去，不动的时候按回车，然后耐心等待，最后重启vps服务器即可。

演示开始，如图：

复制并粘贴代码后，按回车键确认

![img](https:////note.youdao.com/src/B0742E154FAD4D1B9FC9C728A786C2B1)

如下图提示，按任意键继续部署

![img](https:////note.youdao.com/src/5756280C260740EDAF4EB551E901E0AC)

![img](https:////note.youdao.com/src/C688BD4785A0466CBDA336F84B5C12DB)

部署到上图这个位置的时候，等待3～6分钟

![img](https:////note.youdao.com/src/39165A828DA44746A3A5B5CA3E741783)

最后输入y重启服务器，如果输入y提示command not found ，接着输入reboot来重启服务器，确保加速生效，bbr加速脚本是开机自动启动，装一次就可以了。

服务器重启成功并重新连接服务器后，输入命令lsmod | grep bbr 如果出现tcp_bbr字样表示bbr已安装并启动成功。如图：

![img](https:////note.youdao.com/src/0E25D9B831E946B4A037E17EE9733E36)



购买vps服务器后，ip有了，通过部署，端口、密码、加密方式、协议和混淆也有了，最后将这些信息填入SSR客户端就可以翻墙啦。

有了账号后，打开SSR客户端，填上信息，这里以windows版的SSR客户端为例子：

![img](https:////note.youdao.com/src/2F9E3B3F67D34912A0201A9D0591E80F)

在对应的位置，填上服务器ip、服务器端口、密码、加密方式、协议和混淆，最后将浏览器的代理设置为（http）127.0.0.1和1080即可。账号的端口号就是你自己设置的，而要上网的浏览器的端口号是1080，固定的，谷歌浏览器可以通过 SwitchyOmega 插件来设置。

启动SSR客户端后，右键SSR客户端图标，选择第一个“系统代理模式”，里面有3个子选项，选择"全局模式“，之后就可以用浏览器设置好了的代理模式（http）127.0.0.1和1080翻墙，此模式下所有的网站都会走SSR代理。（适合新手）

![img](https:////note.youdao.com/src/402A814610C44173A3D76762619DD0E6)



常见问题参考解决方法：

1、用了一段时间发现ssr账号用不了了

首先ping一下自己的ip，看看能不能ping的通，ping不通那么就是ip被墙了，ip被墙时，xshell也会连接不上服务器，遇到这种情况重新部署一个新的服务器，新的服务器就是新的ip。关于怎么ping ip的方法，可以自行网上搜索，或者用xshell软件连接服务器来判断，连不上即是被墙了。vultr开通和删除服务器非常方便，新服务器即新ip，大多数vps服务商都没有这样的服务，一般的vps服务商可能会提供免费更换1次ip的服务。n

2、刚搭建好的ssr账号，ip能ping通，但是还是用不了

首选排除杀毒软件的干扰，尤其是国产杀毒软件，比如360安全卫生、360杀毒软件、腾讯管家、金山卫生等。这些东西很容易干扰翻墙上网，如果你的电脑安装了这样的东西，建议至少翻墙时别用，最好卸载。其次，检查下SSR信息是否填写正确。浏览器的代理方式是否是ssr代理，即（HTTP）127.0.0.1 和1080。如果以上条件都排除，还是用不了，那么可以更换端口、加密方式、协议、混淆，或者更换服务器位置。另外，如果你的vps服务器配置的是SSR账号，即有协议和混淆且没有兼容原版(SS版），那么你必须使用SSSR客户端来使用账号，因为SS客户端没有填写协议和混淆的选项。

3、有的地区需要把混淆参数设置成plain才好用。因为混淆不总是有效果，要看各地区的策略，有时候不混淆（plain）让其看起来像随机数据更好。

4、电脑能用但手机用不了

如果你的手机用的是SS客户端，SS客户端没有填协议和混淆的地方，如果你部署的协议和混淆的时候没有选择兼容原版（SS版），因此手机是用不了的。这个时候你把协议弄成兼容原版、混淆也设置成兼容原版即可。或者直接将协议设置成origin且混淆设置成plain。

5、vps的服务器操作系统不要用的太高，太高可能会因为系统的防火墙问题导致搭建的SSR账号连不上，如果你用的centos系统，建议用centos6，不要用centos7。如果你前面不小心装了centos7系统，那么只能重装系统或者重新部署新的vps服务器。

6、vultr服务商提供的vps服务器是单向流量计算，有的vps服务商是双向流量计算，单向流量计算对于用户来说更实惠。因为我们是在vps服务器上部署SSR服务端后，再用SSR客户端翻墙，所以SSR服务端就相当于中转，比如我们看一个视频，必然会产生流量，假如消耗流量80M，那么VPS服务器会产生上传80M和下载80M流量，vultr服务商只计算单向的80M流量。如果是双向计算流量，那么会计算为160M流量。

7、如果你想把搭建的账号给多人使用，不用额外设置端口，因为一个账号就可以多人使用。一般5美元的服务器可以同时支持40人在线使用。

如果想实现支持每个用户(端口)不同的加密方式/协议/混淆等，并且管理流量使用，可以参考多用户配置脚本：wget -N --no-check-certificate https://raw.githubusercontent.com/hombo125/doubi/master/ssrmu.sh && chmod +x ssrmu.sh && bash ssrmu.sh 备用脚本：wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssrmu.sh && chmod +x ssrmu.sh && bash ssrmu.sh 安装后管理命令为：bash ssrmu.sh

注意：这个多用户配置脚本和教程内容的脚本无法共存！要想用这个脚本，把之前的脚本卸载，输入管理命令bash ssr.sh ，选择3，卸载ShadowsocksR即可卸载原脚本。

8、vultr服务器每月有流量限制，超过限制后服务器不会被停止运行，但是超出的流量会被额外收费。北美和西欧地区的服务器超出流量后，多出的部分收费为0.01美元/G。新加坡和日本东京（日本）为0.025美元/G，悉尼（澳大利亚）为0.05美元/G。把vultr服务器删掉，开通新的服务器，流量会从0开始重新计算。

9、vultr怎样才能申请退款呢？

与其他国家商家一样输入你的退款理由。比如：请退还我账户中的所有余额。工单提交以后一般很快就可以给你确认退款，若干个工作日后就会退回你的支付方式。（全额退）款结束后，账号可能会被删除）

如果英语水平不好，但是想和客服进行交流，可以用百度在线翻译，自动中文转英文和英文转中文。

10，路由器也可以配置ss / ssr账号，详见openwrt-ssr项目地址：https://github.com/ywb94/openwrt-ssr

## 流量伪装

客户端

混淆协议：tls1.2_ticket_auth

混淆参数：HK2SCH130083c18593.wns.windows.com



```
{                                                                                                   
    "server": "0.0.0.0",
    "server_ipv6": "::",
    "server_port": 447,
    "local_address": "127.0.0.1",
    "local_port": 1080,

    "password": "901112rongtian",
    "method": "aes-256-cfb",
    "protocol": "origin",
    "protocol_param": "",
    "obfs": "tls1.2_ticket_auth",
    "obfs_param": "",
    "speed_limit_per_con": 0,
    "speed_limit_per_user": 0,

    "additional_ports" : {},
    "timeout": 120,
    "udp_timeout": 60,
    "dns_ipv6": false,
    "connect_verbose_info": 0,
    "redirect": ["*:446#HK2SCH130083c18593.wns.windows.com:443"],
    "fast_open": false
}
```

