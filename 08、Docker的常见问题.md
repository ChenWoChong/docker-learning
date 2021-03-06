## 08、Docker的常见问题

### 0、前注

问题会不断更新，如果你遇见问题但不知如何解决，可以加QQ群：387017550，进行交流。

目录：

* <a href="#1、容器自动退出">1、容器自动退出/停止</a>
* <a href="#"></a>
* <a href="#"></a>
* <a href="#"></a>
* <a href="#"></a>

### 1、容器自动退出、停止

如果容器内的程序是后台运行的，那么当你退出容器后（比如从bash退出），或者后台运行容器（即不在bash内），那么容器就会自动退出；

解决方案：

【方法一】

使用 ``-dit`` 参数，并且末尾加 ``/bin/bash``。

效果：

【是后台运行、交互模式、分配终端，容器启动后不会退出】。这个并不影响你正常进入容器和使用命令行；

缺点：

创建容器时，看不到CMD执行的一些命令了；

【方法二】

创建一个前台一直执行的脚本，然后在容器里执行即可。

比如CMD执行一个脚本，脚本内容最后包含：

```
echo "" >/usr/src/cron.log
tail -f /usr/src/cron.log
```

这样方便调试，需要看容器运行状况时使用，调试完按 ctrl + z 退出。

### 2、安装依赖时遇见墙

某些依赖，在本机时可以安装，但在容器里时无法安装。原因是往往本机有代理可以访问外网，但容器的网络不经过本机网络。

解决方案：

【方法一】

使用国内镜像。例如 pip 安装依赖时，如下使用

```
RUN pip install --no-cache-dir -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple
```

其中 ``-i https://mirrors.aliyun.com/pypi/simple`` 表示使用阿里云镜像。

* ``-i 镜像源``：表示通过指定镜像安装依赖

【方法二】

配置 pypi 仓库，具体来说。在文件 ``~/.pip/pip.conf`` 里添加内容

```
[global]
index-url=https://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host=mirrors.aliyun.com
```

而 ``~`` 目录一般指 ``/root`` 目录。或者你手动进入容器后，输入 ``cd ~``，然后再通过 ``pwd`` 查看当前目录