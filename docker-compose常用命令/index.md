# Docker-Compose常用命令


<!--more-->

## 简介：

Docker-Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。Compose允许用户通过一个单独的docker-compose.yml模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。Docker-Compose项目由Python编写，调用Docker服务提供的API来对容器进行管理。因此，只要所操作的平台支持Docker API，就可以在其上利用Compose来进行编排管理。

Docker-Compose将所管理的容器分为三层，分别是`工程（project）`，`服务（service）`以及`容器（container）`。Docker-Compose运行目录下的所有文件（docker-compose.yml，extends文件或环境变量文件等）组成一个工程，若无特殊指定工程名即为当前目录名。一个工程当中可包含多个服务，每个服务中定义了容器运行的镜像，参数，依赖。一个服务当中可包括多个容器实例，Docker-Compose并没有解决负载均衡的问题，**因此需要借助其它工具实现服务发现及负载均衡**。

Docker-Compose的工程配置文件默认为docker-compose.yml，可通过环境变量COMPOSE_FILE或-f参数自定义配置文件，其定义了多个有依赖关系的服务及每个服务运行的容器。
使用一个Dockerfile模板文件，可以让用户很方便的定义一个单独的应用容器。在工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个Web项目，除了Web服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。

## 一、常用命令

### docker-compose

常见的命令格式如下

```sh
$ docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
```

常见的选项包括

- -f，–file FILE指定Compose模板文件，默认为docker-compose.yml，可以多次指定。
- -p，–project-name NAME指定项目名称，默认将使用所在目录名称作为项目名。
- -x-network-driver 使用Docker的可拔插网络后端特性（需要Docker 1.9+版本）
- -x-network-driver DRIVER指定网络后端的驱动，默认为bridge（需要Docker 1.9+版本）
- -verbose输出更多调试信息
- -v，–version打印版本并退出

### docker-compose up

```sh
$ docker-compose up [options] [--scale SERVICE=NUM...] [SERVICE...]
```

选项包括：

- -d 在后台运行服务容器
- –no-color 不使用颜色来区分不同的服务的控制输出
- –no-deps 不启动服务所链接的容器
- –force-recreate 强制重新创建容器，不能与–no-recreate同时使用
- –no-recreate 如果容器已经存在，则不重新创建，不能与–force-recreate同时使用
- –no-build 不自动构建缺失的服务镜像
- –build 在启动容器前构建服务镜像
- –abort-on-container-exit 停止所有容器，如果任何一个容器被停止，不能与-d同时使用
- -t, –timeout TIMEOUT 停止容器时候的超时（默认为10秒）
- –remove-orphans 删除服务中没有在compose文件中定义的容器
- –scale SERVICE=NUM 设置服务运行容器的个数，将覆盖在compose中通过scale指定的参数

```sh
$ docker-compose up
```

启动所有服务

```sh
$ docker-compose up -d
```

在后台所有启动服务
-f 指定使用的Compose模板文件，默认为docker-compose.yml，可以多次指定。

```sh
$ docker-compose -f docker-compose.yml up -d
```

### docker-compose ps

列出项目中目前所有的容器

### docker-compose start

启动已经存在的服务容器。

```sh
$ docker-compose start [SERVICE...]
$ docker-compose start
```

### docker-compose stop

停止正在运行的容器，可以通过docker-compose start 再次启动。

```sh
$ docker-compose stop [options] [SERVICE...]
```

### docker-compose restart

重启项目中的服务。

```sh
$ docker-compose restart [options] [SERVICE...]
```

选项包括：

- -t, –timeout TIMEOUT，指定重启前停止容器的超时（默认为10秒）

### docker-compose down

停止和删除容器、网络、卷、镜像。

```sh
$ docker-compose down [options]
```

选项包括

- –rmi type，删除镜像，类型必须是：all，删除compose文件中定义的所有镜像；local，删除镜像名为空的镜像
- -v, –volumes，删除已经在compose文件中定义的和匿名的附在容器上的数据卷
- –remove-orphans，删除服务中没有在compose中定义的容器

### docker-compose logs

查看服务容器的输出。默认情况下，docker-compose将对不同的服务输出使用不同的颜色来区分。可以通过–no-color来关闭颜色。

```sh
$ docker-compose logs [options] [SERVICE...]
```

