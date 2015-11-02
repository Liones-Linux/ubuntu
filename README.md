# 常见问题

## 避免 sudo 输入密码

-   运行：

    `sudo visudo`
    
-   在尾部增加：

    ```
    # 属于 mwn 组的用户运行 sudo 不需要密码
    %mwn ALL=(ALL:ALL) NOPASSWD: ALL
    ```

## 查特定程序的进程号

-   `pgrep redis`

-   `ps -e | grep redis`

-   `netstat -lnp  | grep 6379`

    根据端口号查进程

## 让命令运行在后台

-   方法一

    `nohub <cmd> &`

-   方法二

    将一个或多个命名包含在“()”中就能让这些命令在子 shell 中运行中

    当我们将"&"也放入“()”内之后，我们就会发现所提交的作业并不在作业列表中，无法通过jobs来查看

    `(ping www.ibm.com &)`

## 配置apt-get 源

-   编辑：

    `vi /etc/apt/sources.list`
    
-   增加国内源：

    ```
    # 搜狐源：
    deb http://mirrors.sohu.com/ubuntu/ precise-updates main restricted
    deb-src http://mirrors.sohu.com/ubuntu/ precise-updates main restricted
    deb http://mirrors.sohu.com/ubuntu/ precise universe
    deb-src http://mirrors.sohu.com/ubuntu/ precise universe
    deb http://mirrors.sohu.com/ubuntu/ precise-updates universe
    deb-src http://mirrors.sohu.com/ubuntu/ precise-updates universe
    deb http://mirrors.sohu.com/ubuntu/ precise multiverse
    deb-src http://mirrors.sohu.com/ubuntu/ precise multiverse
    deb http://mirrors.sohu.com/ubuntu/ precise-updates multiverse
    deb-src http://mirrors.sohu.com/ubuntu/ precise-updates multiverse
    deb http://mirrors.sohu.com/ubuntu/ precise-backports main restricted universe multiverse
    deb-src http://mirrors.sohu.com/ubuntu/ precise-backports main restricted universe multiverse

    # 网易源：
    deb http://mirrors.163.com/ubuntu/ precise-updates main restricted
    deb-src http://mirrors.163.com/ubuntu/ precise-updates main restricted
    deb http://mirrors.163.com/ubuntu/ precise universe
    deb-src http://mirrors.163.com/ubuntu/ precise universe
    deb http://mirrors.163.com/ubuntu/ precise-updates universe
    deb-src http://mirrors.163.com/ubuntu/ precise-updates universe
    deb http://mirrors.163.com/ubuntu/ precise multiverse
    deb-src http://mirrors.163.com/ubuntu/ precise multiverse
    deb http://mirrors.163.com/ubuntu/ precise-updates multiverse
    deb-src http://mirrors.163.com/ubuntu/ precise-updates multiverse
    deb http://mirrors.163.com/ubuntu/ precise-backports main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ precise-backports main restricted universe multiverse
    ```

## 不能识别移动硬盘
-   安装：

    `sudo apt-get install cifs-utils`

## 双显卡设备出现未知显示器
-   详细：

    <http://www.kaijia.me/2014/04/ubuntu-14-04-unknown-display-in-dual-graphics-solved/>

-   原因：

    官方对这个错误的解释是双显卡驱动的情况下（比如Kaijia的笔记本是就是集显和N卡热切换）Ubuntu会同时读出两个显示器，显示的效果即为一个内置显示器和一个未知显示器。

-   解决：

    `sudo apt-get install bumblebee-nvidia`

## 双显示器扩展方式外接显示器分辨率低
-   详细：
   
    <http://www.linuxidc.com/Linux/2010-09/28586.htm>

-   `cvt 1440 900`

    结果如下

    ```
    # 1440x900 59.89 Hz (CVT 1.30MA) hsync: 55.93 kHz; pclk: 106.50 MHz
    Modeline "1440x900_60.00"  106.50  1440 1528 1672 1904  900 903 909 934 -hsync +vsync
    ```

    Modeline后面的值在下边要用。然后执行

-   `xrandr`

    得到显示器名称，我的为 VGA1，笔记本为LVDS1，大家以此类推，不一定都一样

