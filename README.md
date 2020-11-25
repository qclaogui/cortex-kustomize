# cortex-kustomize

## Rancher Fleet Support

```yaml
kind: GitRepo
apiVersion: fleet.cattle.io/v1alpha1
metadata:
  # Any name can be used here
  name: repo-cortex-kustomize
  # For single cluster use fleet-local, otherwise use the namespace of
  # your choosing
  namespace: fleet-local
spec:
  # This can be a HTTPS or git URL.  If you are using a git URL then
  # clientSecretName will probably need to be set to supply a credential.
  # repo is the only required parameter for a repo to be monitored.
  #
  repo: https://github.com/qclaogui/cortex-kustomize

  # Enforce all resources go to this target namespace. If a cluster scoped
  # resource is found the deployment will fail.
  #
  # targetNamespace: app1

  # Any branch can be watched, this field is optional. If not specified the
  # branch is assumed to be master
  #
  branch: main

  # A specific commit or tag can also be watched.
  #
  # revision: v0.3.0

  # For a private registry you must supply a clientSecretName. A default
  # secret can be set at the namespace level using the BundleRestriction
  # type. Secrets must be of the type "kubernetes.io/ssh-auth" or
  # "kubernetes.io/basic-auth". The secret is assumed to be in the
  # same namespace as the GitRepo
  #
  clientSecretName: ssh-key-wangweifeng

  # A git repo can read multiple paths in a repo at once.
  # The below field is expected to be an array of paths and
  # supports path globbing (ex: some/*/path)
  #
  # Example:
  # paths:
  # - single-path
  # - multiple-paths/*
  paths:
  - deploy

  # The service account that will be used to perform this deployment.
  # This is the name of the service account that exists in the
  # downstream cluster in the fleet-system namespace. It is assumed
  # this service account already exists so it should be create before
  # hand, most likely coming from another git repo registered with
  # the Fleet manager.
  #
  # serviceAccount: moreSecureAccountThanClusterAdmin

  # Target clusters to deploy to if running Fleet in a multi-cluster
  # style. Refer to the "Mapping to Downstream Clusters" docs for
  # more information.
  #
  # targets: ...
```