## 使用镜像

1. 获取镜像

   ```shell
   docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
   ```

   - Docker 镜像仓库地址：地址的格式一般是 **<域名/IP>[:端口号]**。默认地址是 Docker Hub (**docker.io**)。
   - 仓库名：如之前所说，这里的仓库名是两段式名称，即 `<用户名>/<软件名>`。对于 Docker Hub，如果不给出用户名，则默认为 `library`，也就是官方镜像。

   

