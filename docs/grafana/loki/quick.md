<!--
 * @Author: kbsonlong kbsonlong@gmail.com
 * @Date: 2023-06-10 21:37:53
 * @LastEditors: kbsonlong kbsonlong@gmail.com
 * @LastEditTime: 2023-06-10 21:54:39
 * @Description: 轻量日志采集Loki快速入门
 * Copyright (c) 2023 by kbsonlong, All Rights Reserved. 
-->

# 轻量日志采集Loki快速入门

## 安装 `Loki`

### 添加 `Helm` 仓库
```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

### loki-values.yaml

```yaml
global:
  # -- configures cluster domain ("cluster.local" by default)
  clusterDomain: "alongparty.cn"
  dnsService: "kube-dns-coredns"

loki:
  tenants:
    - name: mongo
      password: mongo
test:
  enabled: false

monitoring:
  lokiCanary:
    enabled: false

write:
  autoscaling:
    enabled: true
  persistence:
    storageClass: ebs-sc
    enableStatefulSetAutoDeletePVC: false

read:
  autoscaling:
    enabled: true
  persistence:
    storageClass: ebs-sc
    enableStatefulSetAutoDeletePVC: false

backend:
  autoscaling:
    enabled: true
  persistence:
    storageClass: ebs-sc
    enableStatefulSetAutoDeletePVC: false

gateway:
  base_auth:
  enabled: true
  # username: mongo
  # password: mongo

minio:
  enabled: true
  clusterDomain: alongparty.cn
  image:
    repository: registry.cn-hangzhou.aliyuncs.com/seam/minio
  mcImage:
    repository: registry.cn-hangzhou.aliyuncs.com/seam/minio-mc
  persistence:
    size: 50Gi
    stroageClass: ebs-sc
```

### 部署 loki

```bash
helm install loki grafana/loki -n loki --create-namespace -f loki-values.yaml
```
### 更新
```bash
helm upgrade --install loki grafana/loki -n loki --create-namespace -f loki-values.yaml
```


## 安装 `Promtail` 日志收集器

### promtail-values.yaml

```yaml
config:
  clients:
  - basic_auth:
      password: mongo
      username: mongo
    url: http://loki-gateway/loki/api/v1/push
  snippets:
    extraScrapeConfigs: |
      # 通过 kubernetes_sd_configs:pod 配置 pod 日志，参考 https://grafana.com/docs/loki/latest/clients/promtail/configuration/#kubernetes_sd_config
      - job_name: kubernetes-pods-app
        pipeline_stages:
          {{- toYaml .Values.config.snippets.podPipelineStages | nindent 4 }}
        kubernetes_sd_configs:
          - role: pod
        relabel_configs:
          # prometheus.io/mongo.scrape: "true"
          - action: keep
            regex: percona-server-mongodb
            source_labels: [__meta_kubernetes_pod_label_app_kubernetes_io_name]
          # 把 pod 所有的标签暴露出来
          - action: labelmap
            regex: __meta_kubernetes_pod_label_(.+)
            replacement: $1
            target_label: $1
          - action: replace
            source_labels:
              - __meta_kubernetes_pod_ip
            target_label: pod_ip
          - action: replace
            source_labels:
              - __meta_kubernetes_pod_label_app
            target_label: app
          - action: replace
            source_labels:
              - __meta_kubernetes_pod_label_app_kubernetes_io_component
            target_label: component
          - action: replace
            source_labels:
              - __meta_kubernetes_pod_label_app_kubernetes_io_replset
            target_label: replset
          - action: replace
            source_labels:
              - __meta_kubernetes_pod_label_app_kubernetes_io_instance
            target_label: app
          - action: replace
            source_labels:
              - __meta_kubernetes_pod_label_topology_kubernetes_io_zone
            target_label: zone
          - action: replace
            source_labels:
              - __meta_kubernetes_pod_node_name
            target_label: node_name
          - action: labeldrop
            regex: (app|version|topology|statefulset|controller)_(.+)
          {{- if .Values.config.snippets.addScrapeJobLabel }}
          - action: replace
            replacement: kubernetes-pods-app
            target_label: scrape_job
          {{- end }}
          {{- toYaml .Values.config.snippets.common | nindent 4 }}
    podPipelineStages:
    - match:
        selector: '{container=~"mongos|mongod"} |= "Slow query"'
        action: keep
        stages:
          - docker: {}
    - match:
        selector: '{container=~"mongos|mongod"} != "Slow query"'
        action: drop
        drop_counter_reason: promtail_not_slow_query
    - match:
        selector: '{container!~"mongos|mongod"}'
        action: drop
        drop_counter_reason: promtail_not_other_pod
    - drop:
        older_than: 24h
        drop_counter_reason: "line_too_old"
    scrapeConfigs: ""
defaultVolumeMounts:
- mountPath: /run/promtail
  name: run
- mountPath: /data/k8s/k8s_docker/data/containers
  name: containers
  readOnly: true
- mountPath: /var/log/pods
  name: pods
  readOnly: true
defaultVolumes:
- hostPath:
    path: /run/promtail
  name: run
- hostPath:
    path: /data/k8s/k8s_docker/data/containers
  name: containers
- hostPath:
    path: /var/log/pods
  name: pods
extraArgs:
- -client.external-labels=cluster=dev
extraVolumeMounts:
- mountPath: /etc/localtime
  name: host-time
extraVolumes:
- hostPath:
    path: /etc/localtime
  name: host-time
readinessProbe:
  httpGet:
    path: '{{ printf `%s/metrics` .Values.httpPathPrefix }}'
    port: http-metrics
resources:
  limits:
    cpu: 512m
    memory: 512Mi

```

### 部署 promtail

```bash
helm install promtail grafana/promtail -n loki -f promtail-values.yaml
```

## 安装 `Grafana`

### grafana-values.yaml
```yaml
service:
  type: NodePort
```

### 部署 grafana
```bash
helm install grafana grafana/grafana -n loki -f grafana-values.yaml
```


- Grafana 添加 `loki` 数据源

![](https://raw.githubusercontent.com/kbsonlong/notes_statics/main/images20230610215237.png)

- 查看日志

![](https://raw.githubusercontent.com/kbsonlong/notes_statics/main/images20230610215415.png)