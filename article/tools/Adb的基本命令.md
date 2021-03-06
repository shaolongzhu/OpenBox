# 基本命令 #
### 命令语法 	
1. 只连接了一个设备/模拟器的时候 
	```sh adb <command>
	```
2. 连接多个设备/模拟器需要指定一个设备/模拟器进行连接  
	```sh 
	adb [-d|-e|-s <serialNumber>] <command>
	```

   -d 当前与USB进行连接的设备/模拟器

   -e 当前正在运行的设备/模拟器

   -s 相应序列号的设备/模拟器
### 连接adb   

想通过序列号来连接adb,可以通过以下命令来获取序列号。如：
<pre><code> $ adb service

 List of devices attached
 emulator-5554	device
</code></pre>

输出里的 emulator-5554 即为设备序列号。指定 emulator-5554 这个设备来运行 adb 命令获取屏幕分辨率：
<pre><code>adb -s emulator-5554 shell wm size</code></pre>

### adb启动/停止
启动 adb 命令：
<pre><code> adb start-server </code></pre>
停止 adb 命令：
<pre><code> adb kill-server </code></pre>
### 查看 adb 版本
<pre><code> adb version </code></pre>
### 指定 adb 网络端口
<pre><code> adb -P <port> start-server </code></pre>
默认端口为 5037。

# 设备连接命令 #


### 查询已连接设备/模拟器数量以及序列号
<pre><code> adb devices </code></pre>
### USB 连接
<pre><code> adb devices </code></pre>
运行以上命令如果能看到
<pre><code> xxxxxx device </code></pre>说明连接成功。
### 无线连接（需要借助 USB 线）
运行以上命令如果能看到<pre><code> <device-ip-address>:5555 device </code></pre>  
说明连接成功。

**断开无线连接**
<pre><code> adb disconnect <device-ip-address> </code></pre> 


# 应用命令 #

### 安装 APK

命令格式：

<pre><code>shadb install [-lrtsdg] <path_to_apk></code></pre>

参数：

-l 将应用安装到保护目录 /mnt/asec 

-r 允许覆盖安装 

-t 允许安装 AndroidManifest.xml 里 application 指定android:testOnly="true" 的应用 -s 将应用安装到 sdcard

-d 允许降级覆盖安装 

-g 授予所有运行时权限

运行命令后如果见到类似如下输出（状态为 Success）代表安装成功：

<pre><code>sh
[100%] /data/local/tmp/1.apk
	pkg: /data/local/tmp/1.apk
Success
</code></pre>


### 卸载应用

命令：

<pre><code>sh adb uninstall [-k] <packagename></code></pre>

`<packagename>` 表示应用的包名，`-k` 参数可选，表示卸载应用但保留数据和缓存目录。

### 清除应用数据与缓存

命令：
<pre><code>shadb shell pm clear <packagename></code></pre>
### 查看前台 Activity

命令：

<pre><code>sh adb shell dumpsys activity activities | grep mFocusedActivity</code></pre>
`<packagename>` 表示应用名包，这条命令的效果相当于在设置里的应用信息界面点击了「清除缓存」和「清除数据」。

#  日志命令 #
Android 系统的日志分为两部分，底层的 Linux 内核日志输出到 /proc/kmsg，Android 的日志输出到 /dev/log。

### Android 日志

命令格式：

<pre><code>sh [adb] logcat [<option>] ... [<filter-spec>] </code></pre>

常用用法列举如下：

#### 按级别过滤日志

Android 的日志分为如下几个级别：

* V —— Verbose（最低，输出得最多）
* D —— Debug
* I —— Info
* W —— Warning
* E —— Error
* F —— Fatal
* S —— Silent（最高，啥也不输出）

按某级别过滤日志则会将该级别及以上的日志输出。

比如，命令：

<pre><code>sh adb logcat *:W </code></pre>

会将 Warning、Error、Fatal 和 Silent 日志输出。

#### 按 tag 和级别过滤日志

比如，命令：

<pre><code> sh adb logcat ActivityManager:I MyApp:D *:S</code></pre>

