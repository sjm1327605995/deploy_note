## docker数据迁移

数据卷默认安装目录应该是：/var/lib/docker

 ``` bash
//查看 信息
docker info
```
 数据卷目录如下
 
![image](https://github.com/user-attachments/assets/8a409e9b-b487-4690-b121-7506f6aa98ee)

## 软连接

前置工作
停止容器服务
```bash
  sudo systemctl stop docker
```

下文以 /store/software/docker 这个路径作为要迁移的新 Docker 安装(存储)目录

在开始迁移之前，首先复制原 Docker 安装(存储)目录到新的路径下：
 ``` bash
  sudo cp -a /var/lib/docker /store/software/
```
如果cp卡住不懂，替换成rsync 进行文件同步复制
```bash
sudo rsync -r /var/lib/docker /store/software/
```

如果出现软连接后，重启Docker找不到容器了。可能是权限导致
```
 sudo systemctl stop docker
sudo chown -R root:root /data/docker/lib
sudo chmod -R 755 /data/docker/lib
 sudo systemctl restart docker
```
