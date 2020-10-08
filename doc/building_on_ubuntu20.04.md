### 安装 Native Compiler

```
sudo apt install build-essential
```

建议 GCC 版本为 7.3.0，最低版本为 4.8。现阶段 Ubuntu20.04 上的 GCC 已经是 9.x 了，不建议使用这么高的版本，很有可能会出现各种各样的问题，[参考](https://www.jianshu.com/p/b2a2d97508e7)。

### 安装 Boot JDK

为了编译 JDK 12，需要有一个最新版本的已经编译好的 JDK 11 或者 JDK12。

[下载地址](https://adoptopenjdk.net/)

可以为该 JDK 设置环境变量，否则需要在 configure 时指定使用的 JDK 目录。 

```
--with-boot-jdk='/home/joeylyu/.jdks/adopt-openj9-11.0.8'
```

### 安装第三方库

```
sudo apt install libfreetype6-dev libcups2-dev -y
sudo apt install libx11-dev libxext-dev libxrender-dev libxrandr-dev libxtst-dev libxt-dev -y
sudo apt install libasound2-dev libffi-dev autoconf -y
sudo apt install libfontconfig1-dev -y
```

### 编译

#### Run configure

```
bash configure --enable-debug --with-target-bits=64 --disable-warnings-as-errors
```

上述命令将创建一个 debug 级别为 fastdebug 的 configuration，输出（部分）如下：

```
A new configuration has been successfully created in
/home/joeylyu/Documents/IdeaProjects/jdk12u/build/linux-x86_64-server-fastdebug
using configure arguments '--enable-debug --with-target-bits=64 --disable-warnings-as-errors'.

Configuration summary:
* Debug level:    fastdebug
* HS debug level: fastdebug
* JVM variants:   server
* JVM features:   server: 'aot cds cmsgc compiler1 compiler2 epsilongc g1gc graal jfr jni-check jvmci jvmti management nmt parallelgc serialgc services shenandoahgc vm-structs zgc' 
* OpenJDK target: OS: linux, CPU architecture: x86, address length: 64
* Version string: 12.0.2-internal+0-adhoc.joeylyu.jdk12u (12.0.2-internal)

Tools summary:
* Boot JDK:       openjdk version "11.0.8" 2020-07-14 OpenJDK Runtime Environment AdoptOpenJDK (build 11.0.8+10) Eclipse OpenJ9 VM AdoptOpenJDK (build openj9-0.21.0, JRE 11 Linux amd64-64-Bit Compressed References 20200715_697 (JIT enabled, AOT enabled) OpenJ9   - 34cf4c075 OMR      - 113e54219 JCL      - 95bb504fbb based on jdk-11.0.8+10)  (at /home/joeylyu/.jdks/adopt-openj9-11.0.8)
* Toolchain:      gcc (GNU Compiler Collection)
* C Compiler:     Version 7.5.0 (at /usr/bin/gcc)
* C++ Compiler:   Version 7.5.0 (at /usr/bin/g++)

Build performance summary:
* Cores to use:   4
* Memory limit:   7846 MB
```

#### Run make

生成 Compilation Database，执行下述命令会在相应的 build 下生成一个 compile_commands.json 文件，帮助 Clion 生成 CMakeLists.txt

```
make compile-commands
```

编译 JDK

```
make images
```

这一步耗时会比较长，在 i5 7200U 8G 的笔记本上，耗费了大约 18 min 才编译完成。

如果提示没有 jre 文件夹，这需要去 boot jdk 的根目录执行下列命令

```
bin/jlink --module-path jmods --add-modules java.desktop --output jre
```

#### 测试编译成功的 JDK

`./build/*/images/jdk/bin/java -version`

### Toubleshooting

[修复 Clion 导入 JDK 源码到处飘红的问题](https://www.codenong.com/j5f194bade51d4534be06e06b/)

[Tips & Tricks: Develop OpenJDK in CLion with Pleasure](https://blog.jetbrains.com/clion/2020/03/openjdk-with-clion/)

[Clion enable gdbinit or lldbinit file](https://www.jetbrains.com/help/clion/configuring-debugger-options.html#enable-initfiles-root)