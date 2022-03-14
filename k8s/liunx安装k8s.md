[k8s安装文档](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
### 在Linux上使用curl安装k8s二进制文件
1. 使用以下命令下载最新版本

> curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

2. 验证二进制文件（可选）

> curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

根据校验和文件验证 kubectl 二进制文件：

> echo "$(<kubectl.sha256)  kubectl" | sha256sum --check

如果有效，输出如下:

> kubectl: OK

如果检查失败，sha256则以非零状态退出并打印类似于以下内容的输出：

> kubectl: FAILED sha256sum: WARNING: 1 computed checksum did NOT match

3. 安装k8s

> sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

> 笔记：
如果您在目标系统上没有 root 访问权限，您仍然可以将 kubectl 安装到该~/.local/bin目录：
chmod +x kubectl
mkdir -p ~/.local/bin/kubectl
mv ./kubectl ~/.local/bin/kubectl 
~~and then append (or prepend) ~/.local/bin to $PATH~~

4. 查看k8s是否安装成功，查看版本

> kubectl version --client

或者使用此选项查看版本的详细信息：

> kubectl version --client --output=yaml 
