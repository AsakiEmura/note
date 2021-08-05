# RabbitMQ部署



**本文档介绍如何在debian系linux系统上安装rabbitmq(以ubuntu为例)**

**其余linux发行版或docker部署请参考 [官方文档](https://www.rabbitmq.com/install-debian.html)**

docker部署比较简单，但仍需要配置admin用户和授予对应权限，建议先了解整个安装流程逻辑再进行部署

----



## Step 1: Install Erlang/OTP　安装erlang

RabbitMQ requires Erlang to be  installed first before it can run. 

rabbitmq的运行依赖于erlang,所以需要先安装它

1. 添加erlang的GPG公钥

   ```shell
   wget -O- https://packages.erlang-solutions.com/ubuntu/erlang_solutions.asc | sudo apt-key add -
   ```

2. 添加erlang的仓库到apt

   **Ubuntu 20.04:**

   ```shell
   echo "deb https://packages.erlang-solutions.com/ubuntu focal contrib" | sudo tee /etc/apt/sources.list.d/rabbitmq.list
   ```

   **Ubuntu 18.04:**

   ```sh
   echo "deb https://packages.erlang-solutions.com/ubuntu bionic contrib" | sudo tee /etc/apt/sources.list.d/rabbitmq.list
   ```

   其他版本一样，只需要到`packages.erlang-solutions.com`找对应的发行版名称对应你ubuntu的版本就行

   | Release         | Distribution |
   | --------------- | ------------ |
   | Ubuntu 20.04    | focal        |
   | Ubuntu 18.04    | bionic       |
   | Debian Buster   | buster       |
   | Debian Bullseye | buster       |
   | Debian Sid      | buster       |

3. 安装erlang

   ```shell
   sudo apt update
   sudo apt install erlang
   ```

4. 验证是否安装完成

   终端键入

   ```shell
   erl
   ```

   如果有回显表示安装成功。ctrl+c 然后a退出即可

   

## Step 2: Add RabbitMQ Repository to Ubuntu　添加rabbitmq的仓库至apt仓库

Import RabbitMQ:

```sh
sudo apt update && sudo apt install wget -y
sudo apt install apt-transport-https -y
wget -O- https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc | sudo apt-key add -
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -
```

Now add RabbitMQ Repository To Ubuntu 20.04|18.04:

```sh
--- Ubuntu 20.04 ---
echo "deb https://dl.bintray.com/rabbitmq-erlang/debian focal erlang-22.x" | sudo tee /etc/apt/sources.list.d/rabbitmq.list

--- Ubuntu 18.04 ---
echo "deb https://dl.bintray.com/rabbitmq-erlang/debian bionic erlang-22.x" | sudo tee /etc/apt/sources.list.d/rabbitmq.list
```

如不同版本请参照对应规则替换

## Step 3: Install RabbitMQ Server Ubuntu 20.04 | 18.04 LTS　开始安装rabbitmq

To install RabbitMQ Server Ubuntu 18.04, update apt list, then install `rabbitmq-server` package:

```
sudo apt update
sudo apt install rabbitmq-server
```

Hit the **y** key to start the installation.

```
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  socat
The following NEW packages will be installed:
  rabbitmq-server soca
0 upgraded, 2 newly installed, 0 to remove and 27 not upgraded.
Need to get 12.3 MB of archives.
After this operation, 15.3 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
```

After installation, RabbitMQ service is started and enabled to start on boot. To check the status, run:　看一看服务的运行状态

```sh
$ systemctl status  rabbitmq-server.service 
● rabbitmq-server.service - RabbitMQ broker
     Loaded: loaded (/lib/systemd/system/rabbitmq-server.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2020-05-17 22:00:58 EAT; 6s ago
   Main PID: 18023 (beam.smp)
     Status: "Initialized"
      Tasks: 85 (limit: 2344)
     Memory: 74.6M
     CGroup: /system.slice/rabbitmq-server.service
             ├─18023 /usr/lib/erlang/erts-10.7.1/bin/beam.smp -W w -A 64 -MBas ageffcbf -MHas ageffcbf -MBlmbcs 512 -MHlmbcs 512 -MMmcs 30 -P 1048576 ->
             ├─18134 /usr/lib/erlang/erts-10.7.1/bin/epmd -daemon
             ├─18277 erl_child_setup 32768
             ├─18302 inet_gethost 4
             └─18303 inet_gethost 4

May 17 22:00:57 ubuntu20 rabbitmq-server[18023]:   Doc guides: https://rabbitmq.com/documentation.html
May 17 22:00:57 ubuntu20 rabbitmq-server[18023]:   Support:    https://rabbitmq.com/contact.html
May 17 22:00:57 ubuntu20 rabbitmq-server[18023]:   Tutorials:  https://rabbitmq.com/getstarted.html
May 17 22:00:57 ubuntu20 rabbitmq-server[18023]:   Monitoring: https://rabbitmq.com/monitoring.html
May 17 22:00:57 ubuntu20 rabbitmq-server[18023]:   Logs: /var/log/rabbitmq/rabbit@ubuntu20.log
May 17 22:00:57 ubuntu20 rabbitmq-server[18023]:         /var/log/rabbitmq/rabbit@ubuntu20_upgrade.log
May 17 22:00:57 ubuntu20 rabbitmq-server[18023]:   Config file(s): (none)
May 17 22:00:58 ubuntu20 rabbitmq-server[18023]:   Starting broker...systemd unit for activation check: "rabbitmq-server.service"
May 17 22:00:58 ubuntu20 systemd[1]: Started RabbitMQ broker.
May 17 22:00:58 ubuntu20 rabbitmq-server[18023]:  completed with 0 plugins.
```

**如果碰到问题无法正常启动可以尝试使用root用户kill 25672端口对应的pid,然后重启启动**

- 可选配置开机启动　You can confirm if the service is configured to start on boot using the command:

```sh
$ systemctl is-enabled rabbitmq-server.service 
<strong>enabled</strong>
```

- 取消开机启动　If it returns disabled, enable it by running:

```sh
sudo systemctl enable rabbitmq-server
```

## Step 4: Enable the RabbitMQ Management Dashboard 开启web管理界面

You can optionally enable the RabbitMQ Management Web dashboard for easy management.　开启后台web管理插件

```
sudo rabbitmq-plugins enable rabbitmq_management
```

The Web service should be listening on TCP port `15672`　查看后台管理的插件是否正常运行在他的端口上

```
# ss -tunelp | grep 15672
tcp   LISTEN  0       128                    0.0.0.0:15672        0.0.0.0:*      users:(("beam.smp",pid=9525,fd=71)) uid:111 ino:39934 sk:9 <->
```

If you have an active UFW firewall, open both ports 5672 and 15672:　

如果你开了ubuntu自带的ufw防火（默认关闭）需要放行这两个端口（当然阿里云后台安全组你也要放行）

```
sudo ufw allow proto tcp from any to any port 5672,15672
```

Access it by opening the URL `http://[server IP|Hostname]:15672`

[![install rabbitmq ubuntu 18.04 dashboard min](https://computingforgeeks.com/wp-content/uploads/2018/10/install-rabbitmq-ubuntu-18.04-dashboard-min-1024x461.png?ezimgfmt=rs:696x313/rscb21/ng:webp/ngcb21)](https://computingforgeeks.com/wp-content/uploads/2018/10/install-rabbitmq-ubuntu-18.04-dashboard-min.png)

By default, the **guest** user exists and can connect only from `localhost`. You can login with this user locally with the password “**guest”**

默认情况下你直接输入`公网ip:15672`可以访问到这个web界面但无法登录，因为他的guest只允许localhost登录

所以我们需要添加一个管理员并开启远程访问。

To be able to login on the network, create an admin user like below:

```sh
#需要注意这里的配置就是celery配置broker_url中的配置信息，帐号和密码
rabbitmqctl add_user admin supor229
rabbitmqctl set_user_tags admin administrator
```

Login with this admin username and the password assigned

然后你就可以登录15672这个后台界面通过web的操作来继续配置了



**最后不管是不是docker部署，都需要对新建立的admin用户进行授权（这里采用web后台管理授权，命令行也行）**

登录后台管理界面15672那个

找到最右边的tab名为Admin

点击admin这个名字，然后给他一个访问根路径的权限　“Can access virtual hosts　/”

配置完成的表格应该是这样的

| Name                                          | Tags          | Can access virtual hosts | Has password |
| --------------------------------------------- | ------------- | ------------------------ | ------------ |
| [admin](http://localhost:15672/#/users/admin) | administrator | /                        | ●            |
| [guest](http://localhost:15672/#/users/guest) | administrator | /                        | ●            |



**celery部署在本地不在赘述，task-server部署过去建立虚拟环境，安装对应的依赖，使用gunicorn启动即可，详细信息阅读task-server的部署文档（在项目目录下README）**







## **以下是进阶配置可以不管，我们需要的安装到此结束**

----------------



### Step 4: Set RabbitMQ Cluster (Optional)　设置rabblitmq的集群

If you need to achieve high availability and higher throughput, consider  configuring RabbitMQ cluster on your Ubuntu 18.04 servers by following  the guide below:

[How to Configure RabbitMQ Cluster on Ubuntu 18.04 LTS](https://computingforgeeks.com/how-to-configure-rabbitmq-cluster-on-ubuntu-18-04-lts/)

### RabbitMQ User Management Commands　一些终端管理的命令行

Delete User:

```
rabbitmqctl delete_user user
```

Change User Password:

```
rabbitmqctl change_password user strongpassword
```

Create new Virtualhost:

```
rabbitmqctl add_vhost /my_vhost
```

List available Virtualhosts:

```
rabbitmqctl list_vhosts
```

Delete a virtualhost:

```
rabbitmqctl delete_vhost /myvhost
```

Grant user permissions for vhost:

```
rabbitmqctl set_permissions -p /myvhost user ".*" ".*" ".*"
```

List vhost permissions:

```
rabbitmqctl list_permissions -p /myvhost
```

To list user permissions:

```
rabbitmqctl list_user_permissions user
```

Delete user permissions:

```
rabbitmqctl clear_permissions -p /myvhost user
```