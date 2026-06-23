# docker-export-sagemath-tar
## 仓库简介
本仓库存放由标准 SageMath Docker 镜像导出的分卷压缩包，用于**离线环境无网络部署 SageMath**。
镜像已完整打包为多段分卷压缩 `sagemath.z01 ~ sagemath.z57` + 总入口 `sagemath.zip`，解压后可得到完整 `sagemath.tar` Docker 镜像文件，支持离线导入 Docker 快速运行 SageMath 数学计算环境。

适用场景：服务器无外网、内网隔离机房、离线教学服务器、离线科研计算环境。

## 文件说明
仓库内全部压缩分卷文件：
- `sagemath.z01` ~ `sagemath.z57`：分卷压缩分片，缺一不可，全部下载才能完整解压
- `sagemath.zip`：分卷压缩入口文件，解压以此文件为基准

> 注意：所有分片必须放在**同一目录**下，缺失任意分片都会解压失败。

## 环境依赖
1. Linux / Windows / MacOS 均可
2. 解压工具：7-Zip、unzip、zcat、7z 等支持多卷分卷解压软件
3. 目标机器已安装 Docker Engine（离线导入镜像必备）

## 完整使用步骤
### 步骤1：下载全部压缩文件
将仓库中以下所有文件下载至同一文件夹：
`sagemath.z01`、`sagemath.z02` …… `sagemath.z57`、`sagemath.zip`

### 步骤2：离线解压获取 sagemath.tar
#### Linux / Mac 命令行解压
进入文件存放目录，执行解压命令：
```bash
# 方式1：使用7z解压（推荐，兼容性最好）
7z x sagemath.zip

# 方式2：unzip 解压
unzip sagemath.zip
```
解压完成后目录会生成 `sagemath.tar` 镜像文件。

#### Windows 图形化解压
1. 安装 7-Zip
2. 右键 `sagemath.zip` → 7-Zip → 提取到当前文件夹
3. 自动读取同目录下所有 `.zXX` 分片，完整输出 `sagemath.tar`

### 步骤3：Docker 离线导入镜像
将解压得到的 `sagemath.tar` 上传至离线服务器，执行导入命令：
```bash
docker load -i sagemath.tar
```
导入成功会输出镜像名称与版本信息。

### 步骤4：查看导入后的镜像
```bash
docker images | grep sagemath
```

### 步骤5：运行 SageMath 容器
#### 交互式命令行启动（数学计算交互）
```bash
docker run -it --rm sagemath
```

#### 持久化挂载工作目录（推荐，保存代码/计算文件）
将本地 `/data/sage_work` 挂载到容器内 `/work`：
```bash
docker run -it --rm -v /data/sage_work:/work sagemath
```

#### 后台运行 + 端口映射（网页版 SageMath Notebook）
```bash
docker run -d -p 8080:8080 --name sagemath-server sagemath
```
访问地址：`http://服务器IP:8080`

## 常用运维命令
1. 停止后台 SageMath 服务
```bash
docker stop sagemath-server
```
2. 删除容器
```bash
docker rm sagemath-server
```
3. 删除本地镜像（释放磁盘空间）
```bash
docker rmi sagemath
```

## 常见问题
1. 解压报错：缺少分片文件
   - 解决方案：检查目录是否存在全部 `sagemath.z01 ~ z57`，所有分片必须齐全。
2. docker load 失败：文件损坏
   - 解决方案：重新下载损坏的分片文件，重新解压。
3. 磁盘空间不足
   - 解压与导入镜像时建议预留至少镜像2倍空闲磁盘空间。
4. Windows 解压提示缺少分卷
   - 不要分开存放分片，全部放在同一个文件夹再解压。

## 仓库地址
https://github.com/EchoOfCloud/docker-export-sagemath-tar