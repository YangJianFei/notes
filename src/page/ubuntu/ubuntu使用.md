## 服务

### 查看服务进程
```
ps  -ef | grep nginx
```

## 创建文件
touch xxx.xx

## 创建文件夹
mkdir xxx

## 上传下载文件
scp 文件.txt root@xxxx:/目录
scp -r .\battery-check-node root@120.79.19.96:/root/docker

### 下载
curl -O 链接

### 查看磁盘
df -h


## 防火墙

### 查看防火墙状态
firewall-cmd --state

### 查看防火墙端口
netstat -ntlp

### 开启端口
firewall-cmd --zone=public --add-port=80/tcp --permanent


### 重启防火墙
systemctl restart firewalld.service
