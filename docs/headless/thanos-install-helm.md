## Install the Thanos operator with Helm {#install-helm}

To install the Thanos operator with Helm 3, complete the following steps. Alternatively, you can install the Thanos operator with the [One Eye CLI](#install-one-eye).

1. Add the Prometheus operator Helm repository.

    ```bash
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    ```

1. Install the Prometheus operator. If you want to use object storage to store your metrics, include the configuration of the Thanos sidecar.

    ```bash
    helm install prometheus-operator --namespace monitor prometheus-community/kube-prometheus-stack -f thanos-sidecar.yaml
    ```

1. Add the Thanos operator chart repository.

    ```bash
    helm repo add banzaicloud-stable https://kubernetes-charts.banzaicloud.com
    helm repo update
    ```

1. Install the Thanos operator.

    ```bash
    helm install thanos-operator --namespace monitor banzaicloud-stable/thanos-operator
   ```