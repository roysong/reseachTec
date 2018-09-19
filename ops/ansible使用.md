# ansible安装及使用
[TOC]
## 背景
广州及湖南地区项目一共8台服务器，分为安调系统(4台)和集中监控系统(4台)，在程序大体功能相同，但又上分为miniJar部署和独立Jar部署，每次在一个地区发现问题并修改程序后，其他地区的项目也会重新部署，但目前采用人工部署的方式，偶有错误产生，且同时部署多台服务器操作繁琐。

### ansoble
#### 控制机器
* linux系统（Red Hat，Debian，CentOS，Unbuntu）
* 安装了python2.6+ / python3.5+

#### 受管节点
* ssh
* sftp

## 安装
只需在控制机器上安装
``` shell
# Red Hat/CentOS
$ sudo yum install ansible

# Debian/Unbuntu
# 添加下面的源到/etc/apt/sources.list:
deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main
# 执行以下命令
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
$ sudo apt-get update
$ sudo apt-get install ansible
```

## 配置
### ssh配置
* 控制机器：
    - 配置受管节点：
    ``` shell
    $ vi /etc/ansible/hosts #受管节点配置文件
    [ad]
    192.168.0.88
    [jzjk]
    192.168.0.99:22 # 非标准ssh端口需要制定端口号
    192.168.0.9[0:8] # 批量设置 192.168.0.90~192.168.0.98
    192.168.0.89 ansible_connection = ssh ansible_user = enovell # 设置链接类型和用户 相同方式可以设置主机变量在playbook中使用
    [jzjk:vars] # 组用户变量
    serverport = 8000
    ```

    - 密匙配置
    ``` shell
    $ ssh-agent $SHELL # 启用ssh代理
    $ ssh-keygen # 生成密匙
    $ ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.0.88 #复制密匙到目标机器
    $ ssh-add ~/.ssh/id_rsa # 添加密匙到ssh代理中
    ```

## 编写脚本
ansible通过调用内置的不同模块，配置模块的不同参数在指定的机器或工作组中实现不同的功能，调用方式分为通过临时命令实现不需要重复利用的简单的功能和通过编写playbook脚本实现复杂的，可重复利用的功能

### ad-hoc 临时命令
``` shell
$ ansible all -m ping -u root #链接测试 ansible 所有机器（/etc/ansible/hosts 中配置的所有机器| 组名） -m (使用ansible模块名称) ping -u(切换用户) root
```

### ansibel-playbook 脚本执行
playbook以yaml格式进行编写，从上到下顺序执行
#### 编写playbook
``` yml
--- # playbook文件必须以---开头
- hosts: ad #服务器组名
  remote_user: root # 以root用户执行,可定义在每个task中，每个命令可使用不同的用户执行
  tasks:  # 执行脚本
    - name: copy config to hosts #单条脚本名称
      template： #使用的模块
        src:  # 模块参数，复制文件在控制机器的源路径
        desc:  #复制到受管节点的路径
      notify：  # 命令执行完成后触发的处理器名
      - look file
    - name: copy jar to hosts
      template:
        src:
        desc:
      tags: copyjar #标签 可在启动命令中加入 -t 标签名直接运行单一标签的命令
  handlers: # 处理器定义
    - name: look file
      command: ll /opt/extend
      register: result #用result保存结果


- hosts: jzjk
  vars: # 定义变量
    http_port: 80
  tasks:
    - name: echo http port
      shell: echo {{http_port}}  # 使用变量
```

#### 执行palybook
``` shell
$ ansibel-playbook playbook.yml
```

### 常用模块及参数

ad-hoc 中 -m 模块名 -a 参数

* shell|command -a "shell 命令"
* copy|template -a "src=本地路径 desc=远程路径"
* file
    - ansible webservers -m file -a "dest=/path/to/c mode=755 owner=用户 group=组 state=directory" 创建文件夹，类似mkdir -p
    - ansible webservers -m file -a "dest=/srv/foo/b.txt mode=600 owner=用户 group=组" 修改权限和用户、组
    - ansible webservers -m file -a "dest=/path/to/c state=absent" 删除目录或文件
* user
    - ansible all -m user -a "name=用户名 password=密码" 创建用户
    - ansible all -m user -a "name=用户名 state=absent" 删除用户
* service
    - ansible webservers -m service -a "name=服务名(httpd) state=started" 启动服务
    - ansible webservers -m service -a "name=服务名(httpd) state=restarted" 重启服务
    - ansible webservers -m service -a "name=服务名(httpd) state=stopped" 关闭服务


