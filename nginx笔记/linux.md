linux 命令

#  上网配置
/etc/sysconfig/network-scripts目录下ifcfg-eth0或类似文件
    ONBOOT=yes
service network restart

#  查看内核版本
    uname -r
#  更新内核
    yum update
# 权限
## chmod 权限 赋予权限的文件
# 文件的权限字符为：『-rwxrwxrwx』， 这九个权限是三个三个一组的！其中，我们可以使用数字来代表各个权限，各权限的分数对照表如下：
    r:4  w:2   x:1
    每种身份(owner/group/others)各自的三个权限(r/w/x)分数是需要累加的，例如当权限为： [-rwxrwx---] 分数则是：
    owner = rwx = 4+2+1 = 7
    group = rwx = 4+2+1 = 7
    others= --- = 0+0+0 = 0
    所以等一下我们设定权限的变更时，该文件的权限数字就是770啦！
    chomod 770 文件

# Linux常见的文件类型有：普通文件、目录文件、字符设备文件和块设备文件、符号链接文件等，其中：
    普通文件的文件权限第一个字符为“-”
    目录文件的文件权限第一个字符为“d”
    字符设备文件的文件权限第一个字符为“c”；块设备文件的文件权限第一个字符为“b”
    符号链接文件的文件权限第一个字符为“s”

## chown
## 非root用户没有权限的操作，在命令前使用 sudo 

# 开机启动项
##  查看开机自启项 和 是否自启状态
    systemctl list-unit-files
##  开机启动
    sytemctl enable xxxx
##  开机自启
1. 服务（例如：mysql.server）
   将服务添加（拷贝）到 /etc/init.d/目录下
   cp mysql.server /etc/init.d/
   chkconfig命令配置开机自启
   chkconfig --add mysql.server
   chkconfig mysql.server on
2. 自定义脚本
    vi /etc/rc.d/rc.local
    末尾添加
    启动脚本路径 /home/myscript/NginxStart.sh
    有些脚本无法启动，可能导致其他脚本也无法启动
    注意文件里的注释
    ##  Please note that you must run 'chmod +x /etc/rc.d/rc.local' to ensure
    ##  that this script will be executed during boot
    在centos 7中，该文件权限降低，没有启动权限
    分配权限
    chmod +x /etc/rc.d/rc.local

    reboot重启系统查看效果

# 查看时间日期
1. 查看当前时区
date -R

2. 修改设置Linux服务器时区
方法 A：tzselect
方法 B 仅限于RedHat Linux 和 CentOS ： timeconfig
方法 C 适用于Debian ： dpkg-reconfigure tzdata

3. 复制相应的时区文件，替换系统时区文件；或者创建链接文件
cp /usr/share/zoneinfo/$主时区/$次时区 /etc/localtime
例如：在设置中国时区使用亚洲/上海（+8）
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 修改Linux的时间
1. 查看时间和日期
    date

2. 设置时间和日期
例如：将系统日期设定成2009年11月3日的命令
    date -s 11/03/2009

将系统时间设定成下午5点55分55秒的命令
    date -s 17:55:55

网络时间同步ntpdate
    安装工具 yum -y install ntp ntpdate
    设置系统时间与网络时间同步 ntpdate cn.pool.ntp.org
    将系统时间写入硬件 hwclock --systohc //可不做

3. 将当前时间和日期写入BIOS，避免重启后失效
    hwclock -w

4. 注：
date
    不加参数可以直接看到当前日期时间
    cal
    不加参数可以直接看到本月月历

    timedatectl list-timezones #  列出所有时区
    timedatectl set-local-rtc 1 #  将硬件时钟调整为与本地时钟一致, 0 为设置为 UTC 时间
    timedatectl set-timezone Asia/Shanghai #  设置系统时区为上海

# firewalld
    firewall-cmd --zone=public --add-port=3306/tcp --permanent （--permanent永久生效，没有此参数重启后失效）
    firewall-cmd --permanent --add-port=15672/tcp  (这样写就可以)
    firewall-cmd --permanent --zone=public --add-port=8080-9999/tcp //永久
    firewall-cmd --zone=public --remove-port=3306/tcp --permanent

#  官方的yum源在国内访问效果不佳。需要改为国内比较好的阿里云或者网易的yum源
##  在/etc/yum..repos.d/下进行如下操作(请做好相应备份)：
    wget http://mirrors.163.com/.help/CentOS7-Base-163.repo
    mv CentOS7-Base-163.repo  CentOS-Base.repo

#  重建缓存：
    yum clean all
    yum makecache

# vi 和 vim显示行号
##  1.当前用户
    vim ~./vimrc
    写入 set nu
    vi 则是 virc
    手动加载配置 source .vimrc
##  2.所有用户
    vim /etc/vimrc
    写入 set nu
    source /etc/bashrc

#  查看端口占用状态
## 安装
    yum -y install net-tools
## 命令
    netstat -ntlp|grep 80
    windows : netstat -aon|findstr "80" ##端口信息
              tasklist|findstr "2016" ##进程信息
              taskkill -PID 2016 -F 强制终止进程
#  查看磁盘容量的使用情况
    df -h
