![Benthos Captain](docs/images/icon.png "Benthos Captain")

[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)

> ⚠️ **This is a work in progress proof of concept** ⚠️

Benthos Captain is a Kubernetes Operator to orchestrate [Benthos](https://www.benthos.dev/) pipelines.

## Getting Started

Currently, there isn't a stable release of the operator. If you want to install the operator for development purposes, you can follow the [developer guide](./docs/developer-guide.md).

The operator provides a custom resource for managing Benthos pipelines. Once you've got the operator running, you can deploy a `Pipeline` resource to test it out:

```yaml
---
apiVersion: captain.benthos.dev/v1alpha1
kind: Pipeline
metadata:
  name: pipeline-sample
spec:
  replicas: 1
  config:
    input:
      generate:
        mapping: |
          let favorite_animal = env("FAVORITE_ANIMAL") 
          root = match $favorite_animal {
            "cat" => file("/config/cat.txt")
            "dog" => file("/config/dog.txt")
            _ => file("/config/dog.txt")
          }
        interval: 5s
        count: 0

    pipeline:
      processors:
        - mapping: root = content().uppercase()

    output:
      stdout: {}

  configFiles:
    cat.txt: |
      meow
    dog.txt: |
      woof

  env:
    - name: FAVORITE_ANIMAL
      value: cat
```

Once the resource is deployed, you can monitor the state of the resource:

```bash
kubectl get pipelines

NAME                     READY   PHASE     REPLICAS   AVAILABLE   AGE
pipeline-sample   true    Running   2          2           62s
```
