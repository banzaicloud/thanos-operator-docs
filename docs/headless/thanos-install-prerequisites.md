## Prerequisites

- Thanos operator requires Kubernetes v1.14.x or later.
- For the [Helm based installation](#install-helm) you need Helm v3.0.2 or later.

1. Create the `monitor` namespace where the Thanos operator and other components will be deployed.

    {{< warning >}}Prometheus operator and Thanos must be deployed into the same namespace.{{< /warning >}}

    ```bash
    kubectl create namespace monitor
    ```

1. (Optional) If you want to store your metrics in an object store, complete the following steps.

    1. Create an Object Store secret.

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

    1. Create the Thanos sidecar definition. Remember to set `externalLabels` as it identifies the Prometheus instance for Thanos.

        Extra configuration for prometheus operator.

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