#  查看目录的大小　　加上-c选项后,会最后一行统计它们的总和
    du -shc 目录
#  查看子目录或文件的大小
    du -shc 目录/*
  
#  安装expect
    yum install -y expect

# 文件操作
## 打包压缩文件
###  tar -参数 压缩后的文件名 需要压缩的文件或目录
        解包：tar xvf FileName.tar 
        打包：tar cvf FileName.tar DirName 
###  .gz 
　　    解压 1：gunzip FileName.gz 
　　    解压2：gzip -d FileName.gz 
　　    压缩：gzip FileName 
###  .tar.gz 和 .tgz 
　　    解压：tar zxvf FileName.tar.gz 
　　    压缩：tar zcvf FileName.tar.gz DirName 

###  zip 压缩后的文件名 需要压缩的文件
###  zip -r 压缩后的文件名 需要压缩的文件夹
        压缩一个目录使用 -r 参数，-r 递归
        解压：unzip FileName.zip 

##  下载文件到本地/本地上传文件
现在大多数Linux服务器都带有lrzsz程序，方便上传下载文件，如果没有，可以直接下载rpm包安装下就好。
    # yum install -y lrzsz
    # sz 文件名   发送到本地   
    # rz         本地上传

    # options–>session options–>X/Y/Zmodem
##  删除文件
    rm -命令 文件路径

##  创建文件夹
    mkdir
##  创建文件
    touch test.sh
    # 授权可执行文件
        chomd 777 test.sh

## 查找文件
    查找目录：find /（查找范围） -name '查找关键字' -type d
    查找文件：find /（查找范围） -name 查找关键字 -print

## 重命名文件
    重命名文件或文件夹：mv
        mv A B 将目录A重命名为B
        mv /a /b/c 将/a目录移动到/b目录下并重命名为c
    根据正则重命名：rename
        rename [options] [perlexpr] [files]
            option
                -v 显示成功重命名的文件
                -n 只显示将被重命名的文件，但是不进行重命名
                -f 如果文件已存在，覆盖
                -e 表明需要需要被执行的code，如果没有-e，那么第一个参数将被当成code
            perlexpr
                -Perl表达式，文件名应当在其中被改变
            files
                -需要被重命名的文件
        例子：
            把文件扩展名为.bak 的文件扩展名去掉
                $ rename 's/\.bak$//' *.bak
            去除文件名中的空格
                $ rename 's/ //g' *
            文件名转为小写
                $ rename 'y/A-Z/a-z/' *

## 批量正则 查找 删除文件
    find . -type f -name "platform.log.*-07-*.*"       | xargs rm
    查找 当前目录 类型为文件(文件:f,目录:d) 名称关键字正则   分批次 对查出的文件继续之后的命令，不一定是删除，也可以二次查找 
# 用户和用户组
## 查看用户和用户组的方法 
 用户列表文件：/etc/passwd 
 用户组列表文件：/etc/group 
 查看系统中有哪些用户：cut -d : -f 1 /etc/passwd 
 查看可以登录系统的用户：cat /etc/passwd | grep -v /sbin/nologin | cut -d : -f 1 
 查看用户操作：w命令(需要root权限) 
 查看某一用户：w 用户名 
 查看登录用户：who
 查看用户登录历史记录：last
## 1.新建用户 
 adduser testuser //新建testuser 用户 
 passwd testuser //给testuser 用户设置密码
## 2.建工作组 
 groupadd testgroup //新建test工作组
## 3.新建用户同时增加工作组 
 useradd -g testgroup testuser //新建testuser用户并增加到testgroup工作组
//注：：-g 所属组 -d 家目录 -s 所用的SHELL
## 4.给已有的用户增加工作组 
 usermod -G groupname username
## 5.临时关闭 
 在/etc/shadow文件中属于该用户的行的第二个字段（密码）前面加上就可以了。想恢复该用户，去掉即可 
 //或者使用如下命令关闭用户账号： 
 passwd testuser –l 
 //重新释放： 
 passwd testuser –u
## 6.永久性删除用户账号 
 userdel testuser 
 groupdel testgroup 
 usermod –G testgroup testuser //（强制删除该用户的主目录和主目录下的所有文件和子目录）
## 7.显示用户信息 
 id user 
 cat /etc/passwd



# 环境变量
    ./etc/profile



# 保证虚拟机每次开机的局域网ip都保持固定不变
    如果centos系统建立在虚拟机之上，那么在设置虚拟机的网络时请选择‘网桥适配器’连接，网桥
    （更改适配器设置-》ctrl选择本机的网络和虚拟机的网络-》右键桥接）

    修改 /etc/sysconfig/network-scripts/ifcfg-eth0 或 以ifcfg-开始的非ifcfg-lo的文件
        BOOTPROTO=static # 设置网卡获得ip地址的方式，选项可以为为static，dhcp或bootp
        IPADDR=192.168.0.119 # 只有网卡设置成static时，才需要此字段
        NETMASK=255.255.255.0 # 网卡对应的网络掩码
        NETWORK=192.168.0.1 # 网卡对应的网络地址，也就是所属的网段
        ONBOOT=yes # 系统启动时是否设置此网络接口，设置为yes时，系统启动时激活此设备

    /etc/sysconfig/network 设置永久网关
        NETWORKING=yes # 表示系统是否使用网络，一般设置为yes。如果设为no，则不能使用网络。
        HOSTNAME=localhost.localdomain # 设置本机的主机名，这里设置的主机名要和/etc/hosts中设置的主机名对应，或/etc/hostname
        GATEWAY=192.168.0.1 # 设置本机连接的网关的IP地址。

    /etc/resolv.conf 设置域名服务器
        nameserver 8.8.8.8 # google域名服务器 
        nameserver 8.8.4.4 # google域名服务器


# 查看ip
    ifconfig
# 查看Gateway
    netstat -rn 或 route -n 
# 查看DNS
    cat /etc/resolv.conf

# nginx
## 安装 make 和 g++ ,一般系统已安装无需在安装
    yum -y install autoconf automake make
    yum -y install gcc gcc-c++
## 安装nginx前安装
        yum -y install zlib zlib-devel | gzip模块需要 zlib 库
        yum -y install pcre pcre-devel | rewrite模块需要 pcre 库
        yum -y install openssl openssl-devel | ssl 功能需要openssl库
## 下载
        cd /usr/local  # 个人是压缩包下载到 创建的 /soft/目录 安装解压到 /usr/local/目录
        wget  http://nginx.org/download/nginx-VERSION.tar.gz # 或自行本地下载上传
        tar -zvxf nginx-1.16.1.tar.gz
## 到解压目录下执行 ./configure
    # --prefix指定安装目录
    # --with-http_ssl_module安装https模块
    # creating objs/Makefile 代表编译成功
        ./configure   --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module 
## make编译,make install安装
        make & make install
## 启停命令：
        ./nginx -c nginx.conf的文件。如果不指定，默认为NGINX_HOME/conf/nginx.conf
        ./nginx -s stop  停止
        ./nginx -s quit退出
        ./nginx -s reload 重新加载nginx.conf
## 日志切割
        编写sh：
            # !/bin/bash
            # 设置日志文件存放目录
            LOG_HOME=/usr/local/nginx/logs/
            # 备分文件名称
            LOG_PATH_BAK=$(date -d yesterday +%Y%m%d%H%M)
            # 重命名日志文件
            mv ${LOG_HOME}/access.log ${LOG_HOME}/access.${LOG_PATH_BAK}.log
            mv ${LOG_HOME}/error.log ${LOG_HOME}/error.${LOG_PATH_BAK}.log
            # 向nginx主进程发信号重新打开日志
            kill -USR1 cat ${LOG_HOME}/nginx.pid

        配置cron：（centos7 命令 crontab -e）
            */1 * * * * /usr/local/nginx/sbin/logcut.sh
