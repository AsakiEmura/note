# centos tips

---

## 查看版本

```bash
cat /etc/os-release
```

---

## redis

### 安装redis

```bash
sudo yum install -y epel-release
sudo yum -y update
sudo yum -y install redis
```

---

## mysql

### 安装mysql

```bash
写在
```

### 修改mysql密码(8.0版本)

```

```



---

## 更新源

```bash
# 做备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 进入yum源配置文件夹
cd /etc/yum.repos.d/
# 更新centos版本下载对应的新源  (以centos7为例)
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# 如果 wget 命令不生效，说明还没有安装 wget 工具，输入 yum -y install wget 回车进行安装，或者使用以下命令
curl -O http://mirrors.aliyun.com/repo/Centos-7.repo
# 执行 yum 源更新命令
yum clean all
yum makecache
yum -y update
```

