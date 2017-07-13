# -
<img src="http://wx4.sinaimg.cn/mw690/8cb7c87ely1fhielh28rwj20pw074glq.jpg" /><br/>
比如你想访问谷歌，直接访问的话通过防火墙的时候会被拦下来。你用Shadowsocks把你的真实目的地隐藏起来，告诉防火墙我只是去访问一个VPS，防火墙放行了，流量到了VPS之后，VPS替你去访问谷歌，再把谷歌返回的结果用Shadowsocks包起来还给你。<br/>
# -步骤
# 一，领优惠券
Github给学生准备了一个大礼包，链接在此：https://education.github.com/pack
在大礼包中找到Digital Ocean的50刀代金券
# 二、注册Digital Ocean
Digital Ocean是一个虚拟主机提供商，就跟AWS、阿里云、腾讯云一样，性价比较高，还有GitHub的大礼包，所以大家就用它吧。当然还有搬瓦工、linode之类，大家可以自己尝试。<br/>
Digital Ocean官网：https://www.digitalocean.com/<br/>
注册需要绑定支付，推荐绑定paypal，然后支付5美元激活账号。之后在Setting里的Billing下面找到Promo Code，填进去你Github给的优惠码。<br/>
这样你账号里应该是55美刀，用最便宜的5刀一个月虚拟机，你可以用11个月。恩，5美元11个月，这波不亏。<br/>
注意，一个人只能用一个优惠码，所以不要填别的码了。<br/>
# 三、创建虚拟机
Digital Ocean里的虚拟机叫Droplet，数字海洋里的一滴水，是不是很贴切。<br/>
5美元的虚拟机足够平时科学上网用了<br/>
镜像请选择Ubuntu16，选其他的请自己琢磨怎么敲命令行。<br/>
旧金山节点（SF）延迟应该是最低的，然而依然200多，但是最近SF的节点好像上不去谷歌学术，我目前在用新加坡节点。<br/>
推荐勾上IPv6服务，这样你的虚拟机就可以同时拥有V4V6两个地址，你可以实现：<br/>
1.在任意能上网的地方通过代理访问IPv6站点，例如北邮人（六维屏蔽了境外IP，所以不行）。<br/>
2.在校园网内能接入IPv6的地方免流量上网（如果你的学校IPv6流量是免费的）。<br/>
做到1很简单，只要你勾选IPv6，然后正常设置就可以实现，2的具体方法我后面会讲。<br/>
虚拟机创建完后你会收到一封邮件，里面有你虚拟机的密码，然后就SSH上去操作吧。<br/>
# 四、部署Shadowsocks服务端
在Ubuntu16的环境下输入以下命令：<br/>
apt-get update<br/>
apt-get install python-pip<br/>
pip install --upgrade pip<br/>
pip install shadowsocks<br/>
接下来创建配置文件<br/>
vi /etc/ss4.json<br/>
内容如下：<br/>
{<br/>
    "server":"你虚拟机的IPv4 IP",<br/>
    "server_port":8388,<br/>
    "local_address":"127.0.0.1",<br/>
    "local_port":1080,<br/>
    "password":"你的密码",<br/>
    "timeout":300,<br/>
    "method":"aes-256-cfb",<br/>
    "fast_open":false<br/>
}<br/>
其中server_port可以自己修改，不要和常用端口冲突即可。<br/>
然后启动ss服务：<br/>
ssserver -c /etc/ss4.json -d start --pid-file ss1.pid<br/>
服务器重启后还得上来再敲一遍这条命令，嫌麻烦可以做个自启动。<br/>
至此，服务器端的Shadowsocks搭建完毕。
# 五、客户端
下载链接：https://github.com/shadowsocks/shadowsocks-windows/releases<br/>
安卓可以下个APKPure（一个跟Google Play差不多的应用市场 没有被墙），从里面可以下到安卓端的Shadowsocks.<br/>
解压后运行小飞机图标，任务栏右下角会有个小飞机图标，右击->服务器->编辑服务器<br/>
<br/>
<img src="http://wx1.sinaimg.cn/mw690/8cb7c87ely1fhielhonc5j20dx0awdhv.jpg" />
<br/>
服务器地址对应之前配置文件的server，服务器端口对应server_port、密码、加密方式都要和配置文件一样。<br/>
代理端口1080，也和配置文件一样<br/>
# 六、系统代理、PAC
如果懒得设置浏览器，请直接右击小飞机，勾上系统代理，然后用IE即可。<br/>
Chrome、Firefox应该默认使用系统代理，也可以直接使用，其他浏览器不太清楚。<br/>
系统代理有两个模式：PAC和全局。PAC模式下会根据规则代理，比如上百度就不走代理，上谷歌才走代理。全局模式就是不管三七二十一全走代理。<br/>
正常上网请用PAC模式，不然你上个淘宝都是淘宝海外版。<br/>
PAC选使用本地PAC，然后请从GFList更新本地PAC。一小段时间后会提示PAC更新成功。<br/>
此时，打开IE，看看能不能上谷歌吧。<br/>
如果你之前虚拟机勾选了IPv6，把系统代理模式改成全局，然后试试北邮人吧：https://bt.byr.cn/<br/>
解释一下为什么要全局：因为PAC模式下的规则是从GFList下载的,就是GFW的屏蔽列表，如果地址没被墙，那么是不会走代理的。北邮人的地址显然不在GFList里，所以想走代理只能用全局模式。
# 七、浏览器
如果用Chrome可以用SwitchyOmega插件，教程：http://www.cylong.com/blog/2017/04/09/chrome-SwitchyOmega/<br/>
火狐可以用FoxyProxy，教程：https://kxsw.life/firefox-shadowsocks-foxyproxy-standard/<br/>
设置好这两个插件后，可以右击小飞机，然后取消系统代理。<br/>
对比一下你就可以发现，浏览器插件其实就是代替了系统代理，在浏览器层面就帮你决定了是否要走代理。<br/>
# 八、IPv6
稍加配置你就可以用IPv6的流量访问任意站点了。
服务器端：<br/>
创建第二个配置文件<br/>
在做这一步之前，最好先ping一下IPv6的地址看能不能ping通<br/>
vi /etc/ss6.json<br/>
内容如下：<br/>
{<br/>
    "server":"你虚拟机的IPv6 IP",<br/>
    "server_port":8389,<br/>
    "local_address":"127.0.0.1",<br/>
    "local_port":1080,<br/>
    "password":"你的密码",<br/>
    "timeout":300,<br/>
    "method":"aes-256-cfb",<br/>
    "fast_open":false<br/>
}<br/>
注意server_port不能与之前ss4.json中的一样。<br/>
然后启动服务：<br/>
ssserver -c /etc/ss6.json -d start --pid-file ss2.pid<br/>
<br/>
客户端：<br/>
同IPv4一样，编辑服务器，只不过服务器地址要填你虚拟机的IPv6地址。<br/>
<br/>
右击小飞机，把服务器切换到IPv6地址的服务器上。<br/>
如果你使用的是系统代理，那就切到全局模式，然后使用IE访问任意站点应该都是走IPv6流量。<br/>
如果你设置了浏览器插件，那么更改插件的情景模式，让插件执行全局代理，也可以达到同样的效果。<br/>
