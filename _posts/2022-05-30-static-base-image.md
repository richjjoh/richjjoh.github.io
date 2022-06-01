---
title: Static Base Images
date: 2022-05-31 20:00:00 +01:00
categories: [docker]
tags: [docker, devops]
---

# Static Base Images

Take the following (simplified docker file)

```dockerfile
FROM Alpine:latest
CMD apk add nginx
```

