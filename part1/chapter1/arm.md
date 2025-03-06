# 银河麒麟

## 银河麒麟V10安装docker和docker-compose

建议使用离线方式安装。下面是离线包下载路径，根据**服务器架构**信息下载对应的安装包。本文将介绍离线安装的方式。
- docker安装包下载目录：http://mirrors.aliyun.com/docker-ce/linux/static/stable
- docker-compose安装包下载目录：https://github.com/docker/compose/releases

### 安装步骤
第一步：根据操作系统及架构下载对应的docker和docker-compose包

```
[root@localhost ~]# cat /etc/kylin-release
Kylin Linux Advanced Server release V10 (Halberd)
[root@localhost ~]# uname -p
x86_64
[root@localhost ~]# uname -r
4.19.90-89.11.v2401.ky10.x86_64
[root@localhost ~]# iptables --version
iptables v1.8.5 (legacy)
```

第二步：在根目录创建文件夹docker，将docker及docker-compose安装包上传至此目录，解压

```
cd /
mkdir docker
cd docker/
# 解压下载好的压缩包
tar -zxvf docker-26.1.4.tgz
# 移动解压出来的二进制文件到 /usr/bin 目录中
mv docker/* /usr/bin/
# 启动测试
dockerd
```

第三步：添加docker.service

```shell
vim /usr/lib/systemd/system/docker.service
# 将下面的内容复制到刚创建的docker.service文件中

[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
[Service]
Type=notify
ExecStart=/usr/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
[Install]
WantedBy=multi-user.target 
```

第四步：为docker.service赋权限并重新加载

```shell
chmod +x /usr/lib/systemd/system/docker.service
# 重新加载
systemctl daemon-reload
```

第五步：创建docker数据存储目录，并编辑daemon.json

```shell
mkdir /var/lib/docker
vim /etc/docker/daemon.json
# 将下面的内容复制到daemon.json文件中
{
  "registry-mirrors": ["https://registry.docker-cn.com"],
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
      
  },
    "data-root":"/var/lib/docker"
}
```

第六步：重启docker服务

```shell
# 启动docker
systemctl start docker
 
# 开机自启动
systemctl enable docker
 
# 验证docker 查看docker 版本：
docker -v
```

第七步：安装docker-compose

```shell
# 进入docker目录
cd /docker/
# 解压docker-compose
tar -zxvf docker-compose-1.29.2-Linux-x86_64.tar.gz
# 移动解压出来的二进制文件到 /usr/bin 目录中
mv docker-compose /usr/bin/
# 验证docker-compose
docker-compose -v
```
