# rosdep-init-and-update-issue

在安装ROS kinetic的时候，最后安装依赖的步骤里，会运行这两句：

sudo rosdep init

rosdep update

现在运行它们总会出现错误，即连不上对应的raw.githubusercontent域名的网址以至于无法下载需要的文件。

连不上的原因是被国内fang huo qiang给屏蔽了。
目前的解决方案是：

1. sudo rosdep init

这句话实际上是在/etc/ros/rosdep/sources.list.d 这个路径里下载一个文件：20-default.list

因此这个文件可以我们自己手动添加，其内容为：
————————————————————————————————————————————————————

\# os-specific listings first

yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/osx-homebrew.yaml osx


\# generic

yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/base.yaml

yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/python.yaml

yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/ruby.yaml

gbpdistro https://raw.githubusercontent.com/ros/rosdistro/master/releases/fuerte.yaml fuerte


\# newer distributions (Groovy, Hydro, ...) must not be listed anymore, they are being fetched from the rosdistro index.yaml instead

————————————————————————————————————————————————————

添加好了以后，sudo rosdep init就算是完成了。

2. rosdep update

这一步的实际操作应该是读取刚刚的20-default.list文件，给予里面所提供的yaml文件（参数文件）的信息，下载几个yaml文件里面的参数内容并根据该内容进行对ros依赖的安装。因此这一步不可避免调动raw.githubusercontent这个域名的五个网址。但是它们被qiang了。因此，我们可以修改hosts文件里的内容来解开当前的屏蔽使我们可以正常访问raw.githubusercontent这个域名的网址。一旦网址可以自由访问，这句rosdep update便可以正常运转。

首先打开hosts文件，在ubuntu16.04LTS中，该文件是在/etc/hosts。

打开方式可以用vim：sudo vim /etc/hosts

也可以用gedit：sudo gedit /etc/hosts

打开以后，向里面直接添加以下文本：

————————————————————————————————————————————————————

\# GitHub Start

52.74.223.119     github.com

192.30.253.119    gist.github.com

54.169.195.247    api.github.com

185.199.111.153   assets-cdn.github.com

151.101.76.133    raw.githubusercontent.com

151.101.76.133    gist.githubusercontent.com

151.101.76.133    cloud.githubusercontent.com

151.101.76.133    camo.githubusercontent.com

151.101.76.133    avatars0.githubusercontent.com

151.101.76.133    avatars1.githubusercontent.com

151.101.76.133    avatars2.githubusercontent.com

151.101.76.133    avatars3.githubusercontent.com

151.101.76.133    avatars4.githubusercontent.com

151.101.76.133    avatars5.githubusercontent.com

151.101.76.133    avatars6.githubusercontent.com

151.101.76.133    avatars7.githubusercontent.com

151.101.76.133    avatars8.githubusercontent.com

\# GitHub End

————————————————————————————————————————————————————

添加好后保存退出。接下来只需重启或者刷新一下网络，理应就可以了。测试网页可以打开github代码里的raw模式以后，理论上运行一下rosdep update应该就可以顺利通过了。
