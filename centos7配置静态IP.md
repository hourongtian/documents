> CentOS7最小化安装后是没有 `ifconfig` 命令的, 所以说你是没有办法通过 `ifconfig` 查看网卡相关配置信息的, 不好意思说了一句废话, 那么CentOS这个那么有情怀的社区linux发行版本怎么可以在通过最小化安装后不能查看网卡配置信息, 这个必须有它就是 `ip` 命令

```
[root@localhost~]# ip addr
```

> 通过 `ip` 命令加参数 `addr` 则是查看当前网卡的配置信息, 从下图中可以看出, 当前系统的 `ens33` 网卡并没有 `ipv4` 及 `ipv6`, 没有 `ip地址` 则只能说明一个问题, 当前linux系统是没有办法上网的, `lo` 这块网卡每一个系统中都会有, 这个称之为 `回环网卡`

![ip addr](https://segmentfault.com/img/remote/1460000018192942?w=861&h=202)

> 当前系统只能通过 `ping` 命令 `ping` 通 `回环网卡` 也就是 `127.0.0.1` 这个 `ip地址`

```
[root@localhost ~]# ping 127.0.0.1
```

![ping 127.0.0.1](https://segmentfault.com/img/remote/1460000018192943)

## CentOS7默认网卡配置信息

> CentOS7默认网卡设备文件存放于

```
/etc/sysconfig/network-scripts/
```

> 目录下, 有于CentOS的发行及系统升级或许可能会存在网卡设备名称与之前版本不一致的情况, 这也很容易理解, 网卡设备名称在linux系统中它只是一个标志, 它并不能说明什么问题, 关键是这个设备文件的配置参数, 我当前CentOS7的网卡设备名称是

```
ifcfg-ens33
```

> 如果你看到你的linux发行版本中的网卡设备名称与我的不一致, 这太正常了, 不要问为什么, 改变嘛, 改变就是希望！ (^-^), 这里只是说的linux的发行版本CentOS, 我可没说过是Ubuntu, 为什么说是CentOS呢？我们都知道在企业应用中linux的发行版本用的最多的还是 `RedHat`, 不过 `RedHat` 要收费了哈, 为什么呢？这是人家服务做的好嘛！再小一点的公司可能最多的用的是 `CentOS`, 为什么要用 `CentOS` 呢？这是因为 `RedHat` 把 `CentOS` 给收购了, `CentOS` 现在叫社区版本, `RedHat` 在做服务方面, 是先将服务放到社区版本 `CentOS` 上, 通过社区反馈, 当技术足够成熟时, 就会将新技术或者服务添加到 `RedHat` 中去, 所以用 `CentOS` 其实也就是间接的在使用 `RadHat` 啦！就是这个样子了, 当然你可以去百度一下去了解了解, 就不说让你Google了，在天国真是上网都有些力不从心呢，你懂就好！哈哈...

```
[root@localhost ~]# cat /etc/sysconfig/network-scripts/ifcfg-ens33
```

> 通过 `cat` 查看CentOS7最小化安装默认网卡设备配置信息, 如下:

```
TYPE=Ethernet                # 网卡类型：为以太网
PROXY_METHOD=none            # 代理方式：关闭状态
BROWSER_ONLY=no                # 只是浏览器：否
BOOTPROTO=dhcp                # 网卡的引导协议：DHCP[中文名称: 动态主机配置协议]
DEFROUTE=yes                # 默认路由：是, 不明白的可以百度关键词 `默认路由` 
IPV4_FAILURE_FATAL=no        # 是不开启IPV4致命错误检测：否
IPV6INIT=yes                # IPV6是否自动初始化: 是[不会有任何影响, 现在还没用到IPV6]
IPV6_AUTOCONF=yes            # IPV6是否自动配置：是[不会有任何影响, 现在还没用到IPV6]
IPV6_DEFROUTE=yes            # IPV6是否可以为默认路由：是[不会有任何影响, 现在还没用到IPV6]
IPV6_FAILURE_FATAL=no        # 是不开启IPV6致命错误检测：否
IPV6_ADDR_GEN_MODE=stable-privacy            # IPV6地址生成模型：stable-privacy [这只一种生成IPV6的策略]
NAME=ens33                    # 网卡物理设备名称
UUID=f47bde51-fa78-4f79-b68f-d5dd90cfc698    # 通用唯一识别码, 每一个网卡都会有, 不能重复, 否两台linux只有一台网卡可用
DEVICE=ens33                    # 网卡设备名称, 必须和 `NAME` 值一样
ONBOOT=no                        # 是否开机启动， 要想网卡开机就启动或通过 `systemctl restart network`控制网卡,必须设置为 `yes` 
```

## CentOS7配置网卡为静态IP

> 上面说了那么多就是为了下面配置网卡一哆嗦！以下操作是通过 `vi` 编辑器所做修改, 老司机会说了, 你怎么不用 `vim`, 这个比 `vi` 更个性, 你呀也别叨叨了, 你是没看到怎么的, 咱这可是最小化安装方式, 啥也没有, 嗯不对, 是最基础的有, 想玩花样, 那可得安装, 要安装那不得先连网, 现在不就是架桥铺路的吗？都到这个地步了, 你可别跟我说你不会用 `vi`, 别闹哈洗洗睡吧！

```
[root@localhost ~]# vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

> 使用 `vi` 文本编辑器打开网卡设备文件, `vi` linux 下的神器, 不多说谁用谁知道, 没用过的别叨叨哈, 进入插入模式, 这个知道吧, 就是你的小手指按 `i`, 修改具体配置项为以下内容：

```
BOOTPROTO=static
```

> 设置网卡引导协议为 `静态`

```
ONBOOT=yes
```

> 设置网卡启动方式为 `开机启动` 并且可以通过系统服务管理器 `systemctl` 控制网卡

```
IPADDR=192.168.1.111
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
```

> 上面是最主要的3个配置项[IP/子网掩码/网关], 配置完成这些参数先保存退出, 按 `esc` 进入到 `命令模式`, 按住 `shift + :` 输入 `:`, 接着再输入 `wq`, `w` 是 `保存`, `q` 就是告退喽, 你可能会说, 你输入的 `:wq` 多麻烦呢, 直接来一个 `:x` 命令多好啊, 其实吧说实话我从来就不用这个指令, 为什么呢? 原因很很简单, 那就是这个指令它不够见名知意, 要记住它你得跟 `wq` 联想到一块吧,这在无形中就会增加你学习的成本, 我们要学习的东西很多, 你要这么学你得累死, 这是个人见解, `w` 就是 `write`, 那么 `q` 就是 `quit`, 这些单词你都学过吧, 学过就不用额外记其它的喽, so...

```
[root@localhost ~]# systemctl restart network
```

> 重启网卡, 说明一点, 在 `CentOS7` 下已全面使用 `systemctl` 作为服务管理器, 它用来替代 `service 和 chkconfig`, 现在 `RadHat`、`Ubuntu`、`fedora` 等主流的 `linux` 发行版本都以全面支持 `systemctl` 服务管理器, 这可是一个好事情！皆大欢喜

## 获取设置具体IP/子网掩码/网关地址说明

> 设置这3个参数你要知道你当前所处的网络环境, 比如公司内网, 家庭局域网, 餐厅, 咖啡厅等, 知道它们的网段,子网及网关, 就比如说我在家里上网有台小路由器吧, 同时可以有多台设备上网iPhone,MAC,MI,ThinkPad等等, 现在谁家还没有路由器, 你可别说你开了一宽带就直接往电脑上一插, 然后开热点, 你可真会过, 我真没见过你这样式的，一个字 `抠门`, 没有路由器的小伙伴可以闭嘴了哈, 开个玩笑而已, 你可真要当真了哈, 哈哈...

![MAC 网卡配置信息](https://segmentfault.com/img/remote/1460000018192944)

> 上图是我的Mac无线网卡的配置信息, 这个配置信息, 你不用操心太多, 这是Mac通过DHCP协议从无线路由器上获取到的, 你可能会说我用的是Windows, 怎么和你的显示不太一样, 不太一样是吧, 这也一点问题都没有, 没毛病, 要不然Mac也成了Windows操作系统了, 这Mac可真是买亏了, 说一下上面红线圈住的具体含义

```
inet 192.168.1.100
```

> 就是局域网IP地址啦！Mac是 `192.168.1.100` 那么上面的 `CentOS` 就不能是这个地址了, 记住了哈, IP是唯一的, 就像你的身份证ID一样, 每个人只有一个, 你跑到国外也不会有人跟你一样, 要不然那还得了, 别人犯事了, 来抓你了, 你能愿意? 呵呵....我不信！

```
netmask 0xffffff00
```

> 这个是子网掩码, 它是用`16`进制表示的, 这可能与我们常常使用的Windows有些不一样, 并且还略显怪异, 我们都知道, IP地址是用 `32` 位表示的, 分为 `4` 段, 每段 `8` 位, 这 `8` 位呢！是 `0` 或 `1` 二进制组合来表示的, 那么转化为 `10` 进制, 它每段的取值就是 `0-255` 所示每段最小是 `0` 最大是 `255`, 这种表示方法在Windows上就是使用的 `10` 进制, 在 `Mac` 下如果存在最小 `0` 或 最大 `255` 它就会转化为 `16` 进制, 如果说当前的子网掩码是 `255.255.255.0`, 这是Windows下的 `10` 进制表示, 那到了 `Mac` 下 `Mac` 系统的研发工程师觉得老板发薪资了, 也不能闲着的时候没事干, 非得将 `10` 进制转换成 `16` 进制就成了我们现在看到的这个样子了 `0xffffff00`, 你就可劲转吧, 我们又不是转不回来, 小样, 哈哈, 博主是猴子请来的...

```
broadcast 192.168.1.255
```

> 这个呢是 `广播地址`, 主要的网络设备有 `路由器`、`防火墙`, `具有三层交换功能的网络交换机`及以 `通过软件开启了路由功能的主机`, 这个地址呢！可能会与IP不是一个网段, 这个要看局域网网络设备多少, 公司内部一般是不会跟IP地址网段一样, 还是那句话, 具体情况具体分析, 你可别较真哈, 像我在家里上网肯定就是一台无线路由器就搞定的事情, 那么这个 `广播地址`, 肯定是跟IP地址在一个网段, 最大 `10` 进制值表示这个 `广播地址` 也就是 `192.168.1.255`, 网卡中设置的是网关地址, 那么这个IP网段的第一个就是网关地址, 也就是 `192.168.1.1` 这个地址

## 配置以上网卡信息后当前网卡状态

```
[root@localhost ~]# ip addr
```

![当前网卡状态](https://segmentfault.com/img/remote/1460000018192945)

> 可以看到 `ens33` 这块网卡, IP/网关, 还有ipv6这个现阶段还暂时用不到, 甭操心! 但是你会发现你看不到 `子网掩码` 设置具体值, 这个安装上 `net-tools` 工具包, 就可以通过 `ifconfig` 来查看网卡配置信息了, 来接着往下看

------

> 到目前为止, 上面配置的网卡信息也只能是在内网里得瑟, 上不了网, 为什么这么说呢, 要不你试试看, 因为还有一个知识点要说, 先来试试看是不是像我说的那样, 下面做一个测试就一目了然了
>
> ### PING内网其它主机
>
> ```
> [root@localhost ~]# ping 192.168.1.100
> ```
>
> ![PING内网其它主机](https://segmentfault.com/img/remote/1460000018192946)
>
> > 这个 `ping` 的是我的Mac, 可以 `ping` 通, 没有任何问题！
>
> ### PING外网其它主机
>
> ```
> [root@localhost ~]# ping baidu.com
> ```
>
> ![PING外网其它主机](https://segmentfault.com/img/remote/1460000018192947)
>
> > 这个 `ping` 的是位于西二旗的在 `百度`, 出现 `未知域名或服务`！shit!
>
> ## 允许网卡访问外网主机
>
> > 为什么上不去网呢？这是因为当前 `CentOS` 还解析不了输入的 `baidu.com` 这个字符串, 我知道它是百度的域名, 你也知道, 但它就是不知道这是百度域名, 它解析 `baidu.com` 域名到底是对应哪一IP地址, 那好了, 去Mac下 `ping` 一下 `baidu.com` 这个域名, 给它加到 `CentOS` 系统的 `hosts` 文件这事也就靠谱了
>
> ![Mac下ping百度](https://segmentfault.com/img/remote/1460000018192948)
>
> > 从结果上来看百度的IP地址就是 `220.181.57.217` 这个IP了, 没错就是它!
>
> ### 编辑 `/etc/hosts` 文件
>
> > 打开 `/etc/hosts` 文件
>
> ```
> [root@localhost ~]# vi /etc/hosts
> ```
>
> > 以下呢！是初始化安装 `CentOS7` 后默认的 `hosts` 文件具体内容
>
> ![hosts-defalut-content](https://segmentfault.com/img/remote/1460000018192949?w=961&h=135)
>
> > 输入以下IP与域名的对应记录, 保存退出
>
> ```
> 220.181.57.217 baidu.com www.baidu.com
> ```
>
> ![修改hosts文件](https://segmentfault.com/img/remote/1460000018192950)
>
> > 现在再去 `ping` 百度的域名 `baidu.com` 肯定没有问题！
>
> ```
> [root@localhost ~]# ping baidu.com
> ```
>
> ![img](https://segmentfault.com/img/remote/1460000018192951)
>
> > 那好, 你去 `ping` 一下 `淘宝` 的域名呗！好啊！
>
> ```
> [root@localhost ~]# ping taobao.com
> ```
>
> ![PING淘宝](https://segmentfault.com/img/remote/1460000018192952)
>
> > 咦！dammit, 怎么又不行了, 难道我要 `ping` 通 `淘宝` 的域名还要再去 `hosts` 中添加对应的IP与域名的关系, 这还不是一个噩梦！要是没有 `域名解析服务器` 你说这个世界会是什么样子, 给你5分钟的考虑时间, 哈哈, 通过这个过程呢, 就是想让那些不是太明域名解析的小伙伴们理解逶了, 其实 `ping` 命令使用到了 `ICMP` 协议, 它是TCP/IP协议集中一个子协议，属于TCP/IP参考模型网络层协议, 你可以去复习网络这门课了, 说多了都是罪过!
>
> ### 配置网卡设备文件添加DNS域名解析服务器地址
>
> > 添加DNS域名解析服务器地址, 就是让 `CentOS7` 在使用 `ping` 命令时, 一看给的是一个字串符而非IP地址, 就通过 `域名解析服务器` 查找对应的IP地址, 然后通过IP去 `ping` 对应的主机这事也就妥妥的了, 简单的说明这个问题它就是这样, 要想知道它具体都干什么事情了, 你还得去研究一下 `网络参考模型` 以及 `协议` 这些事！
>
> ------
>
> #### Windows获取DNS服务器地址
>
> > 那你可能会说了, 我去哪找 `域名解析服务器` 地址呢？这个很简单, 如果你是windows用户你可以通过在 `cmd` 命令行下输入：
>
> ```
> C:\Windows\System32> ipconfig /all
> ```
>
> ![DNS](https://segmentfault.com/img/remote/1460000018192953)
>
> #### Mac用户获取DNS服务器地址
>
> > 如果你是Mac用户则可以通过, 界面的形式获取DNS服务器地址, 因为在终端下是显示不出来的, 如果你有办法可以在终端下显示出来请不要吝啬给我留言好了,谢谢！
>
> ```
> 系统偏好设置->网络->高级->DNS
> ```
>
> ![mac下查看DNS服务器地址](https://segmentfault.com/img/remote/1460000018192954)
>
> #### 修改网卡设备配置文件
>
> > 添加DNS服务器地址到网卡设备配置文件, 添加结果如下所示
>
> ```
> DNS1=127.207.160.106
> DNS2=219.239.26.42
> ```
>
> > 保存退出, 重启网卡设备
>
> ```
> [root@localhost ~]# systemctl restart network
> ```
>
> > 现在你再去 `ping` 淘宝的域名 `taobao.com` 就是OK!
>
> ```
> [root@localhost ~]# ping taobao.com
> ```
>
> ![PING淘宝域名](https://segmentfault.com/img/remote/1460000018192955)
>
> ## 最终网卡设备配置详情表
>
> ```
> TYPE=Ethernet
> PROXY_METHOD=none
> BROWSER_ONLY=no
> BOOTPROTO=static
> DEFROUTE=yes
> IPV4_FAILURE_FATAL=no
> IPV6INIT=yes
> IPV6_AUTOCONF=yes
> IPV6_DEFROUTE=yes
> IPV6_FAILURE_FATAL=no
> IPV6_ADDR_GEN_MODE=stable-privacy
> NAME=ens33
> UUID=f47bde51-fa78-4f79-b68f-d5dd90cfc698
> DEVICE=ens33
> ONBOOT=yes
> IPADDR=192.168.1.111
> NETMASK=255.255.255.0
> GATEWAY=192.168.1.1
> DNS1=127.207.160.106
> DNS2=219.239.26.42
> ```
>
> ## 安装辅助工具包
>
> ### 安装 `wget` 下载工具
>
> ```
> [root@locahost ~] yum install -y wget
> ```
>
> - -y 参数是遇到询问时一侓同意
>
> > wget 安装完成后你就可以通过它下载你想下载的内容了, 比如你要下载一账MM图片, 就可以这么做:
>
> - 找到想要下载的MM图片资源
>
> ![获取资源地址](https://segmentfault.com/img/remote/1460000018192956)
>
> - 下载资源下本地
>
> ```
> [root@locahost ~]# wget -O duanfamm.jpg -c 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1510489141277&di=10bdf080b2d210fa55b5874f84dab96a&imgtype=0&src=http%3A%2F%2Fp3.wmpic.me%2Farticle%2F2016%2F06%2F29%2F1467184706_GzGPwLch.jpg'
> ```
>
> ![下载资源下本地](https://segmentfault.com/img/remote/1460000018192957)
>
> > 在上图的
> >
> >  
> >
> > ```
> > terminal
> > ```
> >
> >  
> >
> > 中可以看到图片下载成功并重命名, 关于更多
> >
> >  
> >
> > ```
> > wget
> > ```
> >
> >  
> >
> > 用法的详情请参考:
> >
> >  
> >
> > wget使用详情
> >
> > 参数说明：
>
> - -O 下载到指定目录
> - -c 断点续传
>
> ### 安装 `net-tools` 工具包
>
> > `net-tools` 工具包, 包含以下命令：arp, hostname, ifconfig, ipmaddr, iptunnel, mii-tool, nameif, netstat, plipconfig, rarp, route und slattach. 关于 `net-tools` 更多的详情信息请参考: [net-tools详情](http://net-tools.sourceforge.net/)
>
> ------
>
> > 当然了话又话说回来，如果你也不知道使用的工具命令在哪个包下, 可以通过下面的方式去获得包名, 通过包名去安装, 就可以使用你想要使用的工具命令了, 就好比说你现在要使用 `ipconfig` 这个命令
>
> ```
> [root@localhost ~]# ifconfig
> ```
>
> > 可能会得到这么一个结果：
>
> ![command not found](https://segmentfault.com/img/remote/1460000018192958)
>
> > 那么你可能使用通过 `yum` 软件包管理工具进行安装：
>
> ```
> [root@localhost ~]# yum install -y ifconfig
> ```
>
> > 很不幸你会得到这么一个结果:
>
> ![img](https://segmentfault.com/img/remote/1460000018192959)
>
> > 没有可用的 `ifconfig` 包, 那这个时候你就要通过:
>
> ```
> yum whatprovides package_name
> ```
>
> > 来查找包名了, 如现在要查找 `ifconfig` 这个命令, 具体包含在哪个包里就可以这么做:
>
> ```
> [root@localhost ~]# yum whatprovides ifconfig
> ```
>
> ![No matches found](https://segmentfault.com/img/remote/1460000018192960)
>
> > 你可能会看到这样的结果, 你觉得我在逗你玩呢？我可没有那功夫哈! 原因是CentOS在 `yum` 源在国内使用非常不稳定, 建议更新为国内的 `yum` 源, 比如 `阿里` 或是 `网易`
>
> ### 更新 `yum` 源
>
> > 更新 `CentOS7` 默认 `yum` 源, 需要以下几个步骤:
>
> - 备份当前系统默认 `yum` 源配置文件
>
> ```
> [root@localhost ~]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
> ```
>
> - 查看当前 `CentOS7` 版本号
>
> ```
> [root@localhost ~]# cat /etc/centos-release
> ```
>
> ![cat /etc/centos-release](https://segmentfault.com/img/remote/1460000018192961?w=938&h=100)
>
> - 到阿里云开源镜像站找到对应的 `yum` 源版本，详情参考: [阿里yum源](http://mirrors.aliyun.com/help/centos) [网易yum源](http://mirrors.163.com/.help/centos.html)
>
> ```
> [root@localhost ~]# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
> ```
>
> - 清除所有yum源缓存文件
>
> ```
> [root@localhost ~]# yum clean all
> ```
>
> - 生成yum源缓存
>
> ```
> [root@localhost ~]# yum makecache
> ```
>
> > 现在的话你就可以通过：
>
> ```
> [root@localhost ~]# yum whatprovides ifconfig
> ```
>
> > 找到是 `net-tools` 这个包啦！
>
> ```
> [root@localhost ~]# yum install -y net-tools
> ```
>
> ### 使用 `ifconfig` 查看网卡配置信息
>
> ```
> [root@localhost ~]# ifconfig
> ```
>
> ![ifconfig查看网卡配置信息](https://segmentfault.com/img/remote/1460000018192962)
>
> > 从上图可以看出：
>
> - ip: 192.168.1.111
> - 子网掩码: 255.255.255.0
> - 广播地址：192.168.1.255
> - 网关地址：192.168.1.1 [这个没有显示出来]
>
> > 你记住一点就好了, 如果是一台路由, 那么 `192.168.1.2 ~ 192.168.1.254` 是可用的, 剩下的呢, `192.168.1.1` 就是网关地址, `192.168.1.255` 就是广播地址, 在网卡配置时, 广播地址是你不用配置的, 你要配置的是网关地址/IP/子网掩码, 就酱！