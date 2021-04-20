## Validate Thanos operator deployment {#validate}

To verify that the installation was successful, complete the following steps.

1. Check the status of the pods. You should see a new thanos-operator pod.

    ```bash
    $ kubectl -n monitor get pods
    NAME                                        READY   STATUS    RESTARTS   AGE
    thanos-operator-7df8485bf6-gf5gk   1/1     Running   0          13s
    ```

1. Check the CRDs. You should see the following three new CRDs.

    ```bash
    $  kubectl get crd
    NAME                                    CREATED AT
    objectstores.monitoring.banzaicloud.io      2020-02-07T21:48:20Z
    storeendpoints.monitoring.banzaicloud.io    2020-02-07T21:48:20Z
    thanos.monitoring.banzaicloud.io            2020-02-07T21:48:20Z
    ```

1. Verify the installation by opening the Thanos query page.

    ```bash
      one-eye thanos connect
    ```