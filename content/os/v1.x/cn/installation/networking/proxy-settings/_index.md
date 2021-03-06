---
title: Configuring Proxy Settings
weight: 172
---

HTTP proxy settings can be set directly under the `network` key. This will automatically configure proxy settings for both Docker and System Docker.

```
#cloud-config
rancher:
  network:
    http_proxy: https://myproxy.example.com
    https_proxy: https://myproxy.example.com
    no_proxy: localhost,127.0.0.1
```

<br>

> **Note:** System Docker proxy settings will not be applied until after a reboot.

To add the `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables to a system service, specify each under the `environment` key for the service.

```
#cloud-config
rancher:
  services:
    myservice:
      ...
      environment:
      - HTTP_PROXY
      - HTTPS_PROXY
      - NO_PROXY
```
