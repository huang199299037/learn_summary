# k8s



## 安装 kind

`kind`（Kubernetes IN Docker）是一个工具，用于在 Docker 容器中运行 Kubernetes 集群，这使得在本地进行 Kubernetes 测试和开发变得非常方便。下面是 Mac 上安装 `kind` 的详细步骤说明

### 步骤 1: 安装 Docker

在安装 `kind` 之前，确保您的 Mac 上已安装 Docker。您可以从 [Docker 官网](https://www.docker.com/products/docker-desktop/) 下载 Docker Desktop 进行安装。

1. 前往 Docker 官网，下载安装文件。
2. 双击 `.dmg` 文件并将 Docker 图标拖入 Applications 文件夹。
3. 启动 Docker Desktop，并按照屏幕上的指示完成安装。

### 步骤 2: 安装 `kind`

您可以使用 Homebrew 或手动安装 `kind`。

#### 方法 1：使用 Homebrew 安装

安装 Homebrew 后，您可以通过以下命令安装 `kind`：

```bash
brew install kind  
```

#### 方法 2：手动安装

1. 打开终端。

2. 使用以下命令下载 `kind` 可执行文件：

   ```bash
   curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-darwin-amd64  
   ```

3. 将下载的文件移动到 `/usr/local/bin` 目录并赋予执行权限：

   ```bash
   chmod +x ./kind  
   sudo mv ./kind /usr/local/bin/  
   ```

### 步骤 3: 验证安装

安装完成后，您可以通过以下命令验证 `kind` 是否安装成功：

```bash
kind version  
```

如果安装成功，您应该会看到类似于以下的输出：

```bash
kind v0.17.0  
```

（版本号可能会根据您安装的版本而有所不同。）

### 步骤 4: 创建一个 Kubernetes 集群

#### 创建自定义配置文件

要创建多节点集群，您需要使用一个配置文件来指定节点的数量和类型。下面是一个示例 YAML 文件，您可以将其命名为 `kind-config.yaml`：

```yaml
kind: Cluster  
apiVersion: kind.x-k8s.io/v1beta1  
Networking:  
  disableDefaultCNI: false  
nodes:  
  - role: control-plane  
  - role: worker  
  - role: worker  
```

#### 配置说明：

- `kind: Cluster`: 定义这是一个集群配置。

- `apiVersion`: API 版本。

- `Networking`: 这里可以配置网络相关的设置，这里是默认的 CNI。

- ```
  nodes
  ```

  : 定义集群中的节点。

  - 第一个节点的角色设为 `control-plane`，表示主节点。
  - 接下来的两个节点的角色设为 `worker`，表示工作节点。

#### 创建多节点集群

使用以下命令来创建基于您配置文件的多节点集群：

```bash
kind create cluster --config kind-config.yaml  
```

#### 验证集群状态

```bash
kubectl get nodes  
```

您应该能看到类似于以下的输出，显示所有节点的状态：

```bash
NAME                STATUS   ROLES                  AGE   VERSION  
kind-control-plane  Ready    control-plane         10m   v1.26.0  
kind-worker         Ready    worker                9m    v1.26.0  
kind-worker2        Ready    worker                9m    v1.26.0  
```

#### 使用集群

现在，您可以在多节点集群上进行测试和部署应用程序。

#### 删除集群

完成测试后，可以使用以下命令删除集群：

```bash
kind delete cluster  
```

## 创建 pod

以下是一个简单的 Kubernetes Pod YAML 文件示例，该 Pod 运行一个 Nginx 容器，您可以使用它来测试 Kubernetes Pod 的基本创建和运行。

```yaml
apiVersion: v1  
kind: Pod  
metadata:  
  name: demo-nginx  
  labels:  
    app: demo  
spec:  
  containers:  
    - name: nginx-container  
      image: nginx:latest  
      ports:  
        - containerPort: 80  
      resources:  
        limits:  
          memory: "256Mi"  
          cpu: "500m"  
      env:  
        - name: EXAMPLE_ENV  
          value: "Hello, Kubernetes!"  
```

### YAML 文件说明：

- `apiVersion`: 定义 API 版本，`v1` 是 Pod 的基本版本。

- `kind`: 资源类型，这里是 `Pod`。

- ```
  metadata
  ```

  : 包含 Pod 的元数据，如名称和标签。

  - `name`: Pod 的名称，您可以命名为 `demo-nginx`。
  - `labels`: 用于识别和选择 Pod 的标签。

- ```
  spec
  ```

  : 描述 Pod 的规范。

  - ```
    containers
    ```

    : 定义 Pod 中的容器。

    - `name`: 容器的名称。
    - `image`: 容器使用的镜像，这里使用 `nginx:latest`。
    - `ports`: 指定容器使用的端口，这里是 `80`。
    - `resources`: 资源限制，可以设置内存和 CPU 限制。
    - `env`: 环境变量，这里定义了一个示例环境变量 `EXAMPLE_ENV`。

### 创建

将上述 YAML 代码保存为 `pod-demo.yaml` 文件，然后使用以下命令创建 Pod：

```bash
kubectl apply -f pod-demo.yaml  
```

### 验证 Pod 状态

创建后，您可以使用以下命令验证 Pod 的状态：

```bash
kubectl get pods  
```

![image-20241113002833915](../images/image-20241113002833915.png)

您应该能看到名为 `demo-nginx` 的 Pod 在运行中。

### 访问 Pod

要访问 Nginx 服务器，您需要在 Pod 上执行以下命令，获取 Pod 的 IP 地址：

```bash
kubectl get pod demo-nginx -o wide  
```

![image-20241113003014447](../images/image-20241113003014447.png)

然后可以通过 Port Forwarding 来访问 Nginx：

```bash
kubectl port-forward pod/demo-nginx 8080:80  
```

#### 命令解释

- **kubectl**: Kubernetes 的命令行工具，用于与 Kubernetes 集群进行交互。
- **port-forward**: 这是一个子命令，用于设置端口转发。
- **pod/demo-nginx**: 指定了要进行端口转发的目标。这里我们将本地端口转发到名为 `demo-nginx` 的 Pod。格式为 `pod/<pod-name>` 指定一个 Pod，也可以使用 `deployment/<deployment-name>` 指定一个部署。
- **8080:80**: 这是一个端口映射：
  - `8080`: 是本地计算机上的端口号。
  - `80`: 是 Pod 内部服务的端口号（在这里假设 `demo-nginx` Pod 中的 Nginx 服务运行在 80 端口）

在浏览器中访问 `http://localhost:8080`，您应该会看到 Nginx 的欢迎页面。

> [!NOTE]
>
> 可能存在端口冲突，可以使用其他端口

#### curl

```bash
kubectl exec -it demo-nginx -- curl 10.244.2.2
本地创建 index 文件复制过去
kubectl cp index.html demo-nginx:/usr/share/nginx/html/index.html
```

### 清理资源

测试完成后，您可以通过以下命令删除 Pod：

```bash
kubectl delete -f pod-demo.yaml  
```

这样，您便成功创建并运行了一个简单的 Kubernetes Pod 示例。

## 命令

### 常见命令

```bash
# 获取当前的资源，pod
$ kubectl get pod 
	-A,--all-namespaces 查看当前所有名称空间的资源
	-n  指定名称空间，默认值 default，kube-system 空间存放是当前组件资源
	--show-labels  查看当前的标签
	-l  筛选资源，key、key=value
	-o wide  详细信息包括 IP、分配的节点
	-w  监视，打印当前的资源对象的变化部分
	
# 进入 Pod 内部的容器执行命令
$ kubectl exec -it podName -c cName -- command
	-c  可以省略，默认进入唯一的容器内部
	
# 查看资源的描述
$ kubectl explain pod.spec

# 查看 pod 内部容器的 日志
$ kubectl logs podName -c cName

# 查看资源对象的详细描述
$ kubectl describe pod podName

# 删除资源对象
$ kubectl delete kindName objName
	--all 删除当前所有的资源对象
```

### 进入 node

#### 通过 `docker exec` 进入节点

1. 首先，使用以下命令列出所有的 Docker 容器，包括 `kind` 创建的节点：

   ```bash
   docker ps  
   ```

   您将看到类似于如下的输出，列出所有正在运行的容器，其中包括控制平面节点和工作节点：

   ```bash
   CONTAINER ID   IMAGE                      COMMAND                  CREATED       STATUS       PORTS                                      NAMES  
   abc123456789   kindest/node:v1.26.0      "/usr/local/bin/entr…"   10 minutes ago Up 10 minutes 0.0.0.0:6443->6443/tcp                     kind-control-plane  
   def987654321   kindest/node:v1.26.0      "/usr/local/bin/entr…"   9 minutes ago  Up 9 minutes   0.0.0.0:6444->6444/tcp                     kind-worker  
   ```

2. 选择您要进入的节点的容器 ID（例如 `abc123456789`），然后使用 `docker exec` 命令进入节点。这是进入控制平面节点的示例：

   ```bash
   docker exec -it kind-control-plane /bin/sh  
   ```

   对于工作节点，您可以使用以下命令：

   ```bash
   docker exec -it kind-worker /bin/sh  
   ```

3. 设置环境变量

   ```bash
   export KUBECONFIG=/etc/kubernetes/kubelet.conf
   
   # 写入进去不用每次都 export
   echo "export KUBECONFIG=/etc/kubernetes/kubelet.conf" >> ~/.bashrc
   source ~/.bashrc
   ```

4. 进入节点后，您将在该节点的命令行中，您现在可以运行 Linux 命令或 Kubernetes 命令（如 `kubectl`）：

   ```bash
   # 例如，查看节点上的所有 pods  
    kubectl get pods --all-namespaces 
   ```

### 进入 Pod

如果您希望进入一个正在运行的 Pod，那么您可以使用以下命令：

1. 列出所有 pods：

   ```bash
   kubectl get pods --all-namespaces  
   ```

2. 找到您想要进入的 Pod 的名称，然后使用 `kubectl exec` 命令进入：

   ```bash
   kubectl exec -it <pod-name> -- /bin/sh  
   ```

   例如，如果您的 Pod 名称是 `nginx-pod`：

   ```bash
   kubectl exec -it nginx-pod -- /bin/sh  
   ```

这样，您就可以进入运行在 `kind` 集群节点或 Pods 中的容器了。

#### 退出节点或 Pod

- 在进入节点或 Pod 中，您可以使用 `exit` 命令退出。





