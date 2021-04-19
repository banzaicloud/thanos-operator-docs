---
title: Install
weight: 100
---

{{< include-headless "thanos-install-prerequisites.md" "one-eye/thanos-operator" >}}

{{< include-headless "thanos-install-helm.md" "one-eye/thanos-operator" >}}

## Install the Thanos operator with the One Eye CLI {#install-one-eye}

[One Eye](/docs/one-eye/overview/) is a commercial product that can install and configure the Thanos operator and all its dependencies.

1. Install the [One Eye CLI](/docs/one-eye/cli/install/).

1. Deploy the Thanos operator, optionally with the secret of the object store you have created in the [Prerequisites](#prerequisites).

    ```bash
    one-eye thanos install --prometheus --secret one-eye/object-store.yaml
    ```

{{< include-headless "thanos-install-verify.md" "one-eye/thanos-operator" >}}
