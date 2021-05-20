---
title: Multicluster installation
weight: 200
---

Once you have a [single cluster deployment of Thanos]({{< relref "/docs/thanos-operator/quickstarts/single_cluster_thanos.md" >}}), you might want to access the metrics of several clusters in one location. There are different approaches to achieve that.

## Multicluster deployment

We assume you have multiple clusters with Thanos already installed. You need to configure a Thanos custom resource with endpoints that point to these clusters.

> Note: this deployment model is applicable to single cluster multi-namespace deployments as well

![Thanos Multi Cluster](../../img/thanos-multi-cluster.png)

1. Configure only the query definition in the Thanos custom resource.

    ```yaml
    apiVersion: monitoring.banzaicloud.io/v1alpha1
    kind: Thanos
    metadata:
      name: thanos-multi
    spec:
      query: {}
    ```

1. Configure the StoreEndpoints on each cluster.

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

This deployment model removes the additional workloads from the clusters, and moves most of the Thanos components to a dedicated observer cluster.

![Thanos Observer Cluster](../../img/thanos-observer-cluster.png)

1. On the observer cluster, enable the queryDiscovery option:

    ```yaml
    apiVersion: monitoring.banzaicloud.io/v1alpha1
    kind: Thanos
    metadata:
      name: query-master
    spec:
      query: {}
      queryDiscovery: true
    ```

1. On the remote (peer) clusters, apply the following changes.

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
