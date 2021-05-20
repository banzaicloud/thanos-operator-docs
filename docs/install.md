---
title: Install
weight: 100
---

{{< include-headless "thanos-install-prerequisites.md" "thanos-operator" >}}

{{< include-headless "thanos-install-helm.md" "thanos-operator" >}}

## Install the Thanos operator from Kubernetes manifests {#install-manifest}

1. Complete the [Prerequisites](#prerequisites).
1. Install the Thanos operator using the [kustomize deploy model from the project repository](https://github.com/banzaicloud/thanos-operator/blob/master/config/crd/).

    ```bash
    make install
    make deploy IMG=banzaicloud/thanos-operator:latest
    ```

1. Apply the CRDs for a single cluster.

    - Thanos:

        ```yaml
        apiVersion: monitoring.banzaicloud.io/v1alpha1
        kind: Thanos
        metadata:
          name: thanos-sample
        spec:
          query: {}
          rule: {}
          storeGateway: {}
        ```

        To automatically discover all Query instances on the cluster, set the **queryDiscovery** parameter to true:

        ```yaml
        apiVersion: monitoring.banzaicloud.io/v1alpha1
        kind: Thanos
        metadata:
          name: query
        spec:
          query: {}
          queryDiscovery: true
        ```

    - ObjectStore

        ```yaml
        apiVersion: monitoring.banzaicloud.io/v1alpha1
        kind: ObjectStore
        metadata:
          name: objectstore-sample
        spec:
          config:
            mountFrom:
              secretKeyRef:
                name: thanos
                key: object-store.yaml
          bucketWeb: {}
          compactor: {}
        ```

    - StoreEndpoint

        ```yaml
        apiVersion: monitoring.banzaicloud.io/v1alpha1
        kind: StoreEndpoint
        metadata:
        name: storeendpoint-sample
        spec:
        thanos: thanos-sample
        config:
            mountFrom:
            secretKeyRef:
                name: thanos
                key: object-store.yaml
        selector: {}
        ```

        You can specify the URL of remote Prometheus instances in the following **http(s)://<fqdn>:<port>** format, for example:

        ```yaml
        apiVersion: monitoring.banzaicloud.io/v1alpha1
        kind: StoreEndpoint
        metadata:
          name: storeendpoint-sample
        spec:
          thanos: thanos-sample
          config:
            mountFrom:
              secretKeyRef:
                name: thanos
                key: object-store.yaml
          url: http://example.com:10901
        ```

        > Note: If you are running multiple Prometheus instances on single cluster, you can define different Prometheuses and Endpoints in the [StoreEndpoint CRDs]({{< relref "/docs/thanos-operator/types/storeendpoint_types.md" >}}).

1. [Validate your deployment](#validate)

## Install the Thanos operator with the One Eye CLI {#install-one-eye}

[One Eye](/docs/overview/) is a commercial product that can install and configure the Thanos operator and all its dependencies.

1. Install the [One Eye CLI](/docs/cli/install/).

1. Deploy the Thanos operator, optionally with the secret of the object store you have created in the [Prerequisites](#prerequisites).

    ```bash
    one-eye thanos install --prometheus --secret one-eye/object-store.yaml
    ```

1. [Validate your deployment](#validate)

{{< include-headless "thanos-install-verify.md" "thanos-operator" >}}
