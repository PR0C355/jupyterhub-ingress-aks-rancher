# JupyterHub with Ingress - Helm Chart

This Helm chart deploys a JupyterHub instance with an Ingress resource. JupyterHub is a multi-user Jupyter Notebook server that allows multiple users to access and run Jupyter Notebooks on a Kubernetes cluster. This chart was designed for use on an Azure Kubernetes Service Cluster but should be applicable to any Kubernetes deployment.

## Prerequisites

Before deploying this Helm chart, ensure that you have the following prerequisites:

- Kubernetes cluster with Helm installed
- Persistent storage provisioner for user notebooks (e.g., a dynamic provisioner like `ReadWriteMany`)

## Installation

1. Clone the repository:

   ```shell
   git clone https://github.com/PR0C355/jupyterhub-ingress-aks.git
   ```

2. Customize the values in `values.yaml` file based on your requirements. You can modify anything normally modifiable in [JupyterHub](https://z2jh.jupyter.org/en/stable/resources/reference.html#helm-chart-configuration-reference), as well as the deployed ingress resource. 

3. Install the Helm chart:

   ```shell
   helm upgrade --cleanup-on-fail \
     --install jupyterhub ./jupyterhub-ingress-aks \
     --namespace jhub \
     --create-namespace
   ```

## Configuration

The following table lists the configurable parameters of the ingress resource in the JupyterHub Helm chart and their default values:

| Parameter                                                                                                        | Description                                                         | Default                                                                               |
|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `ingress.enabled`                                                                                                | Enables Ingress resource creation                                   | `True`                                                                                |
| `ingress.annotations`                                                                                            | Ingress resource annotations                                        | `service.beta.kubernetes.io/azure-load-balancer-health-probe-request-path : /healthz` |
| `ingress.name`                                                                                                   | Name of the ingress resource                                        | `jhub-ingress`                                                                        |
| `ingress.path`                                                                                                   | The path for the ingress rule                                       | (none)                                                                                |
| `ingress.pathType`                                                                                               | The path type for the ingress rule                                  | (none)                                                                                |
| `ingress.serviceName`                                                                                            | The name of the service that the ingress rule will route to         | `proxy-public`                                                                        |
| `ingress.servicePortNumber`                                                                                      | The port of the service that the ingress rule will route to         | `80`                                                                                  |
| `ingress-control.email`                                                                                          | The email to verify domain ownership with                           | (none)                                                                                |
| `ingress-control.ingress-nginx.controller.service.annotations."service.beta.kubernetes.io/azure-dns-label-name"` | The DNS label to use for an Azure Load Balancer to provision a FQDN | `jupyterhub`                                                                          |


You can modify the value of the ingress controller's annotation to fit your specific requirements by editing `ingress-control.ingress-nginx.controller.service.annotations`. Make sure to refer to the Ingress controller's documentation for more details on the available annotations and their configurations.

Please note that the `(none)` values indicate parameters that are dependent on user configuration and are not provided in the given Ingress file.

## Usage

Once the Helm chart is deployed, you can access JupyterHub by navigating to the specified Ingress FQDN in your web browser. You can authenticate using the configured authentication method (e.g., Dummy, GitHub, CILogon, etc.) and start using Jupyter Notebooks.

## Upgrading

To upgrade the JupyterHub deployment with a new version of the Helm chart, use the following command:

```shell
helm upgrade --cleanup-on-fail \
   jupyterhub ./jupyterhub-ingress-aks
   --namespace jhub
```

Ensure to modify the `values.yaml` file if you want to make any configuration changes during the upgrade.

## Uninstallation

To uninstall the JupyterHub deployment, use the following command:

```shell
helm uninstall jupyterhub
```

This will remove all resources associated with the JupyterHub deployment, including the Ingress resource and user notebooks PVCs.

## Limitations

- This Helm chart does not handle automatic scaling of the JupyterHub deployment. You may need to manually adjust the number of replicas based on the workload.
- It is recommended to secure the JupyterHub deployment with appropriate authentication and authorization mechanisms to control user access and prevent unauthorized usage.
- Ensure that your Kubernetes cluster has sufficient resources (CPU, memory, storage) to accommodate the desired number of JupyterHub replicas and user notebooks.
