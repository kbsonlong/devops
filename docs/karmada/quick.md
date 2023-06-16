
## 快速安装
```bash
git clone https://github.com/karmada-io/karmada
cd karmada
hack/local-up-karmada.sh
```


```text
Local Karmada is running.

To start using your Karmada environment, run:
  export KUBECONFIG="$HOME/.kube/karmada.config"
Please use 'kubectl config use-context karmada-host/karmada-apiserver' to switch the host and control plane cluster.

To manage your member clusters, run:
  export KUBECONFIG="$HOME/.kube/members.config"
Please use 'kubectl config use-context member1/member2/member3' to switch to the different member cluster.
```


```bash
  export KUBECONFIG="$HOME/.kube/karmada.config"
  kubectl config use-context karmada-apiserver
```

### 查看成员集群
```bash
➜  ~ kubectl get clusters
NAME      VERSION   MODE   READY   AGE
member1   v1.26.0   Push   True    105m
member2   v1.26.0   Push   True    104m
member3   v1.26.0   Pull   True    104m
```


### 查看部署
```bash
➜  ~ kubectl get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   0/2     2            0           84s
➜  ~ export KUBECONFIG="$HOME/.kube/members.config"
➜  ~ kubectl config use-context member1
Switched to context "member1".
➜  ~ kubectl get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   0/1     1            0           101s
➜  ~ kubectl config use-context member2
Switched to context "member2".
➜  ~ kubectl get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   0/1     1            0           106s
➜  ~ kubectl config use-context member3
Switched to context "member3".
➜  ~ kubectl get deployment
No resources found in default namespace.
```