# vSphere Container Storage Interface (CSI)

[vSphere Container Storage Interface](https://github.com/kubernetes/vsphere-csi-driver) handles storage specific functionality for Kubernetes running on VMware vSphere infrastructure.

## Introduction

This chart deploys all components required to run the vSphere CSI as described on it's [GitHub page](https://vsphere-csi-driver.sigs.k8s.io/).

## Prerequisites

- Has been tested on Kubernetes ????+
- Assumes your Kubernetes cluster has been configured to use the external vSphere CPI cloud provider. Please take a look at configuration guidelines located in the [Kubernetes documentation](https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/#running-cloud-controller-manager).

## Installing the Chart using Helm 3.0+

In Helm 3.0+, there is an effort to move the charts repo into a [distributed model](https://github.com/helm/hub/blob/master/Repositories.md).

To test the helm chart before installing, the following commands can be run:

```bash
cd vsphere-csi-helmchart/
helm template --debug vsphere-csi .
helm install --dry-run  --debug vsphere-csi .
```

Then to install this chart and by providing vCenter information/credentials, run the following command (replacing the placeholder values with the ones for your environment):

```bash
helm install vsphere-csi . --namespace kube-system --set config.enabled=true --set config.vcenter=<vCenter IP> --set config.username=<vCenter Username> --set config.password=<vCenter Password> --set config.datacenter=<vCenter Datacenter> --set config.clusterId='changeme' --set vsphere-cpi.config.enabled=true --set vsphere-cpi.config.vcenter=<vCenter IP> --set vsphere-cpi.config.username=<vCenter Username> --set vsphere-cpi.config.password=<vCenter Password> --set vsphere-cpi.config.datacenter=<vCenter Datacenter>
```

An example can be seen here:

```bash
helm upgrade --install vsphere-csi . --namespace kube-system --set config.enabled=true --set config.vcenter='10.27.51.106' --set config.password='VMware123!' --set config.datacenter='Datacenter' --set netconfig.enabled=true --set netconfig.ips="*" --set netconfig.permissions="READ_WRITE" --set netconfig.rootsquash=true --set netconfig.datastore='ds:///vmfs/volumes/vsan:52e2cfb57ce8d5d3-c12e042893ff2f76/' --set config.clusterId='MyCluster1' --set vsphere-cpi.config.enabled=true --set vsphere-cpi.config.vcenter='10.27.51.106' --set vsphere-cpi.config.password='VMware123!' --set vsphere-cpi.config.datacenter='Datacenter'
```

> **Tip**: List all releases using `helm list --all`

If you want to provide your own `csi-vsphere.conf` and Kubernetes secret `vsphere-config-secret` (for example, to handle multple datacenters/vCenters or for using zones), you can learn more about the `csi-vsphere.conf` and `vsphere-csi` secret by reading the following [documentation](https://vsphere-csi-driver.sigs.k8s.io/driver-deployment/installation.html) and then running the following command:

```bash
helm install vsphere-csi stable/vsphere-csi --namespace kube-system
```

## Installing the Chart using Helm 2.X

To install this chart with the release name `vsphere-csi` and by providing a vCenter information/credentials, run the following command:

```bash
helm install stable/vsphere-csi --name vsphere-csi --namespace kube-system --set config.enabled=true --set config.vcenter=<vCenter IP> --set config.username=<vCenter Username> --set config.password=<vCenter Password> --set config.datacenter=<vCenter Datacenter>
```

If you provide your own `vsphere.conf` and Kubernetes secret `vsphere-csi`, then deploy the chart running the following command:

```bash
helm install stable/vsphere-csi --name vsphere-csi --namespace kube-system
```

## About CSI File Based Persistent Volumes

This helm chart includes a single netconfig to support CSI file shares. This will allow vSAN File Shares to be used as read-write-many Persistent Volumes. To enable a certain IP address range to access the file shares, set specific file share permissions or control the rootsquash parameter, run the following command:

```bash
helm install stable/vsphere-csi --name vsphere-csi --namespace kube-system --set config.enabled=true --set config.vcenter=<vCenter IP> --set config.username=<vCenter Username> --set config.password=<vCenter Password> --set config.datacenter=<vCenter Datacenter> --set netconfig.enabled=true --set netconfig.ips="*" --set netconfig.permissions="READ_WRITE" --set netconfig.rootsquash="true"
```

## Uninstalling the Chart

To uninstall/delete the `vsphere-csi` deployment:

```bash
helm delete vsphere-csi --namespace kube-system
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

> **Tip**: To permanently remove the release using Helm v2.X, run `helm delete --purge vsphere-csi --namespace kube-system`
