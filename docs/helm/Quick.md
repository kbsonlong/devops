# 使用 [GitHub] 作为 Helm 仓库

> 使用 GitHub 作为 Helm 的仓库；在创建前需要安装 Helm，以 Helm3 为例

## 准备工作

-   创建仓库

在 GitHub 上创建名为 `helm-chart`的仓库

-   本地创建 `helm-chart`文件夹

## 创建并配置仓库

-   进入文件夹，并执行以下命令创建 Helm 包

```bash
helm create hellohelm
cd hellohelm
```

此时，已经在 `helm-chart-resources`目录下创建出了 `helloworld`这个包的配置文件

```bash
.
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml

3 directories, 10 files
```

修改为自己的相应的配置

-   检查配置

```bash
# helm lint  . 
==> Linting .
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, 0 chart(s) failed
```

## 打包发布应用

-   打包应用

```bash
# helm package .
Successfully packaged chart and saved it to: /Users/zengshenglong/Code/HelmWorkSpace/hellohelm/hellohelm-0.1.0.tgz
```

-   添加描述文件 index.yaml

```bash
# helm repo index --url  https://kbsonlong.github.io/helm-chart/ .

# ls -l
total 32
-rw-r--r--@  1 zengshenglong  staff  1145  6 12 12:02 Chart.yaml
drwxr-xr-x@  2 zengshenglong  staff    64  6 12 12:02 charts
-rw-r--r--@  1 zengshenglong  staff  3760  6 12 13:17 hellohelm-0.1.0.tgz
-rw-r--r--@  1 zengshenglong  staff   431  6 12 13:17 index.yaml
drwxr-xr-x@ 10 zengshenglong  staff   320  6 12 12:02 templates
-rw-r--r--@  1 zengshenglong  staff  1876  6 12 12:02 values.yaml
```

对应的 url 即为 repo 的 url

```yaml
# cat index.yaml 
apiVersion: v1
entries:
  hellohelm:
  - apiVersion: v2
    appVersion: 1.16.0
    created: "2023-06-12T13:17:51.188765+08:00"
    description: A Helm chart for Kubernetes
    digest: a09fee670a0bda778f28f0c264f337934739339f366b2195ae495f04ac6f1a14
    name: hellohelm
    type: application
    urls:
    - https://kbsonlong.github.io/helm-chart/hellohelm-0.1.0.tgz
    version: 0.1.0
generated: "2023-06-12T13:17:51.187974+08:00"
```

-   提交并推送到仓库中

-   配置仓库开启 GitHub Pages

![helm-chart-github-page.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9oZWxsb3dvb2Rlcy5vc3MtY24tYmVpamluZy5hbGl5dW5jcy5jb20vcGljdHVyZS9oZWxtLWNoYXJ0LWdpdGh1Yi1wYWdlLnBuZw?x-oss-process=image/format,png)

## 客户端添加安装

-   添加仓库到 Helm 客户端

```bash
helm repo add myrepo https://helloworlde.github.io/helm-chart
```

-   查找应用

```bash
helm search repo seata
```

```auto
NAME              	CHART VERSION	APP VERSION	DESCRIPTION
myrepo/helloworlde	0.1.0        	1.0        	A Helm chart for Kubernetes

```

-   安装应用

```auto
helm install helloworld helloworld

```

## 升级 Helm 版本

修改版本号为 `0.1.1`

```auto
vi helm-chart-sources/helloworld/Chart.yaml
```

-   打包

```bash
helm package helm-chart-sources/*
```

-   修改描述文件 index.yaml

```bash
helm repo index --url https://helloworlde.github.io/helm-chart/ --merge index.yaml .
```

此时 index.yaml 内容变为

```yaml
apiVersion: v1
entries:
  helloworld:
  - apiVersion: v2
    appVersion: 1.16.0
    created: "2019-12-07T18:08:17.053158+08:00"
    description: A Helm chart for Kubernetes
    digest: aca5feeb8137addab872a98e5da5e4e4aa57d5523faeeedf1cd5c672b26c1274
    name: helloworld
    type: application
    urls:
    - https://helloworlde.github.io/helm-chart/helloworld-0.1.1.tgz
    version: 0.1.1
  - apiVersion: v2
    appVersion: 1.16.0
    created: "2019-12-07T18:08:17.052134+08:00"
    description: A Helm chart for Kubernetes
    digest: 5909523dffde5b12f3c589bcea2d31a5785aa437dc8ea6ed147fcbf57b13a671
    name: helloworld
    type: application
    urls:
    - https://helloworlde.github.io/helm-chart/helloworld-0.1.0.tgz
    version: 0.1.0
generated: "2019-12-07T18:08:17.050373+08:00"

```

再次提交，即完成 Helm 包的升级

* * *

### 参考文章

-   [Using a private github repo as helm chart repo (https access)](https://medium.com/hackernoon/using-a-private-github-repo-as-helm-chart-repo-https-access-95629b2af27c?)