## nginx安装第三方模块echo
        使用第三方模块ngx_echo的功能，请重新配置添加到nginx插件中

        # # 下载第三方模块
            wget https://github.com/openresty/echo-nginx-module/archive/v0.61.tar.gz
            tar -zxvf v0.61.tar.gz		# # 解压
            cd nginx-VERSION			# # 进入nginx源码目录，准备重新配置nginx
        # # 配置，--add-module指向模块目录即会安装插件到nginx中
        ./configure --add-module=/usr/local/src/echo-nginx-module-0.61/ 
        make && make install

# 卸载 centos7 自带 openjdk
    yum -y remove java
    rm -rf /usr/bin/java
# 安装 jdk
    官网下载解压
    配置环境变量
        vi /etc/profile
        
        JAVA_HOME=/usr/local/jdk-version
        CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar # 注意这里是分号隔开
        export JAVA_HOME CLASSPATH
        export PATH=$PATH:$JAVA_HOME/bin

        source /etc/profile 立即生效命令

# 安装maven
    下载解压
    配置环境变量
        vi /etc/profile

        export MAVEN_HOME=/usr/local/apache-maven-3.6.1
        export PATH=$PATH:$JAVA_HOME/bin:/usr/local/mysql/bin:$MAVEN_HOME/bin

        source /etc/profile 立即生效命令

# 安装git
    yum -y install git

# nacos
## 您可以从 最新稳定版本(https://github.com/alibaba/nacos/releases) 下载 nacos-server-$version.zip 包。
    unzip nacos-server-$version.zip 或者 tar -xvf nacos-server-$version.tar.gz
    cd nacos/bin
## 启动命令(standalone代表着单机模式运行，非集群模式):
    ./startup.sh -m standalone

# 安装卸载mysql
##    yum安装
    wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
    yum -y install mysql57-community-release-el7-10.noarch.rpm
    yum -y install mysql-community-server
    systemctl start  mysqld.service
    systemctl status mysqld.service

    rpm -qa|grep -i mysql 查看mysql的安装情况
    rpm -ev mysql-version.el6_8.x86_64 停止服务，删除Mysql
    注意：可能会抛依赖错误错 error:Failed dependencies:
    加上 --nodeps,不检查依赖关系
    find / -name mysql | xargs rm -rf 删除遗留的mysql 文件
    rm -rf /etc/my.cnf 删除mysql配置文件（一般不删，centos安装自带）

