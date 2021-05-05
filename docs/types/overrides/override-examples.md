---
title: Override examples
weight: 50
---

The following examples show you how you can [override parameters](../) in the Thanos operator custom resources.

## Change deployment strategy

The following example changes the deployment strategy of the query frontend pods to Recreate (instead of the default RollingUpdate). Other parameters of the deployment are left to use their default values, so they don't have to be included in the CRD.

```yaml
apiVersion: monitoring.banzaicloud.io/v1alpha1
kind: Thanos
spec:
  queryFrontend:
    deploymentOverrides:
      spec:
        strategy: Recreate
```

## Change container image

The following example specifies a custom container image to use for the query-frontend pods.

```yaml
apiVersion: monitoring.banzaicloud.io/v1alpha1
kind: Thanos
spec:
  queryFrontend:
    deploymentOverrides:
      spec:
        template:
          spec:
            containers:
            - name: query-frontend
              image: <custom/image:tag>
```

## Set requests and limits for store

The following example sets resource requests and limits for stores.

```yaml
apiVersion: monitoring.banzaicloud.io/v1alpha1
kind: Thanos
metadata:
  name: thanos-sample
spec:
  storeGateway:
    deploymentOverrides:
      spec:
        template:
          spec:
            containers:
            - name: store
              resources:
                requests:
                  memory: "1Gi"
                  cpu: "250m"
                limits:
                  memory: "2Gi"
                  cpu: "500m"
```
