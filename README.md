---
title: '[2020/7/15]vs编译libcurl'
date: 2020-07-15 15:23:53
tags:
 -visual stutio
 -c++
 -环境搭建
---

## libcurl简介

libcurl是CURL的产品之一，它的功能是**基于网络协议，对指定url进行网络传输**，CURL的产品只涉及对任何网络协议的传输，而不包含对具体数据的处理（如html的渲染等）。<font size=2>--维基百科</font>

### 支持的协议

 CURL支持的通讯协议有DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, POP3, POP3S, RTMP, RTMPS, RTSP, SCP, SFTP, SMB, SBMS, SMTP, SMTPS, TELNET 和TFTP。 

## 编译

### 前言

* zlib编译（zlib-1.2.11）
* openssl编译（openssl-1.1.0l）
* libssh2编译（libssh2-1.9.0）
* libcurl编译（curl-7.66.0）

<font size=2>*上述组件版本是我编译过程中所使用的，小伙伴也可以使用你所在时期的最新版本，如果换了版本，可能会存在一些奇怪的的问题，欢迎与我交流*</font>

### zlib编译

#### 环境准备

zlib: [zlib-current-release]( http://www.zlib.net/ )

visual studio:至少VC9（visual studio 2008),对应版本如下：

* VC9 —— visual studio 2008
* VC10 —— visual stutio 2010
* VC11 —— visual stutio 2012
* VC12 —— visual stutio 2013
* VC14 —— visual studio 2015

当然，目前（2020年7月）visual stutio已经更新到了2019，但是不影响，可以使用2015之后的visual studio打开VC14的项目，依旧可以完美编译。这里统一说明一下使用更高版本的visual studio打开低版本的项目需要的配置：

* 如果项目打开时自动提示升级平台工具集，那么升级即可：

  ![20200715-1](20200715-1.png)

* 如果没有弹出提示，那么可以右键选择项目->属性->配置属性->常规->工具集平台进行修改：

  ![20200715-2](20200715-2.png)

#### 步骤

1. 导入zlibvc.vcxproj（**目标所在目录：xxx\zlib-1.2.11\contrib\vstudio\vc15**);

   ![20200715-3](20200715-3.png)

2. 更改zlibvc项目的部分属性：

   配置管理器为：x64
   【常规】->【目标文件名】内容改为zlib_zip1211;
   【C/C++】->【预处理器定义】更改ZLIB_WINAPI–>ZLIB_DLL (为了编译出的dll可以使用zip相关接口)
   【链接器】->【常规】->【输出文件】内容改为\$(OutDir)\$(TargetName).dll
   【链接器】->【高级】->【导入库】内容改为\$(OutDir) $(TargetName).lib

3.  install

   本文环境统一将依赖库组织到 **@统一文件夹** 下
   zlib安装目录：**@统一文件夹**\zlib_zip_1_2_11_x64
   安装文件夹组织如下 :

   ![20200715-4](20200715-4.png)

4. 结果图示：

   *注意：使用vs编译之后需要在项目目录附近寻找编译结果，具体可以查看最后的**编译输出**，这里的示例是我将编译结果移动到**@统一文件夹**下的结果。*

   lib文件夹

   ![20200715-5](20200715-5.png)

   bin文件夹

   ![20200715-6](20200715-6.png)

   include文件夹

   ![20200715-7](20200715-7.png)

### openssl编译

#### 环境准备