##    源码安装
    tar压缩包解压到/usr/local
    groupadd mysql 添加组
    groupdel mysql 删除组
    useradd -r -g mysql mysql 创建用户添加到组
    ln -s full-path-to-mysql-VERSION-OS mysql 创建软连接 （快捷方式）
    cd mysql
    mkdir mysql-files
    chmod 770 mysql-files                    # linux权限
    chown -R mysql .                         # linux权限
    chgrp -R mysql .                         # linux权限
    bin/mysqld --initialize --user=mysql     #  MySQL  and up   最后有密码 bzllc%ow,7oJ
    bin/mysql_ssl_rsa_setup                  #  MySQL  and up   
    chown -R root .                          # linux权限
    chown -R mysql data mysql-files          # linux权限
    bin/mysqld_safe --user=mysql &           # 启动

## 配置环境变量 (配置好后可用mysql -u -p 进入mysql 命令)
        vi /etc/profile
            加上 export PATH=/usr/local/mysql/bin:$PATH
        source /etc/profile 立即生效
## 配置开机自启
        cp support-files/mysql.server /etc/init.d/mysql.server # 复制服务到/etc/init.d/目录下
        chkconfig mysql.server on
## 查看开机自启项
        chkconfig --list

## 允许远程登录
        GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root';
        GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
        flush privileges;  
    
    本地仍无法连接mysql---虚拟机centos端口对外开放是否
    防火墙端口对外开放（博客）
    firewalld 命令配置开放端口 
    iptables 配置文件配置开放端口
    
## 主从配置 需要my.cnf文件
    find / |grep my.cnf 找到类似文件 cp 文件 /etc/my.cnf 在做相应修改
    博客有文章
## 在安装centos的时候，哪怕是mini版本都会有个默认的配置在/etc目录中
## my.cnf 备份
    # 
    #  These groups are read by MariaDB command-line tools
    #  Use it for options that affect only one utility
    # 

    [mysql]

    [mysql_upgrade]

    [mysqladmin]

    [mysqlbinlog]

    [mysqlcheck]

    [mysqldump]

    [mysqlimport]

    [mysqlshow]

    [mysqlslap]

    [mysqld]
    log-bin=mysql-bin # 主从配置
    server-id=1


# redis
## 下载安装
     wget http://download.redis.io/releases/redis-version.tar.gz # 可以本地下载再上传
     tar xzf redis-version.tar.gz
     cd redis-version
     make  #  make完后 redis-version 目录下会出现编译后的redis服务程序redis-server,还有用于测试的客户端程序redis-cli,两个程序位于安装目录 src 目录下

## 默认配置启动
     cd src 
     ./redis-server
     # 指定配置启动
     ./redis-server ../redis.conf

## 远程连接redis 拒绝连接问题
    防火墙firewall已开启端口
    修改redis.conf配置文件
    注释绑定的主机地址
    # bind 127.0.0.1 

# zookeeper
## 下载解压
## 将解压目录下zoo_sample.cfg复制并重命名为zoo.cfg
    zoo.cfg为zookeeper指定启动配置文件
## 修改zoo.cfg
    #  存放数据文件
    dataDir=/tmp/zookeeper
    #  存放日志文件
    dataLogDir=/usr/local/apache-zookeeper-3.5.5-bin/logs
    # 集群 配置多个zk节点 2888为选举端口，3888为心跳端口
    # 此处配置三台机器，集群配置为奇数，官方推荐最小集群为三个
    server.1=192.168.0.117:2888:3888
    server.2=192.168.0.118:2888:3888
    server.3=192.168.0.119:2888:3888
## 配置myid
    在我们zoo.cfg配置的dataDir指定目录下，分别创建一个myid文件
    内容为zoo.cfg配置的server.number中的数字number,用来标识当前主机
    (三台机器myid内容分别为1,2,3)
## 基本命令
    bin目录下、
    # 启动
    ./zkServer.sh start
    # 停止
    ./zkServer.sh stop
    # 查看状态（集群有角色信息，Leader和Follower）
    ./zkServer.sh status
    # 客户端连接 集群配置，客户端连的是一个整体，连接本地客户端独享整个集群服务
    ./zkCli.sh -server 192.168.0.117:2181 # 2181是客户端端口,zoo.cfg中有

    # 启动失败
    删除dataDir 和 dataLogDir文件夹下的version-2 文件夹

# docker
1. 可以直接 yum -y install docker
2. 官方安装文档 https://docs.docker.com/install
3. 设置稳定存储库
    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
4. 安装最新版本的Docker Engine - 社区和容器
    yum install docker-ce docker-ce-cli containerd.io
5. 要安装特定版本的Docker Engine - Community，请列出repo中的可用版本，然后选择并安装
    yum list docker-ce --showduplicates | sort -r
6. 返回的列表取决于启用的存储库，并且特定于您的CentOS版本
    yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
7. 启动Docker
    systemctl start docker
    sudo docker run hello-world

8. 允许远程
 vi /usr/lib/systemd/system/docker.service

 ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2375

