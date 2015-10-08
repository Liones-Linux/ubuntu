# ubuntu 常见问题

## 避免 sudo 输入密码

-   运行：

    `sudo visudo`
    
-   在尾部增加：

    ```
    # 属于 mwn 组的用户运行 sudo 不需要密码
    %mwn ALL=(ALL:ALL) NOPASSWD: ALL
    ```

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

## 使用 Virtualbox 安装 Windows

-   详细：

    <http://www.linuxidc.com/Linux/2012-11/74195p3.htm>

## 安装 amule 下载工具

-   运行：

    `sudo apt-get install amule amule-utils`

-   配置与chrome关联：

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
