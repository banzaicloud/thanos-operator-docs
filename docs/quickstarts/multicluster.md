---
title: Multicluster installation
weight: 200
---

So now we have a single cluster deployment, but what if we have several clusters and need a single means of viewing them? There are several different approaches we can take. For now, we'll explore the most basic. We have multiple clusters with Thanos already installed. We need to configure a Thanos customresource with endpoints that point to these clusters.

> Note: this deployment model is applicable to single cluster multi-namespace
> deployments as well

![Thanos Multi Cluster](../../img/thanos-multi-cluster.png)

**Thanos with only query definition**
```yaml
apiVersion: monitoring.banzaicloud.io/v1alpha1
kind: Thanos
metadata:
  name: thanos-multi
spec:
  query: {}
```

**store-endpoints per cluster**
```yaml
apiVersion: monitoring.banzaicloud.io/v1alpha1
kind: StoreEndpoint
metadata:
  name: remote-cluster-n
spec:
  thanos: thanos-multi
  url: http://remote-cluster-n.com
```

## Observer cluster deployment

This deployment model subtracts the additional workload from the cluster, and moves Thanos components to a dedicated observer cluster.

![Thanos Observer Cluster](../../img/thanos-observer-cluster.png)

**thanos with queryDiscovery definition**
```yaml
apiVersion: monitoring.banzaicloud.io/v1alpha1
kind: Thanos
metadata:
  name: query-master
spec:
  query: {}
  queryDiscovery: true
```

**thanos definition for the remote clusters**
```yaml
apiVersion: monitoring.banzaicloud.io/v1alpha1
kind: Thanos
metadata:
  name: thanos-generic-n
spec:
  query: {}
  rule: {}
  storeGateway: {}
```

```yaml
apiVersion: monitoring.banzaicloud.io/v1alpha1
kind: StoreEndpoint
metadata:
  name: remote-cluster-n
spec:
  thanos: thanos-generic-n
  url: http://remote-cluster-n.com
  config:
    mountFrom:
      secretKeyRef:
        name: thanos
        key: object-store-n.yaml
```