9. SpringBoot项目(单模块、多模块)使用docker容器运行jar包镜像
 <!--mavem docker插件配置-->
    <!-- Docker maven plugin -->
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>1.0.0</version>
                <configuration>
                    <imageName>${docker.image.prefix}.${project.artifactId}</imageName>
                    <dockerDirectory>src/main/docker</dockerDirectory>
                    <resources>
                        <resource>
                            <targetPath>/</targetPath>
                            <directory>${project.build.directory}</directory>
                            <include>${project.build.finalName}.jar</include>
                        </resource>
                    </resources>
                </configuration>
            </plugin>
    <!-- Docker maven plugin -->
<!--DockerFile配置文件-->
    FROM openjdk:8-jdk-alpine
    VOLUME /tmp
    ADD love-master-0.0.1-SNAPSHOT.jar love-master.jar
    ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/love-master.jar"]
<!--配置完成后使用maven命令生成docker镜像-->
  <!--单模块项目情况-->
    # ! /bin/bash 
    # 首先移除原来的镜像
    docker stop containerName 
    docker rm containerName 
    docker rmi imageName
    # 找到项目所在目录
    cd /home/project/projectName/ 
    # 执行maven命令生成jar包和镜像
    mvn clean package -Ptest -Dmaven.test.skip=true docker:build
    # 查看镜像是否存在
    docker images
    # 运行镜像
    docker run -p 8761:8761 --name containerName -d imageName
 <!--多模块项目情况-->
    # ! /bin/bash 
    # 首先移除原来的镜像
    docker stop containerName 
    docker rm containerName 
    docker rmi imageName
    # 找到项目所在目录
    cd /home/program/parentProject/
    # 根目录下进行 install
    mvn clean install package -Dmaven.test.skip=true
    # 进入web模块下
    cd moduleProject/
    # 执行maven命令生成jar包和镜像
    mvn package docker:build -Dmaven.test.skip=true
    # 查看镜像是否存在
    docker images
    # 运行镜像（参考日志下的运行）
    docker run -p 8080:8080 --name containerName -d imageName
10. 日志
 docker logs -f -t --since="2017-05-31" --tail=10 edu_web_1
 docker logs -f -t --tail 行数 容器名
    --since : 此参数指定了输出日志开始日期，即只输出指定日期之后的日志。
    -f : 查看实时日志
    -t : 查看日志产生的日期
    -tail=10 : 查看最后的10条日志。
    edu_web_1 : 容器名称
11. 在服务器指定目录下 启动容器
docker run -v /home/docker_logs/:/home/logs/love/all -v /home/docker_logs/:/home/logs/love/info -v /home/docker_logs/:/home/logs/love/err/ -p 8888:8888  --name love-master -d springboot.love-master
    -v docker日志输出目录:宿主机输出日志 （有多个日志文件，则按上述格式多加几个就好了）
    -d 后台运行 springboot.love-master 镜像名称
    -p 容器端口:宿主机端口 （端口映射）
    -name 容器名称


# activemq
    下载解压
    启动 /bin目录下
    sh activemq start 
    关闭
    sh activemq stop

# rabbitmq
1. 下载安装包和依赖
    wget www.rabbitmq.com/releases/erlang/erlang-18.3-1.el7.centos.x86_64.rpm
    wget http://repo.iotti.biz/CentOS/7/x86_64/socat-1.7.3.2-5.el7.lux.x86_64.rpm
    wget www.rabbitmq.com/releases/rabbitmq-server/v3.6.5/rabbitmq-server-3.6.5-1.noarch.rpm
2. 安装 顺序为先安装erlang环境，然后安装socat,最后安装rabbitmq，因为它们之间是有依赖关系的
    rpm -ivh erlang-18.3-1.el7.centos.x86_64.rpm  # #  erl进入
    rpm -ivh socat-1.7.3.2-5.el7.lux.x86_64.rpm
    rpm -ivh rabbitmq-server-3.6.5-1.noarch.rpm 
3. 防火墙 开放 5672 和15672端口
4. 配置文件
    /usr/lib/rabbitmq/lib/rabbitmq_server-3.6.5/ebin/rabbit.app
5. 启动、停止
    rabbitmq-server start &
    stop 
    status
    (service) systemctl rabbitmq-server start (stop)
6. 插件管理
    whereis rabbitmq
    到安装目录下sbin目录下，执行命令
    rabbitmq-plugins enable rabbitmq_management
7. 报错处理
    启动了 activemq 占用了5672端口，关闭就好

8. 设置开机启动
    chkconfig rabbitmq-server on
9. 设置配置文件
    cd /etc/rabbitmq
    cp /usr/share/doc/rabbitmq-server-3.4.1/rabbitmq.config.example /etc/rabbitmq/

    mv rabbitmq.config.example rabbitmq.config
    vi /etc/rabbitmq/rabbitmq.config
10. 开启用户远程访问
    guest(guest) 用户只能在localhost下访问
    查看用户：rabbitmqctl list_users
    新增用户：rabbitmqctl add_user admin admin 
    设置角色：rabbitmqctl set_user_tags admin administrator
    权限：rabbitmqctl set_permissions -p VHostPath 【虚拟主机路径 "/"】 User 【用户名字】 ConfP  WriteP  ReadP
    rabbitmqctl  set_permissions  -p  "/"  admin  ".*" ".*" ".*"
