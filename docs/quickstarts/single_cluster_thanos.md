---
title: Single Cluster installation
weight: 100
---

<p align="center"><img src="../../img/thanos-single-cluster2.png" ></p>

## Prerequisites

1. Create `monitor` namespace

    ```bash
    kubectl create namespace monitor
    ```

1. Create Object Store secret

    Example S3 configuration

    ```bash
    cat <<'EOF' >> object-store.yaml
    type: S3
    config:
      endpoint: "s3.eu-west-1.amazonaws.com"
      bucket: "test-bucket"
      region: "eu-west-1"
      access_key: "XXXXXXXXX"
      secret_key: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    EOF
    ```

1. Deploy the secret on Kubernetes

    ```bash
    kubectl create secret generic thanos --from-file=object-store.yaml=object-store.yaml --namespace monitor
    ```

1. Create the Thanos sidecar definition
    Extra configuration for prometheus operator.

    > Note: Prometheus-operator and Thanos MUST be in the same namespace.

    ```bash
    cat <<'EOF' >> thanos-sidecar.yaml
    prometheus:
      prometheusSpec:
        thanos:
          image: quay.io/thanos/thanos:v0.17.2
          version: v0.17.2
          objectStorageConfig:
            name: thanos
            key: object-store.yaml
        externalLabels: 
          cluster: thanos-operator-test
    EOF
    ```

    Remember to set `externalLabels` as it identifies the Prometheus instance for Thanos.

## Install the Thanos Operator with Helm

1. Add the Kubernetes stable Helm repository

    ```bash
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    ```

1. Install prometheus-operator with the Thanos sidecar

    ```bash
    helm install prometheus-operator --namespace monitor prometheus-community/kube-prometheus-stack -f thanos-sidecar.yaml
    ```

1. Add the operator chart repository.

    ```bash
    helm repo add banzaicloud-stable https://kubernetes-charts.banzaicloud.com
    helm repo update
    ```

1. Install the Thanos operator.

    ```bash
    helm install thanos-operator --namespace monitor banzaicloud-stable/thanos-operator
   ```

## Install the Thanos operator with the One Eye CLI

 [One Eye](/docs/one-eye/overview/) will take care of installing and configuring all the dependencies.

1. Install the [One Eye CLI](/docs/one-eye/cli/install/).

1. Deploy the Thanos operator with the secret of the object store you have created in the [Prerequisites](#prerequisites).

    ```bash
    one-eye thanos install --prometheus --secret one-eye/object-store.yaml
    ```

1. Verify the installation by opening the Thanos query page.

    ```bash
      one-eye thanos connect
    ```