### docker-compose build

构建（重新构建）项目中的服务容器。

```sh
$ docker-compose build [options] [--build-arg key=val...] [SERVICE...]
```

选项包括：

- –compress 通过gzip压缩构建上下环境
- –force-rm 删除构建过程中的临时容器
- –no-cache 构建镜像过程中不使用缓存
- –pull 始终尝试通过拉取操作来获取更新版本的镜像
- -m, –memory MEM为构建的容器设置内存大小
- –build-arg key=val为服务设置build-time变量

### docker-compose pull

拉取服务依赖的镜像。

```sh
$ docker-compose pull [options] [SERVICE...]
```

选项包括：

- –ignore-pull-failures，忽略拉取镜像过程中的错误
- –parallel，多个镜像同时拉取
- –quiet，拉取镜像过程中不打印进度信息

### docker-compose rm

删除所有（停止状态的）服务容器。

```sh
$ docker-compose rm [options] [SERVICE...]
```

选项包括：
–f, –force，强制直接删除，包括非停止状态的容器
-v，删除容器所挂载的数据卷

### docker-compose run

在指定服务上执行一个命令。

```sh
$ docker-compose run [options] [-v VOLUME...] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND] [ARGS...]
```

## 二、docker-compose 模版文件

Compose允许用户通过一个docker-compose.yml模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

Compose模板文件是一个定义服务、网络和卷的YAML文件。Compose模板文件默认路径是当前目录下的docker-compose.yml，可以使用.yml或.yaml作为文件扩展名。

Docker-Compose标准模板文件应该包含version、services、networks 三大部分，最关键的是services和networks两个部分。

先来看一份 docker-compose.yml 文件：

```yaml
version: '2'
services:
  web:
    image: dockercloud/hello-world
    ports:
      - 8080
    networks:
      - front-tier
      - back-tier

  redis:
    image: redis
    links:
      - web
    networks:
      - back-tier

  lb:
    image: dockercloud/haproxy
    ports:
      - 80:80
    links:
      - web
    networks:
      - front-tier
      - back-tier
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock 

networks:
  front-tier:
    driver: bridge
  back-tier:
driver: bridge
```

可以看到一份标准配置文件应该包含 version、services、networks 三大部分，其中最关键的就是 services 和 networks 两个部分，下面先来看 services 的书写规则。

### 1. image

```yaml
services:
  web:
    image: hello-world
```

在 services 标签下的第二级标签是 web，这个名字是用户自己自定义，它就是服务名称。
 image 则是指定服务的镜像名称或镜像 ID。如果镜像在本地不存在，Compose 将会尝试拉取这个镜像。
 例如下面这些格式都是可以的：

```yaml
image: redis
image: ubuntu:14.04
image: tutum/influxdb
image: example-registry.com:4000/postgresql
image: a4bc65fd
```

### 2. build

服务除了可以基于指定的镜像，还可以基于一份 Dockerfile，在使用 up 启动之时执行构建任务，这个构建标签就是 build，它可以指定 Dockerfile 所在文件夹的路径。Compose 将会利用它自动构建这个镜像，然后使用这个镜像启动服务容器。

```yaml
build: /path/to/build/dir
```

也可以是相对路径，只要上下文确定就可以读取到 Dockerfile。

```yaml
build: ./dir
```

设定上下文根目录，然后以该目录为准指定 Dockerfile。

```yaml
build:
  context: ../
  dockerfile: path/of/Dockerfile
```

注意 build 都是一个目录，如果你要指定 Dockerfile 文件需要在 build 标签的子级标签中使用 dockerfile 标签指定，如上面的例子。
 如果你同时指定了 image 和 build 两个标签，那么 Compose 会构建镜像并且把镜像命名为 image 后面的那个名字。

```yaml
build: ./dir
image: webapp:tag
```

既然可以在 docker-compose.yml 中定义构建任务，那么一定少不了 arg 这个标签，就像 Dockerfile 中的 ARG 指令，它可以在构建过程中指定环境变量，但是在构建成功后取消，在 docker-compose.yml 文件中也支持这样的写法：

```yaml
build:
  context: .
  args:
    buildno: 1
    password: secret
```

下面这种写法也是支持的，一般来说下面的写法更适合阅读。