表示输出 tag `ActivityManager` 的 Info 以上级别日志，输出 tag `MyApp` 的 Debug 以上级别日志，及其它 tag 的 Silent 级别日志（即屏蔽其它 tag 日志）。

## 设备信息命令

### 查看型号

命令：

<pre><code>shadb shell getprop ro.product.model </code></pre>

输出示例：
<pre><code>sh Nexus 5</code></pre>

### 查看电池状况

命令：
<pre><code>sh adb shell dumpsys battery</code></pre>

### 查看屏幕分辨率
命令：
<pre><code>sh adb shell wm size</code></pre>
### 查看屏幕密度
命令：
<pre><code>sh adb shell wm density</code></pre>
### 查看显示屏参数
命令：
<pre><code>sh adb shell dumpsys window displays</code></pre>

# 实用功能 #

### 屏幕截图
命令：
<pre><code>sh adb shell screencap -p /sdcard/sc.png</code></pre>
然后将 png 文件导出到电脑：
<pre><code>sh adb pull /sdcard/sc.png</code></pre>

### 重新挂载 system 分区为可写

**注：需要 root 权限。**

/system 分区默认挂载为只读，但有些操作比如给 Android 系统添加命令、删除自带应用等需要对 /system 进行写操作，所以需要重新挂载它为可读写。

步骤：

1. 进入 shell 并切换到 root 用户权限。
	命令：
   	<pre><code>sh adb shell su</code></pre>

2. 查看当前分区挂载情况。
   命令：
   	<pre><code>sh mount</code></pre>
3. 重新挂载。
   命令：
  	 <pre><code>sh mount -o remount,rw -t yaffs2 /dev/block/platform/msm_sdcc.1/by-name/system /system</code></pre>


### 重启手机

命令：
<pre><code>sh adb reboot</code></pre>

### 检测设备是否已 root

命令：
<pre><code>sh adb shell su </code></pre>

此时命令行提示符是 `$` 则表示没有 root 权限，是 `#` 则表示已 root。

### 跑Monkey

Monkey 可以生成伪随机用户事件来用户操作，可以对开发中的程序进行随机压力测试。

简单用法：
<pre><code>sh adb shell monkey -p <packagename> -v 500
</code></pre>

表示向 `<packagename>` 指定的应用程序发送 500 个伪随机事件。

Monkey 的详细用法参考 [官方文档](https://developer.android.com/studio/test/monkey.html)。

### 查看系统进程

命令：
<pre><code>shadb shell ps</code></pre>

### 查看系统资源占用情况

命令：
<pre><code>sh adb shell top</code></pre>

## 参考链接

* [ADB Usage Complete / ADB 用法大全](https://github.com/mzlogin/awesome-adb)
* [Android Debug Bridge](https://developer.android.com/studio/command-line/adb.html)
* [ADB Shell Commands](https://developer.android.com/studio/command-line/shell.html)
* [logcat Command-line Tool](https://developer.android.com/studio/command-line/logcat.html)
* [Android ADB命令大全](http://zmywly8866.github.io/2015/01/24/all-adb-command.html)
* [adb 命令行的使用记录](https://github.com/ZQiang94/StudyRecords/blob/master/other/src/main/java/com/other/adb%20%E5%91%BD%E4%BB%A4%E8%A1%8C%E7%9A%84%E4%BD%BF%E7%94%A8%E8%AE%B0%E5%BD%95.md)
* [Android ADB命令大全(通过ADB命令查看wifi密码、MAC地址、设备信息、操作文件、查看文件、日志信息、卸载、启动和安装APK等)](http://www.jianshu.com/p/860bc2bf1a6a)
* [那些做Android开发必须知道的ADB命令](http://yifeiyuan.me/2016/06/30/ADB%E5%91%BD%E4%BB%A4%E6%95%B4%E7%90%86/)
* [adb shell top](http://blog.csdn.net/kittyboy0001/article/details/38562515)
* [像高手一样使用ADB命令行（2）](http://cabins.github.io/2016/03/25/UseAdbLikeAPro-2/)

