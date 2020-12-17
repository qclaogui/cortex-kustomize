# Cortex Kustomize

## Kustomize background

[Kustomize](https://kustomize.io) is a CNCF project that is a part of Kubernetes.  It's included in
the `kubectl` in order to allow users to customize their configurations without introducing templates.

### Generate customized YAML with:

`kustomize build deploy/base/blocks > deploy/base/blocks/deploy.yaml`

### The YAML can be directly applied to a cluster:

`kustomize build deploy/base/blocks | kubectl apply -f -`

## Usage

kustomize encourages defining multiple variants - e.g. dev, staging and prod, as overlays on a common base.

It’s possible to create an additional overlay to compose these variants together - just declare the overlays as the bases of a new kustomization.

[cortex-kustomize](https://github.com/qclaogui/cortex-kustomize) provides a common base for [Blocks Storage](https://cortexmetrics.io/docs/blocks-storage/) deployment to Kubernetes. People should [Create variants using overlays](https://github.com/kubernetes-sigs/kustomize#2-create-variants-using-overlays) to deploy Cortex in their own environment.

> An overlay is just another kustomization, referring to the base, and referring to patches to apply to that base.
 This arrangement makes it easy to manage your configuration with git. The base could have files from an upstream repository managed by someone else. The overlays could be in a repository you own. Arranging the repo clones as siblings on disk avoids the need for git submodules (though that works fine, if you are a submodule fan).


### Example

This is an example of monitoring Cortex by adding `prometheus` and `grafana` using kustomize

1. Create `development` Environment

   `mkdir -p deploy/overlays/dev`

2) Create `kustomization.yaml`

    ```yaml
   apiVersion: kustomize.config.k8s.io/v1beta1
   kind: Kustomization
   
   namespace: cortex-monitoring-system
   
   resources:
   - github.com/qclaogui/cortex-kustomize/deploy/base/blocks?ref=main
   - add-grafana-dep.yaml
   - add-grafana-svc.yaml
   - add-retrieval-dep.yaml
   - add-retrieval-svc.yaml
   
   patchesStrategicMerge:
   - patch-nginx-svc.yaml
   
   images:
   - name: quay.io/cortexproject/cortex
     newTag: master-b6eea5f
   - name: minio/minio
     newTag: RELEASE.2021-06-17T00-10-46Z
    ```

File structure:
```shell
   └── deploy
       └── overlays
           └── dev
               ├── add-grafana-dep.yaml
               ├── add-grafana-svc.yaml
               ├── add-retrieval-dep.yaml
               ├── add-retrieval-svc.yaml
               ├── kustomization.yaml
               └── patch-nginx-svc.yaml

```

3. Deploy to a cluster

`kustomize build deploy/overlays/dev | kubectl apply -f -`

More example detailed [https://github.com/qclaogui/cortex-kustomize-demo](https://github.com/qclaogui/cortex-kustomize-demo).

Happy Cortex