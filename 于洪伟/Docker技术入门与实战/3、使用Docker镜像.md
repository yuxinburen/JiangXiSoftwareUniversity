## 使用Docker镜像

**@author：于洪伟**



镜像是Docker三大核心概念中最重要的，Docker运行容器前需要本地存在对应的镜像，如果镜像不存在，Docker会先尝试从默认镜像仓库下载（默认仓库是Docker Hub公共注册服务器的仓库），用户也可以进行配置，使用自定义的镜像仓库。



#### 1、获取镜像

镜像是运行容器的前提。下载镜像的命令格式为：

```
docker [image] pull NAME[:TAG]
```

* NAME：要下载的镜像名称
* TAG是镜像的标签，往往用来表示版本的信息。

通常情况下，描述一个镜像需要包括：”名称+版本“ 信息。例如：安装一个Ubuntu 18.04的镜像，使用如下命令：

```
$docker pull ubuntu:18.04
```

上述命令相当于docker pull registry.hub.docker.com/ubuntu:18.04命令，即从默认的注册服务器docker hub registry中的ubuntu仓库来下载。如果要使用其他仓库中进行下载，比如从网易蜂巢镜像仓库下载，可以使用如下命令：

```
$docker pull hub.c.163.com/public/ubuntu:18.04
```

pull命令的子选项包括：

* -a，--all--tags = true|false：是否获取仓库中的所有镜像，默认为否；
* --disable-content-trust：取消镜像的内容校验，默认为真。



#### 2、查看镜像信息

* images：使用images命令列出本地主机上已有的镜像的基本信息。有docker images 和docker image ls两种用法。列出的镜像信息有：repository、tag、image id、created、size。images命令可以使用的主要参数有：
  * -a, --all=true|false：列出所有（包含临时）的文件镜像，默认为否；
  * --digests=true|false：列出镜像的数字摘要值，默认为否；
  * -f，--filter=[]：过滤列出的镜像
* tag：使用tag命令可以为镜像添加自定义标签，即制作一个副本，并起个新名字。具体用法为docker tag  name:tag  new_name:tag
* inspect：使用inspect命令查看详细镜像的详细信息，具体的用法为docker inspect name:tag，输出的结果是一个json格式的内容。
* history：使用history命令可以查看镜像的历史，使用方法为docker history name:tag



#### 3、搜寻镜像

Docker的搜寻命令主要是search子命令，用户可以使用docker search命令搜索Docker Hub官方仓库中的镜像。具体语法为：

```
$docker search [option] keyword
```

该命令支持的可选项参数为：

* -f，--filter filter：过滤输出内容；
* --format string：格式化输出内容；
* --limit int：限制输出结果个数，默认值为25个；
* --no-trunc：不截断输出结果；



#### 4、删除和清理镜像

删除和清理主要使用到rm和prune子命令。

* rmi和rm：在docker中可以使用docker rmi或者docker image rm命令可以删除镜像，上述命令的使用格式为：

  ```
  $docker rmi IMAGE [IMAGE...]
  ```

  上述命令中，IMAGE可以是标签或者是ID。rmi命令所支持的选项参数包括：

  * -f，-force：强制删除镜像，即使有容器依赖于该镜像，也会删除该镜像；
  * -no-prune：不要清理未带标签的父镜像；

  除了通过镜像名称来进行删除以外，还可以使用镜像ID来删除镜像：docker rmi 8f1bd21bd25c

* prune：使用Docker一段时间后，系统中可能会遗留一些临时的镜像文件，以及一些没有被使用的镜像，可以通过docker image prune命令来进行清理。该命令支持的可选项操作包括：

  * -a，-all：删除所有无用镜像，不光是临时镜像；
  * -filter filter：只清理符合给定过滤器的镜像；
  * -f，-force：强制删除镜像，而不进行提示确认；

  ```
  $docker image prune -f 该命令会自动清理临时的遗留镜像文件层，最后会提示释放的存储空间；
  ```



#### 5、创建镜像

创建镜像的方式主要有三种，分别是：基于已有镜像的容器创建、基于本地模板导入、基于Dockerfile创建。

* 基于已有容器创建：使用命令格式为docker [container] commit 命令

  ```
  docker [container] commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
  ```

  该命令可以包含的可选项主要有：

  * -a， --atuhor=""：作者信息
  * -c，--change=[]：提交的时候执行Dockerfile指令，包括CMD|ENTRYPOINT|ENV|EXPOSE|LABEL|ONBUILD|USER|VOLUME|WORKDIR等。
  * -m，--message=”“：提交消息
  * -p，--pause=true：提交时暂停容器运行

  例如，如下是提交一个新的镜像的命令：

  ```
  $docker commit -m "提交时的注释信息" -a "谁提交的" 容器id [仓库:版本号]
  ```

* 基于本地的模板导入：用户也可以直接从一个操作系统模板文件导入一个镜像，主要使用的命令是docker [container] import命令。import命令的使用格式如下为：

  ```
  $docker import [OPTIONS] file|URL|-[REPOSITORY[:TAG]]
  ```


* 基于Dockerfile创建：基于Dockerfile创建是最常见的方式，也是最应该掌握的方式。在大多数情况下，进行自定义容器编排时，都需要编写dockerfile文件。Dockerfile文件是一个文本文件，该文件可以利用给定的指令描述基于某个父镜像创建新镜像的过程。

  

#### 6、导出和载入镜像

* 导出镜像：即导出某个镜像到一个本地文件，可以使用docker save命令进行导出。该命令有-o、-output string等参数，例如如下导出镜像的命令：

  ```
  $docker save -o ubuntu_18.04.tar ubuntu:18.04
  ```

* 导入镜像：可以使用docker load命令将某个导出的tar文件再导入到本地镜像库中，该命令支持-i、-input string选项，从指定文件中读取镜像内容。比如如下导入镜像命令：

  ```
  $docker load -i ubuntu_18.04.tar
  或者
  $docker load < ubuntu_18.04.tar
  ```

  导入镜像成功后，可以使用docker images命令进行查看镜像详细信息。

#### 7、上传镜像

可以使用push命令将镜像上传的到仓库，默认会将镜像上传到Docker Hub官方仓库。具体的命令使用格式为：docker push NAME[:TAG] | [REGISTRY_HOST[:REGISTRY_PORT/]NAME[:TAG]，比如下面的使用方式：

```
docker push user/test:latest
```



