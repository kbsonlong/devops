<!--
 * @Author: kbsonlong kbsonlong@gmail.com
 * @Date: 2023-06-07 09:47:39
 * @LastEditors: kbsonlong kbsonlong@gmail.com
 * @LastEditTime: 2023-06-07 16:34:54
 * @Description: 
 * Copyright (c) 2023 by kbsonlong, All Rights Reserved. 
-->


# Quick Start with Docker

```bash
docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab:Z \
  --volume $GITLAB_HOME/logs:/var/log/gitlab:Z \
  --volume $GITLAB_HOME/data:/var/opt/gitlab:Z \
  --shm-size 256m \
  gitlab/gitlab-ce:latest
```