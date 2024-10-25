# configmap-sync-operator
Specialized operator known as `ConfigmapSync`. This operator is designed to seamlessly synchronize config maps from one namespace to others within a cluster.

## Description
This operator was created as part of my learning process about k8s Operators.

#### To test, git clone the repo and:

Install the Operator
```sh
kubebuilder create api --group apps --version v1 --kind ConfigMapSync

make docker-build docker-push IMG=registry/configmapsync-operator:latest
make deploy IMG=registry/configmapsync-operator:latest
```

Create the namespaces and Deploy the ConfigMapSync configuration
```sh
kubectl create ns srcns
kubectl create ns dstns
kubectl apply -f config/samples/apps_v1_configmapsync.yaml
```
sample config:
```yml
apiVersion: apps.pupimvictor.com/v1
kind: ConfigMapSync
metadata:
  labels:
    app.kubernetes.io/name: configmapsync
    app.kubernetes.io/instance: configmapsync-sample
    app.kubernetes.io/part-of: configmapsync
    app.kubernetes.io/managed-by: kustomize
    app.kubernetes.io/created-by: configmapsync
  name: configmapsync-sample
spec:
  sourceNamespace: "srcns"
  destinationNamespace: "dstns"
  configMapName: "my-config"
```


Test
```sh
kubectl get cm -n dstns # no synced cms
kubectl create configmap my-config -n srcns --from-literal=key=val # create in source ns
kubectl get cm -n dstns # my-config cm is present, synced from srcns
```

## Getting Started

### Prerequisites
- go version v1.22.0+
- docker version 17.03+.
- kubectl version v1.11.3+.
- Access to a Kubernetes v1.11.3+ cluster.

### To Deploy on the cluster
**Build and push your image to the location specified by `IMG`:**

```sh
make docker-build docker-push IMG=<some-registry>/configmap-sync-operator:tag
```

**NOTE:** This image ought to be published in the personal registry you specified.
And it is required to have access to pull the image from the working environment.
Make sure you have the proper permission to the registry if the above commands don’t work.

**Install the CRDs into the cluster:**

```sh
make install
```

**Deploy the Manager to the cluster with the image specified by `IMG`:**

```sh
make deploy IMG=<some-registry>/configmap-sync-operator:tag
```

> **NOTE**: If you encounter RBAC errors, you may need to grant yourself cluster-admin
privileges or be logged in as admin.

**Create instances of your solution**
You can apply the samples (examples) from the config/sample:

```sh
kubectl apply -k config/samples/
```

>**NOTE**: Ensure that the samples has default values to test it out.

### To Uninstall
**Delete the instances (CRs) from the cluster:**

```sh
kubectl delete -k config/samples/
```

**Delete the APIs(CRDs) from the cluster:**

```sh
make uninstall
```

**UnDeploy the controller from the cluster:**

```sh
make undeploy
```

## Project Distribution

Following are the steps to build the installer and distribute this project to users.

1. Build the installer for the image built and published in the registry:

```sh
make build-installer IMG=<some-registry>/configmap-sync-operator:tag
```

NOTE: The makefile target mentioned above generates an 'install.yaml'
file in the dist directory. This file contains all the resources built
with Kustomize, which are necessary to install this project without
its dependencies.

2. Using the installer

Users can just run kubectl apply -f <URL for YAML BUNDLE> to install the project, i.e.:

```sh
kubectl apply -f https://raw.githubusercontent.com/<org>/configmap-sync-operator/<tag or branch>/dist/install.yaml
```

## Contributing
// TODO(user): Add detailed information on how you would like others to contribute to this project

**NOTE:** Run `make help` for more information on all potential `make` targets

More information can be found via the [Kubebuilder Documentation](https://book.kubebuilder.io/introduction.html)

## License

Copyright 2024.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