```yaml
build:
  context: .
  args:
    - buildno=1
    - password=secret
```

与 ENV 不同的是，ARG 是允许空值的。例如：

```yaml
args:
  - buildno
  - password
```

这样构建过程可以向它们赋值。

> 注意：YAML 的布尔值（true, false, yes, no, on, off）必须要使用引号引起来（单引号、双引号均可），否则会当成字符串解析。

### 3. command

使用 command 可以覆盖容器启动后默认执行的命令。

```yaml
command: bundle exec thin -p 3000
```

也可以写成类似 Dockerfile 中的格式：

```yaml
command: [bundle, exec, thin, -p, 3000]
```

### 4. container_name

Compose 的容器名称格式是：<项目名称>*<服务名称>*<序号>
虽然可以自定义项目名称、服务名称，但是如果你想完全控制容器的命名，可以使用这个标签指定：

```yaml
container_name: app
```

这样容器的名字就指定为 app 了。

### 5. depends_on

在使用 Compose 时，最大的好处就是少打启动命令，但是一般项目容器启动的顺序是有要求的，如果直接从上到下启动容器，必然会因为容器依赖问题而启动失败。
 例如在没启动数据库容器的时候启动了应用容器，这时候应用容器会因为找不到数据库而退出，为了避免这种情况我们需要加入一个标签，就是 depends_on，这个标签解决了容器的依赖、启动先后的问题。
 例如下面容器会先启动 redis 和 db 两个服务，最后才启动 web 服务：

```yaml
version: '2'
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```

注意的是，默认情况下使用 docker-compose up web 这样的方式启动 web 服务时，也会启动 redis 和 db 两个服务，因为在配置文件中定义了依赖关系。

### 6. dns

和 --dns 参数一样用途，格式如下：

```yaml
dns: 8.8.8.8
```

也可以是一个列表：

```yaml
dns:
  - 8.8.8.8
  - 9.9.9.9
```

此外 dns_search 的配置也类似：

```yaml
dns_search: example.com
dns_search:
  - dc1.example.com
  - dc2.example.com
```

### 7. tmpfs

挂载临时目录到容器内部，与 run 的参数一样效果：

```yaml
tmpfs: /run
tmpfs:
  - /run
  - /tmp
```

### 8. entrypoint

在 Dockerfile 中有一个指令叫做 ENTRYPOINT 指令，用于指定接入点，第四章有对比过与 CMD 的区别。
 在 docker-compose.yml 中可以定义接入点，覆盖 Dockerfile 中的定义：

```yaml
entrypoint: /code/entrypoint.sh
```

格式和 Docker 类似，不过还可以写成这样：

```yaml
entrypoint:
    - php
    - -d
    - zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so
    - -d
    - memory_limit=-1
    - vendor/bin/phpunit
```

### 9. env_file

.env 文件可以设置 Compose 的变量。而在 docker-compose.yml 中可以定义一个专门存放变量的文件。
 如果通过 docker-compose -f FILE 指定了配置文件，则 env_file 中路径会使用配置文件路径。

如果有变量名称与 environment 指令冲突，则以后者为准。格式如下：

```yaml
env_file: .env
```

或者根据 docker-compose.yml 设置多个：

```yaml
env_file:
  - ./common.env
  - ./apps/web.env
  - /opt/secrets.env
```

注意的是这里所说的环境变量是对宿主机的 Compose 而言的，如果在配置文件中有 build 操作，这些变量并不会进入构建过程中，如果要在构建中使用变量还是首选前面刚讲的 arg 标签。

### 10. environment

与上面的 env_file 标签完全不同，反而和 arg 有几分类似，这个标签的作用是设置镜像变量，它可以保存变量到镜像里面，也就是说启动的容器也会包含这些变量设置，这是与 arg 最大的不同。
 一般 arg 标签的变量仅用在构建过程中。而 environment 和 Dockerfile 中的 ENV 指令一样会把变量一直保存在镜像、容器中，类似 docker run -e 的效果。

```yaml
environment:
  RACK_ENV: development
  SHOW: 'true'
  SESSION_SECRET:

environment:
  - RACK_ENV=development
  - SHOW=true
  - SESSION_SECRET
```

### 11. expose

这个标签与Dockerfile中的EXPOSE指令一样，用于指定暴露的端口，但是只是作为一种参考，实际上docker-compose.yml的端口映射还得ports这样的标签。