11. 集群（比较弱）
    <!--一般管理命令-->
    ## 管理虚拟主机 
    rabbitmqctl add_vhost [vhost_name] 
    rabbitmqctl delete_vhost [vhost_name] 
    rabbitmqctl list_vhosts 

    ## 用户管理
    rabbitmqctl add_user [username] [pwd]
    rabbitmqctl delete_user [username]
    rabbitmqctl  change_password [username] [newpwd]

    ## 用户权限
    这个virtual host中所有资源的配置、写、读权限以便管理其中的资源
    rabbitmqctl set_permissions [-p <vhostpath>] <user> <conf> <write> <read>

    注意在win上，字符全部使用双引号，linux上使用单引号
    windows上  ：  rabbitmqctl set_permissions -p / king ".*" ".*" ".*"	
    linux上  ：rabbitmqctl set_permissions -p / king '.*' '.*' '.*'

    .*是正则表达式，表示全部

    ## rabbitmqctl  set_user_tags  User  Tag
    User为用户名， Tag为角色名(对应于下面的administrator，monitoring，policymaker，management，none)
    赋予其administrator角色
    rabbitmqctl set_user_tags  king  administrator

    ## 查看队列
    rabbitmqctl list_queues

    ## 查看交换器
    rabbitmqctl list_exchanges

    ## 查看绑定
    rabbitmqctl list_bindings

    <!-- RabbitMQ集群(所有的节点应该在局域网中)-->
    ## 修改 /etc/hosts 
    192.168.1.1 node1
    192.168.1.2 node2

    ## 修改机器名  /etc/hostname
    192.168.1.1的机器  node1
    192.168.1.2的机器  node2

    ## Erlang Cookie 文件，不同服务器上的文件内容应该一致，同时权限和所属用户组
    cat /var/lib/rabbitmq/.erlang.cookie

    ## node1和node2均要关闭防火墙
    firewall-cmd --permanent --add-port=15672/tcp
    firewall-cmd --permanent --add-port=5672/tcp
    firewall-cmd --permanent --add-port=25672/tcp
    firewall-cmd --permanent --add-port=4369/tcp
    firewall-cmd --reload
    ======如果怀疑有端口未打开，使用以下rab命令检查，返回yes表示成功
    firewall-cmd --query-port=4369/tcp

    ## 在node1、node2上分别运行
    [root@node2 ~]# rabbitmqctl stop_app
    [root@node2 ~]./rabbitmqctl reset
    [root@node2 ~]# rabbitmqctl join_cluster rabbit@node1
    [root@node2 ~]# rabbitmqctl start_app

    ## 检查集群状态（node1和node2均可）
    rabbitmqctl cluster_status

    ## 移除集群中的node2
    rabbitmqctl stop_app
    rabbitmqctl reset
    rabbitmqctl start_app

    ## 加入内存节点
    内存节点则是 rabbitmqctl join_cluster rabbit@node1 --ram

    <!--RabbitMQ镜像队列的配置-->
    ## rabbitmqctl set_policy [-p Vhost] Name Pattern Definition [Priority]
    可以在代码级别设置
    -p Vhost： 可选参数，针对指定vhost下的queue进行设置
    Name: policy的名称
    Pattern: queue的匹配模式(正则表达式)
    Definition：镜像定义，包括三个部分ha-mode, ha-params, ha-sync-mode
        ha-mode:指明镜像队列的模式，有效值为 all/exactly/nodes
            all：表示在集群中所有的节点上进行镜像
            exactly：表示在指定个数的节点上进行镜像，节点的个数由ha-params指定
            nodes：表示在指定的节点上进行镜像，节点名称通过ha-params指定
        ha-params：ha-mode模式需要用到的参数
        ha-sync-mode：进行队列中消息的同步方式，有效值为automatic和manual
    priority：可选参数，policy的优先级

    例如，对队列名称以“queue_”开头的所有队列进行镜像，并在集群的两个节点上完成进行，policy的设置命令为：
    rabbitmqctl set_policy ha-queue-two '^queue_' '{"ha-mode":"exactly","ha-params":2,"ha-sync-mode":"automatic"}'



# rocketmq
0. 官方
    http://rocketmq.apache.org/docs/quick-start/
1. 下载 & 构建 (Download & Build from Release)
    http://mirrors.tuna.tsinghua.edu.cn/apache/rocketmq/4.4.0/rocketmq-all-4.4.0-source-release.zip
  > unzip rocketmq-all-4.4.0-source-release.zip
  > cd rocketmq-all-4.4.0/
  > mvn -Prelease-all -DskipTests clean install -U
  > cd distribution/target/apache-rocketmq
2. 启动名称服务 (Start Name Server)
  > nohup sh bin/mqnamesrv &
  > tail -f ~/logs/rocketmqlogs/namesrv.log
  The Name Server boot success...
3. 启动经纪人 (Start Broker)
  > nohup sh bin/mqbroker -n localhost:9876 &
  > tail -f ~/logs/rocketmqlogs/broker.log 
  The broker[%s, 172.30.30.233:10911] boot success...
