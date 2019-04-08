---
title: "Flutter基础"
date: 2019-03-07T10:51:14+08:00
toc: false
draft: false
tags: 
  - flutter
---

# Flutter是什么？

Flutter是Google一个新的用于构建跨平台的手机App的SDK。写一份代码，在Android 和iOS平台上都可以运行。
## Flutter安装

因为我是使用的mac，所以用mac演示

这里是[官方链接](https://flutter.dev/docs/get-started/install/macos)，演示了flutter的安装流程，中间有点小坑；我重新走一遍。

1.下载Flutter SDK的最新稳定版本，[地址](https://flutter.dev/docs/development/tools/sdk/archive)

2.在你的finder选择你想把Flutter工具放的位置，这点和golang要设置gopath非常像。
比如我喜欢放在文稿里：
 
```
cd /Users/figaro/Documents/flutter
unzip ~/Downloads/flutter_macos_v1.2.1-stable.zip
```
3.将工具添加到路径：


```
export PATH="$PATH:`pwd`/flutter/bin"
```
这个是临时性方案，这样做只能在当前命令行窗口执行。如果要永久性的话，走下面流程：
3.1 终端进入当前用户的home目录
    
    
```
cd ~
```
3.2 创建或者打开profile文件


```
创建
touch .bash_profile 
打开编辑
open -e .bash_profile 
```
编辑完成关闭就保存修改

3.3 更新配置的环境变量


```
source .bash_profile
```
3.4 检查


```
echo $PATH
```

4.检查是否需要依赖完成设置


```
flutter doctor
```
仔细完成提示的解决方案就好：

```
[!] iOS toolchain - develop for iOS devices
    ✗ Xcode installation is incomplete; a full installation is necessary for iOS
      development.
      Download at: https://developer.apple.com/xcode/download/
      Or install Xcode via the App Store.
      Once installed, run:
        sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
    ✗ libimobiledevice and ideviceinstaller are not installed. To install with
      Brew, run:
        brew update
        brew install --HEAD usbmuxd
        brew link usbmuxd
        brew install --HEAD libimobiledevice
        brew install ideviceinstaller
    ✗ ios-deploy not installed. To install:
        brew install ios-deploy
[!] Android Studio (not installed)
[!] Connected device
    ! No devices available

! Doctor found issues in 4 categories.
```
4.1 出现错误解决方案
在这一步：brew install --HEAD libimobiledevice 出现错误如下：


```
Error: An unexpected error occurred during the `brew link` step
The formula built, but is not symlinked into /usr/local
Permission denied @ dir_s_mkdir - /usr/local/Frameworks
Error: Permission denied @ dir_s_mkdir - /usr/local/Frameworks
```
解决方法：


```
 sudo mkdir /usr/local/Frameworks
 sudo chown $(whoami):admin /usr/local/Frameworks
```
手机创建目录，然后赋权。可以使用brew cleanup清理废弃文件。

5.xcode配置

xcode9.0以上版本确定有了之后，执行

```
 sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
```

确保通过打开过一次xcode，然后执行

```
sudo xcodebuild -license
```
读完以后按空格，出现以下选择

填写agree

```
By typing 'agree' you are agreeing to the terms of the software license agreements. Type 'print' to print them or anything else to cancel, [agree, print, cancel] agree
```
这样xcode的协议就签署完成。

6.设置iOS模拟器

6.1 打开模拟器


```
 open -a Simulator
```

6.2 配置或者不需要修改

通过检查模拟器的硬件>设备菜单中的设置，确保您的模拟器使用的是64位设备（iPhone 5s或更高版本）。
根据您的开发机器的屏幕尺寸，模拟的高屏密度iOS设备可能会溢出您的屏幕。在模拟器的“ 窗口”>“比例”菜单下设置设备比例。

7 创建并运行一个简单的flutter app

7.1 命令行创建一个新的flutter app


```
flutter create my_app
```
7.2 进入此目录


```
cd my_app
```
7.3 要在模拟器中启动应用程序，请确保模拟器**正在运行**并输入：


```
flutter run
```

8 部署到iOS设备

要将Flutter应用程序部署到物理iOS设备，您需要一些其他工具和Apple帐户。您还需要在Xcode中设置物理设备部署。

8.1 确保homebrew 是最新的


```
 brew update
```
8.2 通过运行以下命令，安装用于将Flutter应用程序部署到iOS设备的工具


```
 brew install --HEAD usbmuxd
 brew link usbmuxd
 brew install --HEAD libimobiledevice
 brew install ideviceinstaller ios-deploy cocoapods
 pod setup
```

iOS的部分基本到这里，如果只是能在apple上使用还说什么跨平台，我们接着设置安卓的部分。


9 android设置

9.1 下载并安装Android studio，[地址](https://developer.android.com/studio)

9.2 启动Android Studio，然后浏览“Android Studio安装向导”。这将安装最新的Android SDK，Android SDK Platform-Tools和Android SDK Build-Tools，这些都是Flutter在开发Android时所需要的

9.3 设置安卓模拟器

TODO：搞定模拟器


9.4 设置安卓设备

因为我没有安卓设备，我抄的官网的；虽然我很多都是抄的官网的。

要准备在Android设备上运行和测试Flutter应用，您需要一台运行Android 4.1（API级别16）或更高版本的Android设备。

在您的设备上启用开发人员选项和USB调试。Android文档中提供了详细说明。
仅限Windows：安装Google USB驱动程序
使用USB线将手机插入计算机。如果您的设备出现提示，请授权您的计算机访问您的设备。
在终端中，运行flutter devices命令以验证Flutter是否识别您连接的Android设备。
默认情况下，Flutter使用您的adb工具所基于的Android SDK版本。如果您希望Flutter使用Android SDK的其他安装，则必须将 ANDROID_HOME环境变量设置为该安装目录。





