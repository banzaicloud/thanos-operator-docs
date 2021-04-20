---
title: Architecture
weight: 300
---

## Concepts

![Thanos architecture](../img/thanos-architecture.png)

## Custom Resources

The Thanos operator is instrumented via custom resources. For the detailed options of the custom resources, see {{% xref "/modules/thanos-operator/docs/types/_index.md" %}}.

### [ObjectStore](../types/objectstore_types/)

This custom resource is responsible for object store (`bucket`) management. Thanos compacts and downsamples on storage buckets, and it is highly recommended that you run only **one** `compactor` per bucket.

> Running multiple compactors is not concurrency safe and must be deployed as a singleton against a bucket.

You can use a simple web interface to inspect the chunks stored in the buckets called **bucket**.

This custom resource also provides credential and configuration for the object storage. The following is a sample ObjectStore custom resource.

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
    dataVolume:
      pvc:
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 1Gi
```

### [Thanos](../types/thanos_types/) and [StoreEndpoint](../types/storeendpoint_types/)

Thanos is the main custom resource responsible for Query, Store and Rule configurations. It has a special relationship with StoreEndpoint, which represents distinct StoreAPI endpoints. These can be Sidecar, Store, Rule or any other Store API providers. Thanos resources instantiate per StoreEndpoint. This means that you can use a single Thanos configuration for several Prometheus instances or clusters.

- Sidecar

    - Serves as a sidecar container alongside Prometheus
    - Uploads Prometheus chunks to an object storage
    - Supports object stores like S3, Google Cloud Storage, Azure Storage and more
    - Prometheus operator, which we also use in our integrated monitoring service, solves the injection of this sidecar transparently

- Store

    - Retrieves chunks from object storage in order to provide long term metrics for Query
    - Supports time-based partitioning
    - Supports label-based partitioning

- Query

    - Is the entry point for PromQL queries
    - Deduplicates results from different sources
    - Supports partial responses

- Rule

    - Is a simplified version of Prometheus that does not require a sidecar and does not scrape or do PromQL evaluations
    - Writes results back to the disk in the Prometheus 2.0 storage format
    - Participates in the system as a store node, which means that it exposes StoreAPI and uploads its generated TSDB blocks to an object store

- StoreEndpoint

    - Define local or remote StoreAPI providers
    - Sidecar, Rule, Query, ...
    - Local (Selector) or Remote (URL) configuration
    - Each `Thanos` and `StoreEndpoint` make a unique Stack with separate instances

## Deployment modes