4. 发送和接收消息 (Send & Receive Messages)
  > export NAMESRV_ADDR=localhost:9876
  > sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
  SendResult [sendStatus=SEND_OK, msgId= ...

  > sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
  ConsumeMessageThread_%d Receive New Messages: [MessageExt...
5. 关机服务器 (Shutdown Servers)
  > sh bin/mqshutdown broker
  The mqbroker(36695) is running...
  Send shutdown request to mqbroker(36695) OK

  > sh bin/mqshutdown namesrv
  The mqnamesrv(36664) is running...
  Send shutdown request to mqnamesrv(36664) OK

# kafka
1. 下载
    http://archive.apache.org/dist/kafka/2.3.0/
    ***.tgz
2. 基本的操作管理
    ## 运行
    启动zookeeper
    进入kafka的bin目录,指定配置文件启动
    ./kafka-server-start.sh ../config/server.properties
    linux 控制台不输出，后台运行启动
    nohup ./kafka-server-start.sh ../config/server.properties > /dev/null 2>&1
    ## 列出所有主题
    ./kafka-topics.sh --zookeeper 192.168.0.117:2181 --list
    ## 列出所有主题的详细信息
    ./kafka-topics.sh --zookeeper 192.168.0.117:2181 --describe
    ## 创建主题，主题名 love-topic，1副本，8分区
    ./kafka-topics.sh --zookeeper 192.168.0.117:2181 --create --topic love-topic --replication-factor 1 --partitions 8
    ## 增加分区，注意，分区无法删除（缩减，只能增加）
    ./kafka-topics.sh --zookeeper 192.168.0.117:2181 --alter --topic love-topic --partitions 16
    ## 删除主题
    ./kafka-topics.sh --zookeeper 192.168.0.117:2181 --delete --topic love-topic
    ## 创建生产者（控制台）
    ./kafka-console-producer.sh --broker-list 192.168.0.117:9092 --topic love-topic
    ## 创建消费者（控制台）
    ./kafka-console-consumer.sh --bootstrap-server 192.168.0.117:9092 --topic love-topic --from-beginning
    ## 列出消费者群组（仅linux）
    --new-consumer 命令不存在
    ## 列出消费者群组详细信息（仅linux）

3. broker 配置 --server.properties
 <a> 常规配置
    ## broker.id
    在单机时无需修改，但在集群下部署时往往需要修改。它是个每一个broker在集群中的唯一表示，要求是正数。当该服务器IP地址发生改变时，broker.id没有变化，则不会影响consumers消息情况
    <重要-不在同一台机器不配置可能找不到，也可能是集群配置改了hosts和hostname的影响>## listeners
    监听列表（以逗号分隔，不同的歇息（如 plaintext,trace,ssl、不同的IP和端口））,hostname如果设置为0.0.0.0则绑定所有的网卡地址；如果hostname为空则绑定默认的网卡。如果没有配置则默认为java.net.InetAddress.getCanonicaHostName()。
    如：PLAINTEXT://myhost:9092,TRACE://9091 或 PLAINTEXT://0.0.0.0:9092
    ## zookeeper.connect
    zookeeper集群的地址，可以是多个，多个之间用逗号分割。（一组hostname:port/path列表，hostname是zk的机器名或IP、port是zk的端口、/path是可选的zk的路径，如果不指定，默认使用根路径）
    ## log.dirs
    kafka把所有的消息都保存在磁盘上，存放这些数据的目录通过log.dirs指定。可以使用多路径，使用逗号分隔。如果是多路径，kfka会根据“最少使用”原则，把同一个分区的日志片段保存到同意路径下。会往拥有最少的数据分区的路径新增分区。
    ## num.recovery.threads.per.data.dir
    每个数据目录用于日志恢复启动和关闭时的线程数量，因为这些线程只是服务器启动（正常启动和崩溃后重启）和关闭时会用到，所以完全可以设置大量的线程来达到并行操作的目的。注意，这个参数指的是每个目录的线程数，若设置为8，log.dirs设置了三个路径，则总共会启动 24 个线程
    ## auto.create.topics.enable
    是否允许自动创建主题，如果设为true，那么produce(生产者往主题里写消息),consume(消费者从主题读消息)或者fetch metadata(任意客户端向主题发送元数据请求时) 一个不存在的主题时，就会自动创建，缺省为true

 <b> 主题配置 --新增主题的默认参数 
    ## num.partitions
    每个新建主题的分区个数(分区个数只能增加，不能减少)。这个参数一般要评估，比如，每秒钟要写入和读取1000M数据，如果每个消费者每秒钟可以处理50MB数据，那么需要20个分区，这样就可以让20个消费者同时读取这些分区，从而达到设计目标，（一般经验，把分区大小限制在25G之内比较理想）
    ## log.retention.hours
    日志保存时间，默认为7天（168小时）。超过这个时间会清理数据。bytes和minutes无论那个先达到都会触发。与此类似还有log.retention.minutes和log.retention.ms，都设置的话，有限使用具有最小值的那个。
    ##　log.retention.bytes
    topic的每个分区的最大文件大小，一般设置为-1没有大小限制。log.retention.bytes和log.retention.minutes任意一个达到要求，都会执行删除。(注意：如果是log.retention.bytes县达到了，则是删除多出来的部分数据)，一般不推荐使用最大文件删除策略，而是推荐使用文件过期删除策略
    ## log.segment.bytes
    分区的日志存放在某个目录下诸多文件中，这些文件将分区的日志切分成一段一段的，我们称为日志片段。这个属性就是每个文件的最大尺寸；当尺寸达到这个数值时，就会关闭当前文件，并创建新文件。被关闭的文件就开始等待过期。默认为1G
    ## log.segment.ms
    作用和log.segment.bytes类似，判断依据为时间。默认不开启
    ## message.max.bytes
    表示一个服务器能够接收处理的消息的最大字节数，注意这个值producer和consumer必须设置一致，且不要大于fetch.message.max.bytes属性的值(消费者能读取的最大消息，这个值应该大于或等于message.max.bytes).
    该值默认是1000000字节，大概900KB~1MB。如果启动压缩，判断压缩后的值。这个值的大小对性能影响很大，值越大，网络和IP的时间越长，还会增加磁盘写入的大小。
    kafka设计的初衷是迅速处理短小的消息，一般10k大小的消息吞吐性能最好

4. 集群
<a> 估算kafka集群中的broker数量
    ## 需要多少磁盘空间保留数据，和每个broker上有多少空间可以用
    若一个集群有10TB的数据需要保留，而每个broker可以存储2TB，那么至少需要5个broker，如果启用了数据复制，则还需要一倍的空间，那么这个集群需要10个broker
    ## 集群处理请求的能力
    磁盘吞吐量和内存不足造成的性能问题，可以通过扩展broker来解决
<b> broker如何加入kafka集群
    1.配置zookeeper.connect
    2.为新增的broker 设置一个集群内的唯一性id
    kafka中的集群可以动态扩容

5. zkCli.sh 客户端操作
    #连接到zookeeper服务
    ./zkCli.sh -server 192.168.0.117:2181 
    显示根目录下、文件： ls / 使用 ls 命令来查看当前 ZooKeeper 中所包含的内容 
    显示根目录下、文件： ls2 / 查看当前节点数据并能看到更新次数等数据 
    创建文件，并设置初始内容： create /zk "test" 创建一个新的 znode节点“ zk ”以及与它关联的字符串  [-e] [-s] 【-e 零时节点】 【-s 顺序节点】
    获取文件内容： get /zk 确认 znode 是否包含我们所创建的字符串  [watch]【watch 监听】
    修改文件内容： set /zk "zkbak" 对 zk 所关联的字符串进行设置 •
    删除文件： delete /zk 将刚才创建的 znode 删除，如果存在子节点删除失败 
    递归删除：rmr  /zk将刚才创建的 znode 删除，子节点同时删除
    退出客户端： quit •
    帮助命令： help



# python 
1. 安装依赖包
    yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make
2. 安装一个工具，不安装会导致pip安装失败
    yum install libffi-devel -y
3. 下载&&解压
    1.官方下载：
    打开python的官方网站：https://www.python.org/  -->Downloads-->Source code-->Latest Python 3 Release - Python 3.7.0-->拉到最下面，选择Gzipped source tarball，下载到本地，然后上传到服务器即可
    2.wget命令：
    wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
    
    tar  -zvxf ...
4. 安装
    cd Python-version
    ./configure
    make&&make install
5. 环境变量
    Centos 7 自带了 python2.7
    因为 yum 命令需要python2.7环境
    python 进入 python2
    python3 进入 python3

    pip 无法找到命令
    pip3 命令  pip3 -V查看版本为python3.7的

# 服务器设置
1. TCP 协议的 time_wait 超时时间，默认 240s
    vi /etc/sysctl.conf
    net.ipv4.tcp_fin_timeout=30
2. 服务器所支持的最大文件句柄数（File Descriptor，fd）
    <1> max-file
    系统级别
        查看 系统级别 的能够打开的文件句柄的数量，Centos7默认是794168：
            cat /proc/sys/fs/file-max
        系统级 打开最大文件句柄的数量永久生效的修改方法，修改文件，文件末尾加入配置内容:
            vi /etc/sysctl.conf
            fs.file-max=2000000
            立即生效：sysctl -p
    <2> ulimit
    用户进程级别
        查看 用户进程级 的能够打开文件句柄的数量，Centos7默认是1024
        这里设置的是当前shell的当前用户的打开的最大限制，如果当前用户打开多个shell，则每个shell都能打开该最大值
        进程级 打开文件句柄数量永久生效的修改方法，修改文件，文件末尾加入配置内容：
        这里限制一个用户的所有shell能打开的最大数:
            vim /etc/security/limits.conf
            * soft nofile 65535
            * hard nofile 65535
        修改以后，需要重新登录才能生效。
        如果需要设置当前用户session立即生效，还需要执行：
            ulimit -n 65535 
3. 获取操作系统上的页大小
    $ getconf PAGE_SIZE

# 在线流程图-思维导图
    https://www.processon.com/diagrams




    