```yaml
expose:
 - "3000"
 - "8000"
```

### 12. external_links

在使用Docker过程中，我们会有许多单独使用docker run启动的容器，为了使Compose能够连接这些不在docker-compose.yml中定义的容器，我们需要一个特殊的标签，就是external_links，它可以让Compose项目里面的容器连接到那些项目配置外部的容器（前提是外部容器中必须至少有一个容器是连接到与项目内的服务的同一个网络里面）。
 格式如下：

```yaml
external_links:
 - redis_1
 - project_db_1:mysql
 - project_db_1:postgresql
```

### 13. extra_hosts

添加主机名的标签，就是往/etc/hosts文件中添加一些记录，与Docker client的--add-host类似：

```yaml
extra_hosts:
 - "somehost:162.242.195.82"
 - "otherhost:50.31.209.229"
```

启动之后查看容器内部hosts：

```yaml
162.242.195.82  somehost
50.31.209.229   otherhost
```

### 14. labels

向容器添加元数据，和Dockerfile的LABEL指令一个意思，格式如下：

```csharp
labels:
  com.example.description: "Accounting webapp"
  com.example.department: "Finance"
  com.example.label-with-empty-value: ""
labels:
  - "com.example.description=Accounting webapp"
  - "com.example.department=Finance"
  - "com.example.label-with-empty-value"
```

15. links

还记得上面的depends_on吧，那个标签解决的是启动顺序问题，这个标签解决的是容器连接问题，与Docker client的--link一样效果，会连接到其它服务中的容器。
 格式如下：

```yaml
links:
 - db
 - db:database
 - redis
```

使用的别名将自动在服务容器中的/etc/hosts里创建。例如：

```yaml
172.12.2.186  db
172.12.2.186  database
172.12.2.187  redis
```

相应的环境变量也将被创建。

### 16. logging

这个标签用于配置日志服务。格式如下：

```yaml
logging:
  driver: syslog
  options:
    syslog-address: "tcp://192.168.0.42:123"
```