openssl: [openssl-cur-release]( https://www.openssl.org/ )

zlib:前一步你编译的zlib的结果

vs平台工具集的配置的说明同第一节。

#### 步骤

编译前仔细阅读**解压根目录**下**INSTALL** 和 **NOTES.WIN** 这两个文件（版本1.0.2对应的文件叫 INSTALL.W32和INSTALL.W64），编译过程基本上都在这两个文件里面。 

1.  安装perl和汇编工具，按照NOTES.WIN里面推荐的去安装 ActivePerl和nasm，一般安装最新版即可。**注：官网下载ActivePerl太慢，可以从360的软件管家里面搜索安装，<font color=red>如果你想编译x86版本的库，那么必须使用x86版本的perl，这里推荐一个[perl_x86]( http://strawberryperl.com/ )</font>**。 

2.  以管理员身份打开VS2019的命令行程序，本文编译x86版本环境，打开 “x86 Native Tools Command Prompt for VS 2019”。在命令行中切换至openssl-1.1.0l 解压根目录，按照如下命令开始编译:

   ```shell
   >> perl Configure VC-WIN32 --prefix=@统一文件夹\openssl_1.1.0l --with-zlib-include=@统一文件夹\zlib_zip1.2.11\include --with-zlib-lib=@统一文件夹\zlib_zip1.2.11\bin\zlib_zip1211.lib zlib-dynamic
   
   # VC-WIN32:32bit版本编译
   # --prefix: 指定编译结果安装目录
   # --with-zlib-include: 指定zlib库头文件
   # --with-zlib-lib: 指定zlib库导入文件
   # zlib-dynamic: zlib以dll形式导入
   
   >> nmake
   >> nmake test
   >> nmake install
   >> nmake clean
   ```

3. install

   命令执行后，内容组织如下：

   lib文件夹：

   ![20200715-8](20200715-8.png)

   bin文件夹：

   ![20200715-9](20200715-9.png)

   include文件夹：

   ![20200715-10](20200715-10.png)

### libssh2编译

#### 环境准备

libssh2: [libssh2-cur-release]( https://www.libssh2.org/ );

zlib: 上述zlib的编译结果

openssl: 上述openssl的编译结果

vs平台工具集的配置第一节。

#### 步骤

1. 导入项目

   将**libssh2-1.9.0解压根目录**\win32\libssh2.dsw项目导入vs2019，项目配置如图：

   ![20200715-11](20200715-11.png)

   项目导入后的样子：

   ![20200715-12](20200715-12.png)

2. 配置libssh2项目，以 **OpenSSL DLL Release | x86**为例，其他类似。

   * 首先选择**OpenSSL DLL Release | x86**（一定要确保这里选择的是你想要编译的平台，不然后边修改了配置你都不知道修改到那个版本去了）。

   *  【C/C++】->【常规】更新**openssl include** 和 **zlib include** 

     >  这里就是【@统一文件夹\openssl_1_1_0l\include】和【@统一文件夹\zlib_zip_1_2_11\include】

   *  【链接器】->【常规】->【附加库目录】更新**openssl lib** 和 **zlib lib**

     > 这里就是【@统一文件夹\openssl_1_1_0l\lib】和【@统一文件夹\zlib_zip_1_2_11\lib】

   *  链接器】->【输入】更新导入库名字 

     >  libcrypto.lib libssl.lib zlib_zip1211.lib （上述编译的结果） 

     *注意：这里的**更新**指的是原来项目已经配置过，现在你需要将原来的删除，然后用你的路径去替换。*

3. install

   配置完成后，启动编译即可。 

   结果内容组织如下：

   lib文件夹：

   ![20200715-13](20200715-13.png)

   bin文件夹：

   ![20200715-14](20200715-14.png)

   include文件夹：

   ![20200715-15](20200715-15.png)

### libcurl编译

#### 环境准备

curl: [curl-all-release]( https://github.com/curl/curl/releases );

zlib: 上述zlib编译结果

openssl: 上述openssl编译结果

libssh2: 上述libssh2编译结果

vs工具集平台配置同第一节。

#### 步骤

1. 项目生成

   从github上下载的源码包的.vcxproj文件被删除了，需要自己生成才行，方法是：运行**curl解压根目录**\projects下的generate.bat，系统自动为我们生成.vcxproj文件。

2. 项目导入

   如果你只想使用libcurl而不想使用curl工具的话，直接将**curl解压根目录**\projects\Windows\VC15\lib\libcurl.sln导入即可，如果两个都想使用，那么就将**curl解压根目录**\projects\Windows\VC15\curl-all.sln导入。这里我只导入了前者。

   导入后项目视图：

   ![20200715-16](20200715-16.png)

   项目配置视图：

   ![20200715-17](20200715-17.png)

3. 编译

   这里只编译 **DLL Release - DLL OpenSSL - DLL LibSSH2 | x86**版本的。 

   *  【C/C++】->【常规】更新**openssl include** 和 **zlib include** 以及 **libssh2 include** 

     > 这里就是【@统一文件夹\openssl_1_1_0l_vc15_x64\include】和【@统一文件夹\zlib_zip_1_2_11_vc15_x64\include】和【@统一文件夹\libssh2_1_9_0_vc15_x64\include】 

   *  【C/C++】->【预处理器】添加**HAVE_ZLIB_H** （zlib的支持） 

   *  【链接器】->【常规】->【附加库目录】更新**openssl lib** 和 **zlib lib** 以及 **libssh2 lib** 

     > 这里就是【@统一文件夹\openssl_1_1_0l_vc15_x64\lib】和【@统一文件夹\zlib_zip_1_2_11_vc15_x64\lib】和【@统一文件夹\libssh2_1_9_0_vc15_x64\lib】 

   *  【链接器】->【输入】更新导入库名字 

     > libcrypto.lib libssl.lib zlib_zip1211.lib libssh2.lib （上述编译的结果） 

   *这里存在问题，上述导入的项目默认情况下，Curl_ssh_init Curl_ssh_cleanup Curl_ssh_version函数没有定义，需要为项目引入定义这些函数的cpp。路径为：@curl解压根目录\lib\vssh\libssh2.c*。也就是【源文件】->【添加】->【现有项】。

4. install

   配置完成后，启动编译即可。

   文件组织如下：

   lib文件夹：

   ![20200715-18](20200715-18.png)

   bin文件夹：

   ![20200715-19](20200715-19.png)

   include文件夹：

   ![20200715-20](20200715-20.png)

至此，编译顺利完成。

**最终编译结果放在这里：**

[编译结果](https://github.com/chenyuxiangg/cyx_libcurl)

