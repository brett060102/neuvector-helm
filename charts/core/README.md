# NeuVector Helm Chart

Helm chart for NeuVector container security's core services.

## CRD
Because the CRD (Custom Resource Definition) policies can be deployed before NeuVector's core product, a new 'crd' helm chart is created. The crd template in the 'core' chart is kept for the backward compatibility. Please set `crdwebhook.enabled` to false, if you use the new 'crd' chart.

## Choosing container runtime
The NeuVector platform supports docker, cri-o and containerd as the container runtime. For a k3s/rke2, or bottlerocket cluster, they have their own runtime socket path. You should enable their runtime options, `k3s.enabled` and `bottlerocket.enabled`, respectively.

## Configuration

The following table lists the configurable parameters of the NeuVector chart and their default values.

Parameter | Description | Default | Notes
--------- | ----------- | ------- | -----
`openshift` | If deploying in OpenShift, set this to true | `false` |
`registry` | NeuVector container registry | `docker.io` |
`tag` | image tag for controller enforcer manager | `latest` |
`oem` | OEM release name | `nil` |
`imagePullSecrets` | image pull secret | `nil` |
`rbac` | NeuVector RBAC Manifests are installed when RBAC is enabled | `true` | Required for Rancher Authentication. |
`psp` | NeuVector Pod Security Policy when psp policy is enabled | `false` |
`serviceAccount` | Service account name for NeuVector components | `default` |
`leastPrivilege` | Use least privileged service account | `false` |
`global.cattle.url` | Set the Rancher Server URL | | Required for Rancher Authentication. `https://<Rancher_URL>/` |
`controller.enabled` | If true, create controller | `true` |
`controller.image.repository` | controller image repository | `neuvector/controller` |
`controller.image.hash` | controller image hash in the format of sha256:xxxx. If present it overwrites the image tag value. | |
`controller.replicas` | controller replicas | `3` |
`controller.schedulerName` | kubernetes scheduler name | `nil` |
`controller.affinity` | controller affinity rules  | ... | spread controllers to different nodes |
`controller.tolerations` | List of node taints to tolerate | `nil` |
`controller.resources` | Add resources requests and limits to controller deployment | `{}` | see examples in [values.yaml](values.yaml)
`controller.nodeSelector` | Enable and specify nodeSelector labels | `{}` |
`controller.disruptionbudget` | controller PodDisruptionBudget. 0 to disable. Recommended value: 2. | `0` |
`controller.priorityClassName` | controller priorityClassName. Must exist prior to helm deployment. Leave empty to disable. | `nil` |
`controller.podLabels` | Specify the pod labels. | `{}` |
`controller.podAnnotations` | Specify the pod annotations. | `{}` |
`controller.env` | User-defined environment variables for controller. | `[]` |
`controller.ranchersso.enabled` | If true, enable single sign on for Rancher | `false` | Required for Rancher Authentication. |
`controller.pvc.enabled` | If true, enable persistence for controller using PVC | `false` | Require persistent volume type RWX, and storage 1Gi
`controller.pvc.accessModes` | Access modes for the created PVC. | `["ReadWriteMany"]` |
`controller.pvc.existingClaim` | If `false`, a new PVC will be created. If a string is provided, an existing PVC with this name will be used. | `false` |
`controller.pvc.storageClass` | Storage Class to be used | `default` |
`controller.pvc.capacity` | Storage capacity | `1Gi` |
`controller.azureFileShare.enabled` | If true, enable the usage of an existing or statically provisioned Azure File Share | `false` |
`controller.azureFileShare.secretName` | The name of the secret containing the Azure file share storage account name and key | `nil` |
`controller.azureFileShare.shareName` | The name of the Azure file share to use | `nil` |
`controller.apisvc.type` | Controller REST API service type | `nil` |
`controller.apisvc.annotations` | Add annotations to controller REST API service | `{}` |
`controller.apisvc.route.enabled` | If true, create a OpenShift route to expose the Controller REST API service | `false` |
`controller.apisvc.route.termination` | Specify TLS termination for OpenShift route for Controller REST API service. Possible passthrough, edge, reencrypt | `passthrough` |
`controller.apisvc.route.host` | Set controller REST API service hostname | `nil` |
`controller.apisvc.route.tls.key` | Set controller REST API service PEM format key file | `nil` |
`controller.apisvc.route.tls.certificate` | Set controller REST API service PEM format certificate file | `nil` |
`controller.apisvc.route.tls.caCertificate` | Set controller REST API service CA certificate may be required to establish a certificate chain for validation | `nil` |
`controller.apisvc.route.tls.destinationCACertificate` | Set controller REST API service CA certificate to validate the endpoint certificate | `nil` |
`controller.certificate.secret` | Replace controller REST API certificate using secret if secret name is specified | `nil` |
`controller.certificate.keyFile` | Replace controller REST API certificate key file | `tls.key` |
`controller.certificate.pemFile` | Replace controller REST API certificate pem file | `tls.pem` |
`controller.federation.mastersvc.type` | Multi-cluster primary cluster service type. If specified, the deployment will be used to manage other clusters. Possible values include NodePort, LoadBalancer and ClusterIP. | `nil` |
`controller.federation.mastersvc.annotations` | Add annotations to Multi-cluster primary cluster REST API service | `{}` |
`controller.federation.mastersvc.route.enabled` | If true, create a OpenShift route to expose the Multi-cluster primary cluster service | `false` |
`controller.federation.mastersvc.route.host` | Set OpenShift route host for primary cluster service | `nil` |
`controller.federation.mastersvc.route.termination` | Specify TLS termination for OpenShift route for Multi-cluster primary cluster service. Possible passthrough, edge, reencrypt | `passthrough` |
`controller.federation.mastersvc.route.tls.key` | Set PEM format key file for OpenShift route for Multi-cluster primary cluster service | `nil` |
`controller.federation.mastersvc.route.tls.certificate` | Set PEM format key certificate file for OpenShift route for Multi-cluster primary cluster service | `nil` |
`controller.federation.mastersvc.route.tls.caCertificate` | Set CA certificate may be required to establish a certificate chain for validation for OpenShift route for Multi-cluster primary cluster service | `nil` |
`controller.federation.mastersvc.route.tls.destinationCACertificate` | Set CA certificate to validate the endpoint certificate for OpenShift route for Multi-cluster primary cluster service | `nil` |
`controller.federation.mastersvc.ingress.enabled` | If true, create ingress for federation master service, must also set ingress host value | `false` | enable this if ingress controller is installed
`controller.federation.mastersvc.ingress.tls` | If true, TLS is enabled for controller federation master ingress service |`false` | If set, the tls-host used is the one set with `controller.federation.mastersvc.ingress.host`.
`controller.federation.mastersvc.ingress.host` | Must set this host value if ingress is enabled | `nil` |
`controller.federation.mastersvc.ingress.ingressClassName` | To be used instead of the ingress.class annotation if an IngressClass is provisioned | `""` |
`controller.federation.mastersvc.ingress.secretName` | Name of the secret to be used for TLS-encryption | `nil` | Secret must be created separately (Let's encrypt, manually)
`controller.federation.mastersvc.ingress.path` | Set ingress path |`/` | If set, it might be necessary to set a rewrite rule in annotations.
`controller.federation.mastersvc.ingress.annotations` | Add annotations to ingress to influence behavior | `nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"` | see examples in [values.yaml](values.yaml)
`controller.federation.managedsvc.type` | Multi-cluster managed cluster service type. If specified, the deployment will be managed by the managed clsuter. Possible values include NodePort, LoadBalancer and ClusterIP. | `nil` |
`controller.federation.managedsvc.annotations` | Add annotations to Multi-cluster managed cluster REST API service | `{}` |
`controller.federation.managedsvc.route.enabled` | If true, create a OpenShift route to expose the Multi-cluster managed cluster service | `false` |
`controller.federation.managedsvc.route.host` | Set OpenShift route host for manageed service | `nil` |
`controller.federation.managedsvc.route.termination` | Specify TLS termination for OpenShift route for Multi-cluster managed cluster service. Possible passthrough, edge, reencrypt | `passthrough` |
`controller.federation.managedsvc.route.tls.key` | Set PEM format key file for OpenShift route for Multi-cluster managed cluster service | `nil` |
`controller.federation.managedsvc.route.tls.certificate` | Set PEM format certificate file for OpenShift route for Multi-cluster managed cluster service | `nil` |
`controller.federation.managedsvc.route.tls.caCertificate` | Set CA certificate may be required to establish a certificate chain for validation for OpenShift route for Multi-cluster managed cluster service | `nil` |
`controller.federation.managedsvc.route.tls.destinationCACertificate` | Set CA certificate to validate the endpoint certificate for OpenShift route for Multi-cluster managed cluster service | `nil` |
`controller.federation.managedsvc.ingress.enabled` | If true, create ingress for federation managed service, must also set ingress host value | `false` | enable this if ingress controller is installed
`controller.federation.managedsvc.ingress.tls` | If true, TLS is enabled for controller federation managed ingress service |`false` | If set, the tls-host used is the one set with `controller.federation.managedsvc.ingress.host`.
`controller.federation.managedsvc.ingress.host` | Must set this host value if ingress is enabled | `nil` |
`controller.federation.managedsvc.ingress.ingressClassName` | To be used instead of the ingress.class annotation if an IngressClass is provisioned | `""` |
`controller.federation.managedsvc.ingress.secretName` | Name of the secret to be used for TLS-encryption | `nil` | Secret must be created separately (Let's encrypt, manually)
`controller.federation.managedsvc.ingress.path` | Set ingress path |`/` | If set, it might be necessary to set a rewrite rule in annotations.
`controller.federation.managedsvc.ingress.annotations` | Add annotations to ingress to influence behavior | `nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"` | see examples in [values.yaml](values.yaml)
`controller.ingress.enabled` | If true, create ingress for rest api, must also set ingress host value | `false` | enable this if ingress controller is installed
`controller.ingress.tls` | If true, TLS is enabled for controller rest api ingress service |`false` | If set, the tls-host used is the one set with `controller.ingress.host`.
`controller.ingress.host` | Must set this host value if ingress is enabled | `nil` |
`controller.ingress.ingressClassName` | To be used instead of the ingress.class annotation if an IngressClass is provisioned | `""` |
`controller.ingress.secretName` | Name of the secret to be used for TLS-encryption | `nil` | Secret must be created separately (Let's encrypt, manually)
`controller.ingress.path` | Set ingress path |`/` | If set, it might be necessary to set a rewrite rule in annotations.
`controller.ingress.annotations` | Add annotations to ingress to influence behavior | `nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"` | see examples in [values.yaml](values.yaml)
`controller.configmap.enabled` | If true, configure NeuVector global settings using a ConfigMap | `false`
`controller.configmap.data` | NeuVector configuration in YAML format | `{}`
`controller.secret.enabled` | If true, configure NeuVector global settings using secrets | `false`
`controller.secret.data` | NeuVector configuration in key/value pair format | `{}`
`enforcer.enabled` | If true, create enforcer | `true` |
`enforcer.image.repository` | enforcer image repository | `neuvector/enforcer` |
`enforcer.image.hash` | enforcer image hash in the format of sha256:xxxx. If present it overwrites the image tag value. | |
`enforcer.updateStrategy.type` | enforcer update strategy type. | `RollingUpdate` |
`enforcer.priorityClassName` | enforcer priorityClassName. Must exist prior to helm deployment. Leave empty to disable. | `nil` |
`enforcer.podLabels` | Specify the pod labels. | `{}` |
`enforcer.podAnnotations` | Specify the pod annotations. | `{}` |
`enforcer.env` | User-defined environment variables for enforcers. | `[]` |
`enforcer.tolerations` | List of node taints to tolerate | `- effect: NoSchedule`<br>`key: node-role.kubernetes.io/master` | other taints can be added after the default
`enforcer.resources` | Add resources requests and limits to enforcer deployment | `{}` | see examples in [values.yaml](values.yaml)
`manager.enabled` | If true, create manager | `true` |
`manager.image.repository` | manager image repository | `neuvector/manager` |
`manager.image.hash` | manager image hash in the format of sha256:xxxx. If present it overwrites the image tag value. | |
`manager.priorityClassName` | manager priorityClassName. Must exist prior to helm deployment. Leave empty to disable. | `nil` |
`manager.podLabels` | Specify the pod labels. | `{}` |
`manager.podAnnotations` | Specify the pod annotations. | `{}` |
`manager.env.ssl` | If false, manager will listen on HTTP access instead of HTTPS | `true` |
`manager.svc.type` | set manager service type for native Kubernetes | `NodePort`;<br>if it is OpenShift platform or ingress is enabled, then default is `ClusterIP` | set to LoadBalancer if using cloud providers, such as Azure, Amazon, Google
`manager.svc.loadBalancerIP` | if manager service type is LoadBalancer, this is used to specify the load balancer's IP | `nil` |
`manager.svc.annotations` | Add annotations to manager service | `{}` | see examples in [values.yaml](values.yaml)
`manager.custom.loginLogo` | Custom UI login logo. The input is a SVG file, encoded in base64. The logo is displayed as a 300 x 80 pixels icon. | |
`manager.custom.pageHeader` | Custom UI page header. The input can be HTML or TEXT, encoded in base64. Keep the input less than two lines and each line shorter than 100 characters. | |
`manager.custom.eulaPrompt` | Custom UI EULA prompt. The input can be HTML or TEXT, encoded in base64. Keep the input less than two lines and each line shorter than 50 characters. | |
`manager.custom.eulaPolicy` | Custom UI EULA policy. The input can be HTML or TEXT, encoded in base64. | |
`manager.route.enabled` | If true, create a OpenShift route to expose the management console service | `true` |
`manager.route.host` | Set OpenShift route host for management console service | `nil` |
`manager.route.termination` | Specify TLS termination for OpenShift route for management console service. Possible passthrough, edge, reencrypt | `passthrough` |
`manager.route.tls.key` | Set PEM format key file for OpenShift route for management console service | `nil` |
`manager.route.tls.certificate` | Set PEM format certificate file for OpenShift route for management console service | `nil` |
`manager.route.tls.caCertificate` | Set CA certificate may be required to establish a certificate chain for validation for OpenShift route for management console service | `nil` |
`manager.route.tls.destinationCACertificate` | Set controller REST API service CA certificate to validate the endpoint certificate for OpenShift route for management console service | `nil` |
`manager.certificate.secret` | Replace manager UI certificate using secret if secret name is specified | `nil` |
`manager.certificate.keyFile` | Replace manager UI certificate key file | `tls.key` |
`manager.certificate.pemFile` | Replace manager UI certificate pem file | `tls.pem` |
`manager.ingress.enabled` | If true, create ingress, must also set ingress host value | `false` | enable this if ingress controller is installed
`manager.ingress.host` | Must set this host value if ingress is enabled | `nil` |
`manager.ingress.ingressClassName` | To be used instead of the ingress.class annotation if an IngressClass is provisioned | `""` |
`manager.ingress.path` | Set ingress path |`/` | If set, it might be necessary to set a rewrite rule in annotations. Currently only supports `/`
`manager.ingress.annotations` | Add annotations to ingress to influence behavior | `nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"` | see examples in [values.yaml](values.yaml)
`manager.ingress.tls` | If true, TLS is enabled for manager ingress service |`false` | If set, the tls-host used is the one set with `manager.ingress.host`.
`manager.ingress.secretName` | Name of the secret to be used for TLS-encryption | `nil` | Secret must be created separately (Let's encrypt, manually)
`manager.resources` | Add resources requests and limits to manager deployment | `{}` | see examples in [values.yaml](values.yaml)
`manager.affinity` | manager affinity rules  | `{}` |
`manager.tolerations` | List of node taints to tolerate | `nil` |
`manager.nodeSelector` | Enable and specify nodeSelector labels | `{}` |
`manager.runAsUser` | Specify the run as User ID | `nil` |
`cve.adapter.enabled` | If true, create registry adapter | `true` |
`cve.adapter.image.repository` | registry adapter image repository | `neuvector/registry-adapter` |
`cve.adapter.image.tag` | registry adapter image tag | |
`cve.adapter.image.hash` | registry adapter image hash in the format of sha256:xxxx. If present it overwrites the image tag value. | |
`cve.adapter.priorityClassName` | registry adapter priorityClassName. Must exist prior to helm deployment. Leave empty to disable. | `nil` |
`cve.adapter.podLabels` | Specify the pod labels. | `{}` |
`cve.adapter.podAnnotations` | Specify the pod annotations. | `{}` |
`cve.adapter.env` | User-defined environment variables for adapter. | `[]` |
`cve.adapter.svc.type` | set registry adapter service type for native Kubernetes | `NodePort`;<br>if it is OpenShift platform or ingress is enabled, then default is `ClusterIP` | set to LoadBalancer if using cloud providers, such as Azure, Amazon, Google
`cve.adapter.svc.loadBalancerIP` | if registry adapter service type is LoadBalancer, this is used to specify the load balancer's IP | `nil` |
`cve.adapter.svc.annotations` | Add annotations to registry adapter service | `{}` | see examples in [values.yaml](values.yaml)
`cve.adapter.harbor.protocol` | Harbor registry request protocol [http|https] | `https` |
`cve.adapter.harbor.secretName` | Harbor registry adapter's basic authentication secret | |
`cve.adapter.route.enabled` | If true, create a OpenShift route to expose the management console service | `true` |
`cve.adapter.route.host` | Set OpenShift route host for management console service | `nil` |
`cve.adapter.route.termination` | Specify TLS termination for OpenShift route for management console service. Possible passthrough, edge, reencrypt | `passthrough` |
`cve.adapter.route.tls.key` | Set PEM format key file for OpenShift route for management console service | `nil` |
`cve.adapter.route.tls.certificate` | Set PEM format certificate file for OpenShift route for management console service | `nil` |
`cve.adapter.route.tls.caCertificate` | Set CA certificate may be required to establish a certificate chain for validation for OpenShift route for management console service | `nil` |
`cve.adapter.route.tls.destinationCACertificate` | Set controller REST API service CA certificate to validate the endpoint certificate for OpenShift route for management console service | `nil` |
`cve.adapter.certificate.secret` | Replace registry adapter certificate using secret if secret name is specified | `nil` |
`cve.adapter.certificate.keyFile` | Replace registry adapter certificate key file | `tls.key` |
`cve.adapter.certificate.pemFile` | Replace registry adapter certificate pem file | `tls.pem` |
`cve.adapter.ingress.enabled` | If true, create ingress, must also set ingress host value | `false` | enable this if ingress controller is installed
`cve.adapter.ingress.host` | Must set this host value if ingress is enabled | `nil` |
`cve.adapter.ingress.ingressClassName` | To be used instead of the ingress.class annotation if an IngressClass is provisioned | `""` |
`cve.adapter.ingress.path` | Set ingress path |`/` | If set, it might be necessary to set a rewrite rule in annotations. Currently only supports `/`
`cve.adapter.ingress.annotations` | Add annotations to ingress to influence behavior | `nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"` | see examples in [values.yaml](values.yaml)
`cve.adapter.ingress.tls` | If true, TLS is enabled for registry adapter ingress service |`false` | If set, the tls-host used is the one set with `cve.adapter.ingress.host`.
`cve.adapter.ingress.secretName` | Name of the secret to be used for TLS-encryption | `nil` | Secret must be created separately (Let's encrypt, manually)
`cve.adapter.resources` | Add resources requests and limits to registry adapter deployment | `{}` | see examples in [values.yaml](values.yaml)
`cve.adapter.affinity` | registry adapter affinity rules  | `{}` |
`cve.adapter.tolerations` | List of node taints to tolerate | `nil` |
`cve.adapter.nodeSelector` | Enable and specify nodeSelector labels | `{}` |
`cve.adapter.runAsUser` | Specify the run as User ID | `nil` |
`cve.updater.enabled` | If true, create cve updater | `true` |
`cve.updater.secure` | If ture, API server's certificate is validated  | `false` |
`cve.updater.image.registry` | cve updater image registry to overwrite global registry | |
`cve.updater.image.repository` | cve updater image repository | `neuvector/updater` |
`cve.updater.image.tag` | image tag for cve updater | `latest` |
`cve.updater.image.hash` | cve updateer image hash in the format of sha256:xxxx. If present it overwrites the image tag value. | |
`cve.updater.priorityClassName` | cve updater priorityClassName. Must exist prior to helm deployment. Leave empty to disable. | `nil` |
`cve.updater.podLabels` | Specify the pod labels. | `{}` |
`cve.updater.podAnnotations` | Specify the pod annotations. | `{}` |
`cve.updater.schedule` | cronjob cve updater schedule | `0 0 * * *` |
`cve.updater.nodeSelector` | Enable and specify nodeSelector labels | `{}` |
`cve.updater.runAsUser` | Specify the run as User ID | `nil` |
`cve.scanner.enabled` | If true, cve scanners will be deployed | `true` |
`cve.scanner.image.registry` | cve scanner image registry to overwrite global registry | |
`cve.scanner.image.repository` | cve scanner image repository | `neuvector/scanner` |
`cve.scanner.image.tag` | cve scanner image tag | `latest` |
`cve.scanner.image.hash` | cve scanner image hash in the format of sha256:xxxx. If present it overwrites the image tag value. | |
`cve.scanner.priorityClassName` | cve scanner priorityClassName. Must exist prior to helm deployment. Leave empty to disable. | `nil` |
`cve.scanner.podLabels` | Specify the pod labels. | `{}` |
`cve.scanner.podAnnotations` | Specify the pod annotations. | `{}` |
`cve.scanner.env` | User-defined environment variables for scanner. | `[]` |
`cve.scanner.replicas` | external scanner replicas | `3` |
`cve.scanner.dockerPath` | the remote docker socket if CI/CD integration need scan images before they are pushed to the registry | `nil` |
`cve.scanner.resources` | Add resources requests and limits to scanner deployment | `{}` | see examples in [values.yaml](values.yaml) |
`cve.scanner.affinity` | scanner affinity rules  | `{}` |
`cve.scanner.tolerations` | List of node taints to tolerate | `nil` |
`cve.scanner.nodeSelector` | Enable and specify nodeSelector labels | `{}` |
`cve.scanner.runAsUser` | Specify the run as User ID | `nil` |
`docker.path` | docker path | `/var/run/docker.sock` |
`containerd.enabled` | Set to true, if the container runtime is containerd | `false` | **Note**: For k3s and rke clusters, set k3s.enabled to true instead
`containerd.path` | If containerd is enabled, this local containerd socket path will be used | `/var/run/containerd/containerd.sock` |
`crio.enabled` | Set to true, if the container runtime is cri-o | `false` |
`crio.path` | If cri-o is enabled, this local cri-o socket path will be used | `/var/run/crio/crio.sock` |
`k3s.enabled` | Set to true for k3s or rke2 | `false` |
`k3s.runtimePath` | If k3s is enabled, this local containerd socket path will be used | `/run/k3s/containerd/containerd.sock` |
`bottlerocket.enabled` | Set to true if using AWS bottlerocket | `false` |
`bottlerocket.runtimePath` | If bottlerocket is enabled, this local containerd socket path will be used | `/run/dockershim.sock` |
`admissionwebhook.type` | admission webhook type | `ClusterIP` |
`crdwebhook.enabled` | Enable crd service and create crd related resources | `true` |
`crdwebhook.type` | crd webhook type | `ClusterIP` |
`awsbilling.enabled` | If true, install AWS billing csp adapter | `false` |
`awsbilling.accountNumber` | AWS Account Number | `nil` | Follow AWS subscription instruction
`awsbilling.roleName` | AWS Role name for billing | `nil` | Follow AWS subscription instruction
`awsbilling.serviceAccount` | Service account name for csp adapter | `csp` | Follow AWS subscription instruction
`awsbilling.annotations` | Annotaion for csp adapter  | `nil` | Follow AWS subscription instruction
`awsbilling.imagePullSecrets` | Pull secret for csp adapter image | `nil` | Follow AWS subscription instruction
`awsbilling.image.repository` | csp adapter image repository | `neuvector/neuvector-csp-adapter` | Follow AWS subscription instruction
`awsbilling.image.tag` | csp adapter image tag | `1.0.0` | Follow AWS subscription instruction
`awsbilling.image.imagePullPolicy` | csp adapter image pull policy | `IfNotPresent` | Follow AWS subscription instruction

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
$ helm install my-release --namespace neuvector ./neuvector-helm/ --set manager.env.ssl=off
```

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example,

```console
$ helm install my-release --namespace neuvector ./neuvector-helm/ -f values.yaml
```
