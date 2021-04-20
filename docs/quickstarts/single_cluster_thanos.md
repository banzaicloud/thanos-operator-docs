---
title: Single Cluster installation
weight: 100
---

To install the Thanos operator on a single cluster, complete the following steps.

![Single cluster setup with the Thanos operator](../../img/thanos-single-cluster2.png)

{{< include-headless "thanos-install-prerequisites.md" "one-eye/thanos-operator" >}}

Install the Thanos operator using [Helm](#install-helm), the [One Eye CLI]({{< relref "/docs/one-eye/thanos-operator/install.md#install-one-eye" >}}), or [from manifests]({{< relref "/docs/one-eye/thanos-operator/install.md#install-manifest" >}}).

{{< include-headless "thanos-install-helm.md" "one-eye/thanos-operator" >}}

{{< include-headless "thanos-install-verify.md" "one-eye/thanos-operator" >}}
