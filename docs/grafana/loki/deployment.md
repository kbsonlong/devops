<!--
 * @Author: kbsonlong kbsonlong@gmail.com
 * @Date: 2023-06-14 22:23:07
 * @LastEditors: kbsonlong kbsonlong@gmail.com
 * @LastEditTime: 2023-06-14 22:30:36
 * @Description: Depoliment Mode
 * Copyright (c) 2023 by kbsonlong, All Rights Reserved. 
-->

# Deployment Mode

## Run Alias

```bash
/ $ /usr/bin/loki -config.file=/etc/loki/config/config.yaml -list-targets
all
  cache-generation-loader
  compactor
  distributor
  ingester
  ingester-querier
  querier
  query-frontend
  query-scheduler
  ruler
  usage-report
backend
  compactor
  index-gateway
  ingester-querier
  query-scheduler
  ruler
  usage-report
cache-generation-loader
compactor
  usage-report
distributor
  usage-report
index-gateway
  usage-report
ingester
  usage-report
ingester-querier
overrides-exporter
querier
  cache-generation-loader
  ingester-querier
  query-scheduler
  usage-report
query-frontend
  cache-generation-loader
  query-scheduler
  usage-report
query-scheduler
  usage-report
read
  cache-generation-loader
  compactor
  index-gateway
  ingester-querier
  querier
  query-frontend
  query-scheduler
  ruler
  usage-report
ruler
  ingester-querier
  usage-report
table-manager
  usage-report
usage-report
write
  distributor
  ingester
  usage-report
```

### write

```bash
/ $ ps -ef |grep loki
    1 loki     35:20 /usr/bin/loki -config.file=/etc/loki/config/config.yaml -target=write
```
这个进程运行组件包含 `distributor`,`ingester` 和 `usage-report`

### backend

```bash
/ $ ps -ef |grep loki
    1 loki     31:45 /usr/bin/loki -config.file=/etc/loki/config/config.yaml -target=backend -legacy-read-mode=false
```
这个进程运行组件包含 `compactor`,`index-gateway`,`ingester-querier`,`query-scheduler`,`ruler` 和 `usage-report`
