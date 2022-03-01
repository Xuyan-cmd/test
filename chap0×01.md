# 第一章：Linux基础-实验

------

## 软件环境

------

- **VirtualBox 6.1**
- **Ubuntu 20.04.02 Server 64bit**

## 实验问题

------

- #### 调查并记录实验环境的如下信息：

  - ###### 当前 Linux 发行版基本信息

  - ###### 当前 Linux 内核版本信息

- #### Virtualbox 安装完 Ubuntu 之后新添加的网卡如何实现系统开机自动启用和自动获取 IP？

- #### 如何使用 `scp` 在「虚拟机和宿主机之间」、「本机和远程 Linux 系统之间」传输文件？

- #### 如何配置 SSH 免密登录？

------

## 一、调查并记录实验环境

------

**查询当前 Linux 发行版基本信息**

- **操作过程**
  - 方法一：通过LSB（Linux标准库）能够打印发行版的具体信息，包括发行版名称、版本号、代号等

操作代码：

```shell
lsb_release -a
```

![Query_release_version](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\Query_release_version.png)

​				可以得到当前Ubuntu的发行版本代号为：20.04

- 方法二：release 文件通常被视为操作系统的标识。在 `/etc` 目录下放置了很多记录着发行版各种信息的文件，每个发行版都各自有一套这样记录着相关信息的文件。

  - 操作代码：

    ```shell
    cat /etc/issue
    cat /etc/issue.net
    cat /etc/lsb-release
    cat /etc/os-release
    ```

    - 两种方法均可查询到当前Linux的发行版本相关信息，包括发行版名称、版本号、代号等。

    

- **查询当前 Linux 内核版本信息**

​	**操作过程**		

- uname（unix name 的意思） 是一个打印系统信息的工具，包括内核名称、版本号、系统详细信息以及所运行的操作系统等等。

操作代码：

```shell
uname -a
```

![Query_kernel_version_information](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\Query_kernel_version_information.png)

可知当前所用Ubuntu20.04的内核版本为：5.4.0-65-generic

------

## 二、Virtualbox 安装完 Ubuntu 之后新添加的网卡如何实现系统开机自动启用和自动获取 IP？

------

### 具体过程

**网络配置**

**在 `NAT` 的基础上再开启另一块网卡 `Host-Only` ，使用 `netplan` 配置。**

- 第一步，查询当前网卡使用情况

  - ```shell
    ip a
    ```

    ![network_card_information](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\network_card_information.png)

- ​	第二步，修改配置文件并应用

  - ```shell
    sudo vim /etc/netplan/01-netcfg.yaml
    sudo netplan apply
    ```

    ![NIC_configuration_information](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\NIC_configuration_information.png)

- 再次查询网卡状态，并获取ip

  ![new_network_card_information](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\new_network_card_information.png)

------

## 三、如何使用 `scp` 在「虚拟机和宿主机之间」、「本机和远程 Linux 系统之间」传输文件？

------

### 具体过程

- ##### 



------

## 四、如何配置 SSH 免密登录？

------

### 具体过程

- **gitbash上进行的操作**

  - ```shell
    ssh-keygen -t rsa
    # 提示输入东西的时候，连续按3次enter 就好，在~/.ssh目录 下生成了id_rsa 和id_rsa.pub两个文件，后者上传至目标服务器
    ssh-copy-id  server_user@ipAddr
    # server_user是服务器用户名，ipAddr是IP地址
    ```

    ![Gitbash_implements_password-free_login](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\Gitbash_implements_password-free_login.png)

- **在虚拟机上进行的操作**

  - ```shell
    # 找到 /etc/ssh/sshd_config 这个文件
    vim /etc/ssh/sshd_config
    # 并查询以下信息的状态：
    #	RSAAuthentication yes
    #	PubkeyAuthentication yes
    #	AuthorizedKeysFile .ssh/authorized_keys
    # 第2个可能找不到，没关系
    sudo service ssh restart
    ```

    ![sshd_config_query](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\sshd_config_query.png)

- **在linux中输入`cat ~/.ssh/authorized_keys`，即可查看公钥内容**

  - ```shell
    cat ~/.ssh/authorized_keys
    ```

    ![public_key_content](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\public_key_content.png)

- **接下来，在cmd中输入`ssh username@remote`，即可免密登录虚拟机的Linux系统**

  - ```shell
    ssh username@remote
    # username为用户名，remote为虚拟机的IP地址
    ```

    ![Password-free_login_succeeded](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\Password-free_login_succeeded.png)

- **在cmd中通过修改配置来实现别名登录**

  - 1、在cmd中输入cd .ssh，进入.ssh文件夹，此时cmd中显示路径为C:\Users\你的机器名文件夹\.ssh
    2、输入cd .>config，在.ssh中创建host配置文件config
    3、在config中输入配置信息

    Host 自定义名称（建议用服务器名）

    HostName 服务器ip地址

    User 服务器名

    Port 22

    ```shell
    C:\Users\你的设备名称\.ssh>echo Host cuc >> config
    C:\Users\你的设备名称\.ssh>echo HostName 192.168.56.103 >> config
    C:\Users\你的设备名称\.ssh>echo User cuc >> config
    C:\Users\你的设备名称\.ssh>echo Port 22 >> config
    
    C:\Users\你的设备名称\.ssh>type config
    #	通过 type config可查看你修改后的配置信息
    ```

- 接下来，就可以直接`ssh 你的名称`就能实现最快捷的免密登录啦！

​	![name_login](C:\Users\86150\Desktop\2022-linux-public-Xuyan-cmd\chap0×01\img\name_login.png)

------

## 过程中遇到的问题

------

- 
- 

------

## 参考资料

------

- [查看 Linux 发行版名称和版本号的 8 种方法]: https://linux.cn/article-9586-1.html

- [Ubuntu查看系统版本信息的方法汇总]: https://blog.csdn.net/timonium/article/details/118694850

- [内核版本与发行版本（CentOS &amp; Ubuntu）的对应关系]: https://blog.csdn.net/zhaihaibo168/article/details/102673669

- [ubuntu20添加新网卡后设置自动启用并获取ip]: https://blog.csdn.net/xiongyangg/article/details/110206220

- [Linux 系统与网络管理(2021)]: https://www.bilibili.com/video/BV1Hb4y1R7FE?p=23

- [Windos + GitBash实现免密远程登录Ubuntu]: https://blog.csdn.net/qq_42123832/article/details/113712378

- [如何在Windows中使用SSH连接Linux服务器，以及设置免密登录和配置别名！]: https://blog.csdn.net/weixin_42633385/article/details/88785250

  

- [The Linux Kernel Archives]: https://www.kernel.org/

  