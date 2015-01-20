# pomelo2 so库生成 #

- 环境：**ubuntu 14**

- 软件：**android ndk r10 for linux、** 
**pomelo2 源码、**
**gyp**
        
##1、下载libpomelo2的代码##
        直接从github上下载就可以。

##2、下载android ndk的包，解压出来##

##3、在系统上安装gyp。##

```bash
sudo apt-get install gyt
```
------快速安装gyp
##4、修改编译配置文件##
打开官方的`MakeFile`生成脚本：android-compile.sh
找出下面这几句（他们并不是紧挨着的）：
> export ANDROID_TOOLCHAIN_DIR=$PWD/android-toolchain <br>
> export PATH=$ANDROID_TOOLCHAIN_DIR/bin:$PATH
> export AR=arm-linux-androideabi-ar<br>
> export CC=arm-linux-androideabi-gcc<br>
> export CXX=arm-linux-androideabi-g++<br>
> export LINK=arm-linux-androideabi-g++<br>
> export PLATFORM=android

将export ANDROID_TOOLCHAIN_DIR=$PWD/android-toolchain这句中的$PWD改为你的libpomelo2代码的根目录

例如：
> export ANDROID_TOOLCHAIN_DIR=/home/colink/project/libpomelo2/android-toolchain

将上面这7行粘贴到/etc/profile的最下面,然后运行

'''bash
source ~/.bash_profile
'''

再运行echo $PATH应该能看到我们设置的ANDROID_TOOLCHAIN_DIR
##5、将android-compile.sh中对应的这7句删掉并保存。
##6、在libpomelo2目录下运行 ./android-compile.sh <ndk解压出来的目录>，
例如：
> ./android-compile.sh /home/colink/project/android-ndk-r10b

##7、运行完之后会看到在libpomelo2目录下生成了Makefile文件。
##8、直接make编译，会在libpomelo2的out/Default/lib.target中看到编译出来的libjpomelo.so文件
