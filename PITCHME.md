## Docker 学习分享

#### ---- 如果生成一个更小的 Docker 镜像
### By FSSlc

---

## Why?

- 节省带宽，减少下载时间
- 减少构建时间
- 减少磁盘占用
- 节省等待时间，加快启动，加快部署速度
- 加强安全性，因为包含更少文件

--- 

## How?

可以从哪几方面考虑生成更小的 Docker 镜像？

### 制作阶段
在编写 Dockerfile 时可以考虑的优化。

### 优化阶段
在构建镜像时可以考虑的优化。

---

## 制作阶段

+++

## 选择合适的基础镜像

- 使用小的基础镜像
  - FROM scratch
  - alpine
  - busybox
  - [distroless](https://github.com/GoogleContainerTools/distroless)

- 使用官方镜像
  - https://github.com/docker-library
- 自己构建基础镜像
  - https://github.com/jfloff/alpine-python

+++

### 使用多阶段构建

- [官方介绍文档](https://docs.docker.com/develop/develop-images/multistage-build/)
- [上文的一个中文翻译](https://linux.cn/article-9133-1.html)

> Docker多阶段构建是 17.05 以后引入的新特性，旨在解决编译和构建复杂的问题，减小镜像大小。

+++ 

### 减少构建层数 

- 每个 RUN COPY ADD 命令会构建一个层(1.10+)

![docker layers](./assets/img/docker-filesystems-multilayer.png)

```bash
RUN apt-get -y update
RUN apt-get install -y python
```

```bash
RUN apt-get -y update && apt-get install -y python
```

+++ 

### 避免不必要的文件

- .dockerignore
- 只添加必要的文件夹
- 删除不必要的文件
  - 删除包缓存
  - 删除构建时的源码包
- 不安装不必要的软件，不安装 debug 工具


--- 

## 构建阶段

- docker build 使用 --squash
- `docker export <CONTAINER ID> | docker import - some-image-name:latest`

+++

## 工具

### 检测工具
- dockerfilelint
- fromlatest

### 压缩工具
- docker-squash
- Python

---

## 优化的考虑

### **适合的才是最好的！**

- 确定哪些东西可以在镜像中删除
- 确定是否有更好的基础镜像
- 考虑如何减少层
- 如何减少构建时间（合理利用 Docker 缓存）

--- 

## 参考

- https://linuxhint.com/optimizing-docker-images/
- https://rollout.io/blog/reduce-docker-image-size/
- https://rollout.io/blog/alpine-based-docker-images-make-difference-real-world-apps/
- https://resources.codeship.com/hubfs/Webinar_Resources/Reducing_Docker_Image_Sizes_Codeship_Webinar_Slides.pdf
- https://pythonspeed.com/docker/