默认的driver是json-file。只有json-file和journald可以通过docker-compose logs显示日志，其他方式有其他日志查看方式，但目前Compose不支持。对于可选值可以使用options指定。
 有关更多这方面的信息可以阅读官方文档：
 [https://docs.docker.com/engine/admin/logging/overview/](https://link.jianshu.com?t=https://docs.docker.com/engine/admin/logging/overview/)

### 17. pid

```yaml
pid: "host"
```

将PID模式设置为主机PID模式，跟主机系统共享进程命名空间。容器使用这个标签将能够访问和操纵其他容器和宿主机的名称空间。

### 18. ports

映射端口的标签。
 使用HOST:CONTAINER格式或者只是指定容器的端口，宿主机会随机映射端口。

```yaml
ports:
 - "3000"
 - "8000:8000"
 - "49100:22"
 - "127.0.0.1:8001:8001"
```

> 注意：当使用HOST:CONTAINER格式来映射端口时，如果你使用的容器端口小于60你可能会得到错误得结果，因为YAML将会解析xx:yy这种数字格式为60进制。所以建议采用字符串格式。

### 19. security_opt

为每个容器覆盖默认的标签。简单说来就是管理全部服务的标签。比如设置全部服务的user标签值为USER。

```yaml
security_opt:
  - label:user:USER
  - label:role:ROLE
```

### 20. stop_signal

设置另一个信号来停止容器。在默认情况下使用的是SIGTERM停止容器。设置另一个信号可以使用stop_signal标签。

```yaml
stop_signal: SIGUSR1
```

### 21. volumes

挂载一个目录或者一个已存在的数据卷容器，可以直接使用 [HOST:CONTAINER] 这样的格式，或者使用 [HOST:CONTAINER:ro] 这样的格式，后者对于容器来说，数据卷是只读的，这样可以有效保护宿主机的文件系统。
docker-compose支持两种方式设置持久化的文件

```yaml
servicename:
  image: image-name
  volumes:
  - /path/to/file:/path/to/container/file
```

这种方式将文件直接挂载到容器中，使用起来比较直观，但是需要管理本地路径。

```yaml
servicename:
  image: image-name
  volumes:
  - volume-name:/path/to/container/file
volumes:
  volume-name: /path/to/local/file
```

 Compose的数据卷指定路径可以是相对路径，使用 . 或者 .. 来指定相对目录。
 数据卷的格式可以是下面多种形式：

```yaml
volumes:
  // 只是指定一个路径，Docker 会自动在创建一个数据卷（这个路径是容器内部的）。
  - /var/lib/mysql

  // 使用绝对路径挂载数据卷
  - /opt/data:/var/lib/mysql

  // 以 Compose 配置文件为中心的相对路径作为数据卷挂载到容器。
  - ./cache:/tmp/cache

  // 使用用户的相对路径（~/ 表示的目录是 /home/<用户目录>/ 或者 /root/）。
  - ~/configs:/etc/configs/:ro

  // 已经存在的命名的数据卷。
  - datavolume:/var/lib/mysql
```

如果你不使用宿主机的路径，你可以指定一个volume_driver。

```yaml
volume_driver: mydriver
```

使用`docker volume ls`命令可以查看本地挂载的文件。
使用`docker volume inspect volume-name`命令可以查看具体的真实地址。

### 22. volumes_from

从其它容器或者服务挂载数据卷，可选的参数是 :ro或者 :rw，前者表示容器只读，后者表示容器对数据卷是可读可写的。默认情况下是可读可写的。

```yaml
volumes_from:
  - service_name
  - service_name:ro
  - container:container_name
  - container:container_name:rw
```

### 23. cap_add, cap_drop

添加或删除容器的内核功能。

```yaml
cap_add:
  - ALL

cap_drop:
  - NET_ADMIN
  - SYS_ADMIN
```

### 24. cgroup_parent

指定一个容器的父级cgroup。

```yaml
cgroup_parent: m-executor-abcd
```

### 25. devices

设备映射列表。与Docker client的--device参数类似。

```yaml
devices:
  - "/dev/ttyUSB0:/dev/ttyUSB0"
```

### 26. extends

这个标签可以扩展另一个服务，扩展内容可以是来自在当前文件，也可以是来自其他文件，相同服务的情况下，后来者会有选择地覆盖原有配置。

```yaml
extends:
  file: common.yml
  service: webapp
```

用户可以在任何地方使用这个标签，只要标签内容包含file和service两个值就可以了。file的值可以是相对或者绝对路径，如果不指定file的值，那么Compose会读取当前YML文件的信息。
 更多的操作细节在后面的12.3.4小节有介绍。

### 27. network_mode

网络模式，与Docker client的--net参数类似，只是相对多了一个service:[service name] 的格式。
 例如：

```yaml
network_mode: "bridge"
network_mode: "host"
network_mode: "none"
network_mode: "service:[service name]"
network_mode: "container:[container name/id]"
```

可以指定使用服务或者容器的网络。

### 28. networks

加入指定网络，格式如下：

```yaml
services:
  some-service:
    networks:
     - some-network
     - other-network
```

关于这个标签还有一个特别的子标签aliases，这是一个用来设置服务别名的标签，例如：

```yaml
services:
  some-service:
    networks:
      some-network:
        aliases:
         - alias1
         - alias3
      other-network:
        aliases:
         - alias2
```

相同的服务可以在不同的网络有不同的别名。

### 29. 其它

还有这些标签：cpu_shares, cpu_quota, cpuset, domainname, hostname, ipc, mac_address, mem_limit, memswap_limit, privileged, read_only, restart, shm_size, stdin_open, tty, user, working_dir
 上面这些都是一个单值的标签，类似于使用docker run的效果。

```yaml
cpu_shares: 73
cpu_quota: 50000
cpuset: 0,1

user: postgresql
working_dir: /code

domainname: foo.com
hostname: foo
ipc: host
mac_address: 02:42:ac:11:65:43

mem_limit: 1000000000
memswap_limit: 2000000000
privileged: true

restart: always

read_only: true
shm_size: 64M
stdin_open: true
tty: true
```

> Compose目前有三个版本分别为Version 1，Version 2，Version 3，Compose区分Version 1和Version 2（Compose 1.6.0+，Docker Engine 1.10.0+）。Version 2支持更多的指令。Version 1将来会被弃用。

# 三、参考链接

- [Docker Compose 配置文件详解](https://www.jianshu.com/p/2217cfed29d7)

