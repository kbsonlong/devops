<!--
 * @Author: kbsonlong kbsonlong@gmail.com
 * @Date: 2023-06-07 16:39:36
 * @LastEditors: kbsonlong kbsonlong@gmail.com
 * @LastEditTime: 2023-06-07 18:31:11
 * @Description: 
 * Copyright (c) 2023 by kbsonlong, All Rights Reserved. 
-->

# Quick Start

```bash
ctr image pull  docker.io/gitlab/gitlab-ce:latest
```


```bash
# 1. 先创建名为 gitlab 的 container 对象
ctr container create docker.io/gitlab/gitlab-ce:latest gitlab
# 2. 再紧接着执行 ctr task start 才会真正启动一个容器，container 对象只是一个静态的数据结构。-d 同 docker 中的 -d
ctr task start -d gitlab

# 效果同上，相同将上面两个步骤合为一步，直接启动一个真正的容器，也会有 container 和 task 两个对象
ctr run -d docker.io/gitlab/gitlab-ce:latest gitlab


# nerdctl 启动容器，默认使用的镜像是 docker.io/library/nginx:latest，同 docker 的使用
nerdctl run -d --name gitlab nginx 


# crictl 无法直接创建一个容器，需要在 sandbox 中创建容器
crictl run container-config.[json|yaml] pod-config.[json|yaml]
```