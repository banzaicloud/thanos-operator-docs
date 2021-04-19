---
title: TLS
weight: 500
---

To set up secure connection you need to generate your certs.

example TLS client, server secret

```yaml
apiVersion: v1
kind: Secret
type: kubernetes.io/tls
metadata:
  name: example-com-server-tls
data:
  ca.crt: xxx
  tls.crt: xxx
  tls.key: xxx
```
