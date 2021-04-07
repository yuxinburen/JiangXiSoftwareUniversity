# 操作Docker容器

**@author：于洪伟**



容器是Docker的一个核心概念。简单的说，容器是镜像的一个运行实例。

① 镜像是静态的只读文件

② 容器带有运行时需要的可写文件层，同时容器中的应用进程处理运行状态。



#### 1、创建容器

* 新建容器：可以使用docker create命令新建一个容器，比如如下举例所示：

  ```
  $docker create -it ubuntu:latest
  ```

  使用docker create命令创建的容器处于**停止**状态，可以使用dockers start命令启动创建的容器。

  create命令有很多的可选项参数，可以根据不同的使用场景进行选择不同的可选项参数。

* 启动容器：可以使用docker start命令来启动一个已经创建的容器，比如如下所示：

  ```
  $docker start af
  ```

* 新建并启动容器：通过docker run 命令，可以在新建一个容器以后，启动该容器的运行。利用docker run命令来创建并启动容器时，Docker在后台执行的操作主要包括：

  * 检查本地是否存在指定的镜像，不存在则自动从共有仓库下载；
  * 利用镜像创建一个容器，并启动该容器；
  * 分配一个文件系统给容器，并在只读的镜像层外挂载一层可读写层；
  * 从宿主主机配置的网络桥接口中桥接一个虚拟接口到容器中去；
  * 从网桥的地址池配置一个IP地址给容器；
  * 执行用户指定的应用程序；
  * 执行完毕后容器被自动终止。

  docker run命令有很多的可选项配置，可以使用man docker-run命令进行查看。

* 守护态运行：通过docker run的-d选项可以实现Docker容器的后台守护态的形式运行。

* 查看容器输出：docker logs命令可以获取容器的输出信息。该logs命令有很多可选项参数选择，如下分别是：

  * -details：打印详细信息
  * -f，-follow：持续输出
  * -since string：输出从某个时间开始的日志
  * -tail string：输出最近的若干日志
  * -t，-timestamps：显示时间戳信息
  * -until string：输出某个时间之前的日志



#### 2、停止容器

* 暂停容器：使用docker pause CONTAINER命令可以来暂停一个运行中的容器。例如下列命令所示，先启动一个容器，然后暂停该容器：

  ```
  $docker run --name test
  $docker pause test
  ```

  处于paused状态的容器，可以使用docker unpause CONTAINER 命令来恢复到运行状态。

* 终止容器：使用docker stop来终止一个运行中的容器，具体的命令格式为docker stop -t=10 CONTAINER...，此处的-t或者--time表示延迟多长时间停止该容器的运行。

  * docker prune：该命令会自动清除掉所有处于停止状态的容器。
  * docker start：处于停止状态的状态可以使用docker start命令来重新启动。

* 重启容器：docker restart命令会将一个运行状态的容器先终止，然后再重新启动。



#### 3、进入容器

使用-d参数启动容器时，启动的容器会进入后台，用户无法查看到容器中的信息，也无法进行任何的操作。

如果需要进入容器中进行操作，可以使用如下命令：

* attach命令：attach是Docker自带的命令，具体的格式为：

  ```
  $docker attach [--detach-keys[=[]]] [--no-stdin] [--sig-proxy[=true]] CONTAINER
  ```

  如上格式为：

  * --detach-keys[=[]]：指定退出attch模式的键序列；
  * --no-stdin=true|false：是否关闭标准输入，默认是保持打开；
  * --sig-proxy=true|false：是否代理收到的系统信号给应用进程，默认为true；

* exec命令：从Docker的1.3.0版本开始，docker提供了一个exec命令，可以在运行中的容器中执行命令。具体的格式为：

  ```
  $docker exec [参数可选项] CONTAINER command [参数]
  ```

  

#### 4、删除容器

使用docker rm命令来删除处于终止或者已经处于退出状态的容器，命令格式为如下所示：

```
$docker rm [-f|--force] [-l|--link] [-v|--volumes] CONTAINER 
```

rm命令的可选项参数主要包括：

* -f，--force=false：是否强行删除一个运行中的容器；
* -l，--link=false：删除容器的链接，但是保留容器；
* -v，--volumes=false：删除容器挂载的数据卷；



#### 5、导入和导出容器

导入和导出容器是在需要将容器从一个系统中迁移到另外一个系统中时，可以使用的功能。

