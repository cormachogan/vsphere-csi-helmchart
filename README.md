# vSphere Container Storage Interface (CSI)

[vSphere Container Storage Interface](https://github.com/kubernetes-sigs/vsphere-csi-driver) handles storage specific functionality for Kubernetes running on VMware vSphere infrastructure. 

## Introduction

This chart deploys all components required to run the vSphere CSI as described on it's [GitHub page](https://vsphere-csi-driver.sigs.k8s.io/). This Helm chart has been created to work with CSI driver version 2.0, released with vSphere 7.0. This means that it supports both block and file Persistent Volumes. Block PVs can be dynamically created on VMFS, NFS, vSAN and vVol datastores. File PVs can be dynamically created on vSAN datastores with vSAN File Services enabled.

## Prerequisites

- Has been tested on Kubernetes v1.18.3
- This Helm chart assumes that your Kubernetes cluster has not been configured to use the external vSphere CPI cloud provider. It has a dependency on the vSphere CPI cloud provider and will automatically deploy is as part of the CSI deployment. For further information on the vSphere CPI driver, please refer to the following: [Kubernetes documentation](https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/#running-cloud-controller-manager).

## Testing the Helm Chart

To test the helm chart before installing it , the following commands can be run:

```bash
cd vsphere-csi-helmchart/
helm template --debug vsphere-csi .
helm install --dry-run  --debug vsphere-csi .
```

## Installing the Helm Chart for Block based Persistent Volumes

To install this chart for block based PVs, you will need to provide additional vCenter information/credentials. Run the following command (but replace the placeholder values with the ones for your environment):

```bash
helm install vsphere-csi . --namespace kube-system --set config.enabled=true --set config.vcenter=<vCenter IP> --set config.username=<vCenter Username> --set config.password=<vCenter Password> --set config.datacenter=<vCenter Datacenter> --set config.clusterId='changeme' --set vsphere-cpi.config.enabled=true --set vsphere-cpi.config.vcenter=<vCenter IP> --set vsphere-cpi.config.username=<vCenter Username> --set vsphere-cpi.config.password=<vCenter Password> --set vsphere-cpi.config.datacenter=<vCenter Datacenter>
```

A full example can be seen here:

```bash
helm upgrade --install vsphere-csi . --namespace kube-system --set config.enabled=true --set config.vcenter='vcsa-01.rainpole.com' --set config.password='VMware123' --set config.datacenter='Datacenter' --set netconfig.enabled=true --set netconfig.ips='*' --set netconfig.permissions='READ_WRITE' --set netconfig.rootsquash=true --set netconfig.datastore='ds:///vmfs/volumes/vsan:52e2cfb57ce8d5d3-c12e042893ff2f76/' --set config.clusterId='MyCluster1' --set vsphere-cpi.config.enabled=true --set vsphere-cpi.config.vcenter='vcsa-01.rainpole.com' --set vsphere-cpi.config.password='VMware123' --set vsphere-cpi.config.datacenter='Datacenter'
```

> **Tip**: List all releases using `helm list --all`

If you want to provide your own `csi-vsphere.conf` and Kubernetes secret `vsphere-config-secret` (for example, to handle multple datacenters/vCenters or for using zones), you can learn more about the `csi-vsphere.conf` and `vsphere-csi` secret by reading the following [documentation](https://vsphere-csi-driver.sigs.k8s.io/driver-deployment/installation.html).

## Installing the Helm Chart for Block and File based Persistent Volumes

To install this helm chart for both block and file PVs, the chart includes a `netconfig` parameter set to support CSI file shares. This allows vSAN File Shares to be used as read-write-many Persistent Volumes. To enable a certain IP address range to access the file shares, set specific file share permissions or control the rootsquash parameter, run the following command:

```bash
helm install stable/vsphere-csi --name vsphere-csi --namespace kube-system --set config.enabled=true --set config.vcenter=<vCenter IP> --set config.username=<vCenter Username> --set config.password=<vCenter Password> --set config.datacenter=<vCenter Datacenter> --set netconfig.enabled=true --set netconfig.ips="*" --set netconfig.permissions="READ_WRITE" --set netconfig.rootsquash="true"
```

## Uninstalling the Chart

To uninstall/delete the `vsphere-csi` deployment:

```bash
helm delete vsphere-csi --namespace kube-system
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

> **Tip**: To permanently remove the release using Helm, run `helm delete --purge vsphere-csi --namespace kube-system`
