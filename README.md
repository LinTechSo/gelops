# GelOps
K8s Operator for Grafana Enterprise Logs plugin

## Description
Manage tenants and LBAC in a kubernetes friendly way!

For more information about Grafana Enterprise Logs visit [GEL](https://grafana.com/docs/enterprise-logs/latest/)

**Note:** This Operator currently developed on GEL version 1.7.x

## Getting Started

1. Create a Tenant

```
apiVersion: loki.gelops.com/v1alpha1
kind: GrafanaEnterpriseLogsTenant
metadata:
  name: lintechso-tenant
spec:
  tenantInfo:
    name: "lintechso"
    displayName: "LinTechSo"
    clusterName: "loki"
```
result:

<img width="1450" alt="Screenshot 1402-05-20 at 12 35 36" src="https://github.com/LinTechSo/gel-plugin-operator/assets/53074289/a18111d9-68a8-4329-9bda-c25e8b05dea7">


2. Create a LBAC

```
apiVersion: loki.gelops.com/v1alpha1
kind: GrafanaEnterpriseLogsAccessPolicy
metadata:
  name: lintechso-access-policy
spec:
  tenantInfoRef: 
    tenantName: "lintechso"
    clusterName: "loki"
    accessPolicies:
      -  "logs:read"
      -  "logs:write"
    labelSelectors:
      - '{foo="bar"}'
      - '{name="hello"}'
```
result:

<img width="1200" alt="Screenshot 1402-05-20 at 12 30 35" src="https://github.com/LinTechSo/gel-plugin-operator/assets/53074289/3a9be440-a898-4b86-8b8e-4b5e873610b7">


3. Create a Token

```
apiVersion: loki.gelops.com/v1alpha1
kind: GrafanaEnterpriseLogsToken
metadata:
  name: lintechso-token
spec:
  accessPolicyRef: 
    name: "lintechso-access-policy"
  expirationTime: "2025-03-01T17:37:59.341728283Z"
```
result:

<img width="1202" alt="Screenshot 1402-05-20 at 12 31 21" src="https://github.com/LinTechSo/gel-plugin-operator/assets/53074289/35da2f25-d76e-466d-9513-8a9d8a5cfb13">


```
$kubctl get secrets/lintechso-token -o yaml
```


```
apiVersion: v1
data:
  token: xxxxxxxxx
kind: Secret
metadata:
  name: lintechso-token
type: Opaque
```

### Running on the cluster
1. Checkout the deployment env
```
export Loki_Endpoint_Api_Version=v3
export Loki_Endpoint_Address="http://loki.com"
export Loki_Admin_Api_Token="X"
```

2. Install Instances of Custom Resources:

```sh
kubectl apply -f config/samples/
```

3. Build and push your image to the location specified by `IMG`:

```sh
make docker-build docker-push IMG=<some-registry>/gel:tag
```

4. Deploy the controller to the cluster with the image specified by `IMG`:

```sh
make deploy IMG=<some-registry>/gel:tag
```

### Uninstall CRDs
To delete the CRDs from the cluster:

```sh
make uninstall
```

### Undeploy controller
UnDeploy the controller from the cluster:

```sh
make undeploy
```

## Contributing

### How it works
This project aims to follow the Kubernetes [Operator pattern](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/).

It uses [Controllers](https://kubernetes.io/docs/concepts/architecture/controller/),
which provide a reconcile function responsible for synchronizing resources until the desired state is reached on the cluster.

### Test It Out
1. Add Loki Endpoint credentials as an ENV
```
export Loki_Endpoint_Api_Version=v3
export Loki_Endpoint_Address="http://loki.com"
export Loki_Admin_Api_Token="X"
```

2. Install the CRDs into the cluster:

```sh
make install
```

3. Run your controller (this will run in the foreground, so switch to a new terminal if you want to leave it running):

```sh
make run
```

**NOTE:** You can also run this in one step by running: `make install run`

### Modifying the API definitions
If you are editing the API definitions, generate the manifests such as CRs or CRDs using:

```sh
make manifests
```

**NOTE:** Run `make --help` for more information on all potential `make` targets

More information can be found via the [Kubebuilder Documentation](https://book.kubebuilder.io/introduction.html)

## License

Copyright 2023.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