-   `sudo xrandr --newmode "1440x900_60.00" 106.50 1440 1528 1672 1904 900 903 909 934 -hsync +vsync`

    参数就是cvt结果中Modeline后面的那一堆，根据自己的参数调整。

-   `xrandr`

    就可以看到新加的那个设置了。

-   `sudo xrandr --addmode VGA1 "1440x900_60.00"`
    添加新的mode

-   添加启动脚本：

    编辑 `/etc/rc.local`

    ```
    xrandr --newmode "1440x900_60.00"  106.50  1440 1528 1672 1904  900 903 909 934 -hsync +vsync
    xrandr --addmode VGA1 "1440x900_60.00"
    xrandr --output LVDS1 --mode 1366x768
    xrandr --output VGA1 --mode 1440x900_60.00
    xrandr --output VGA1 --right-of LVDS1
    ```

# 应用程序

## amule
-   简介

    下载工具

-   安装

    `sudo apt-get install amule amule-utils`

-   配置与chrome关联

    `sudo gedit /usr/bin/xdg-open`

    将其中open-gnome处改为

    ``` 
    open_gnome()
    {
        if echo "$1" | grep -q '^ed2k://'; then
        ed2k "$1"

        elif gvfs-open --help 2>/dev/null 1>&2; then
            gvfs-open "$1"
        else
            gnome-open "$1"
        fi

        if [ $? -eq 0 ]; then
            exit_success
        else
            exit_failure_operation_failed
        fi
    }
    ```

## virtualbox

-   使用 Virtualbox 安装 Windows

    <http://www.linuxidc.com/Linux/2012-11/74195p3.htm>

## upstart

-   官网

    <http://upstart.ubuntu.com>

-   简介

    Upstart 可以优雅地管理所有Linux程序的启动和停止,包括Node程序。

    Upstart支持现代版的Ubuntu和 CentOS。

-   安装

    Ubuntu: `sudo apt-get install upstart`

    CentOS: `sudo yum install upstart`

-   配置文件

    `sudo touch /etc/init/hellonode.conf`

-   配置选项

    把下面代码清单中的内容添加到你的配置文件中。这个设置会在你的服务器启动时运行你的
    程序,并在服务器关闭前停止它。其中的 exec 部分由 Upstart执行。

    ```
    #!upstart

    author "mcantelon"
    description "hellonode"
    setuid "nonrootuser"

    start on (local-filesystems and net-device-up IFACE=eth0)
    stop on shutdown
    respawn

    console log

    env NODE_ENV=production

    exec /usr/bin/node /path/to/server.js
    ```

    1.指定程序的作者

    2.设定程序的名称或描述

    3.用 nonrootuser 用户运行程序

    4.系统启动时,在文件系统和网络可用之后启动程序

    5.关闭时停止程序

    6.在程序崩溃时重启程序

    7.将 stdin 和 stderr 输出到 /var/log/upstart/yourapp.log

    8.为程序设定必要的环境变量

    9.指定执行程序的命令

## nginx

-   简介

    尽管Node在提供动态内容服务时很高效,但在提供图片、 CSS样式表或客户端JavaScript等静
    态文件服务时并不是最有效的办法。

    通过HTTP提供静态文件的服务应该交给专门针对这个特定任务优化过的特定软件项目,
    因为它们多年以来主要专注于这项任务。

    Nginx (http://nginx.org/en/) 是一个专门针对静态文件服务做过优化的开源Web服务器,很容
    易设置成跟Node一起提供那些文件服务。在典型的Nginx/Node配置中,一般由Nginx先处理所有
    Web请求,再将非静态文件的请求转给Node。

-   安装

-   配置文件

    `/etc/nginx/nginx.conf`

-   配置选项

    ```
    http {
      upstream my_node_app {
        server 127.0.0.1:8000;
      }
      server {
        listen 80;
        server_name localhost domain.com;
        access_log /var/log/nginx/my_node_app.log;
        location ~ /static/ {
          root /home/node/my_node_app;
          if (!-f $request_filename) {
            return 404;
          }
        }
        location / {
          proxy_pass http://my_node_app;
          proxy_redirect off;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header Host $http_host;
          proxy_set_header X-NginX-Proxy true;
        }
      }
    }
    ```