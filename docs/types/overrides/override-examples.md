---
title: Override examples
weight: 50
---

The following examples show you how you can [override parameters](../) in the Thanos operator custom resources.

## Change deployment strategy

The following example changes the deployment strategy of the query frontend pods to Recreate (instead of the default RollingUpdate). Other parameters of the deployment are left to use their default values, so they don't have to be included in the CRD.

```yaml
Kind: Thanos
Spec:
  queryFrontend:
    deploymentOverrides:
      spec:
        strategy: Recreate
```

## Change container image

The following example specifies a custom container image to use for the query-frontend pods.

```yaml
Kind: Thanos
Spec:
  queryFrontend:
    deploymentOverrides:
      spec:
        template:
          spec:
            containers:
            - name: query-frontend
              image: <custom/image:tag>
```
