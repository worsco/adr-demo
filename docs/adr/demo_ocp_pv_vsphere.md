# OpenShift PV-vSphere

Contents:


  - [Summary](#summary)
    - [Issue](#issue)
    - [Decision](#decision)
    - [Status](#status)
  - [Details](#details)
    - [Assumptions](#assumptions)
    - [Constraints](#constraints)
    - [Positions](#positions)
    - [Argument](#argument)
    - [Implications](#implications)
  - [Notes](#notes)

<a name="summary"></a>
## Summary

OpenShift components require persistent storage. Cloud Providers such as GCE, AWS, Azure, and vSphere provide the capability to automatically provision and allocate 'movable' storage to OpenShift nodes. OUR-COMPANY has VMware vSphere on-premise and can fully utilize the vSphere plug-in for OpenShift.

<a name="issue"></a>
### Issue

OUR-COMPANY needs to determine how to provision storage for OpenShift components that require persistent storage.  These components include:

  * Cassandra
  * ElasticSearch
  * AlertManager
  * Prometheus

There are three choices for persistent storage:

  * NFS
  * Local vmdk (host path)
  * VMware vSphere volumes (datastores)

<a name="decision"></a>
### Decision

We will use the following storage types:

| Components | Storage Type |
| --- | --- |
| OCP-Registry | - |
| ElasticSearch | vSphere Volume |
| AlertManager | vSphere Volume |
| Prometheus | vSphere Volume |

All types assigned to 'vSphere Volume' require block storage.  NFS cannot satisfy that requirement, and Red Hat does not recommend using RHEL NFS in production.  NFS can be used for the OCP-Registry in production as long as it is not RHEL-NFS (NetApp NFS is approved).

<a name="status"></a>
### Status

* YYYY-MM-DD A new Service Account (tied into Active Directory) for vCenter has been created
* YYYY-MM-DD Pending Testing using `govc` to test viability of new Service Account

<a name="details"></a>
## Details

<a name="assumptions"></a>
### Assumptions

This is a new storage deployment type within OUR-COMPANY where automation attaches VMDKs to VMs in an automated process.

<a name="constraints"></a>
### Constraints

 * If VMware administrators move the VM from the folder it will break the resource connection to the VMDK, and OpenShift will not be able to relocate the VMDK to another node in the cluster.

* VMs and pods will fail to provision their persistent storage if the VMs are moved to clusters that do not have the DataStore assigned, even when the **disk.EnableUUID=TRUE** option is configured.

* vStorage movement of the VMDKs to a new DataStore will break OpenShift as the CloudProvider in OpenShift will no longer have visibility or knowledge of the new DataStore.

<a name="positions"></a>
### Positions

 * OUR-COMPANY does not want to implement GlusterFS due to: time constraints, budget, additional storage requirements, ramp-up of internal support, network overhead, and additional RAM requirements.
 * OUR-COMPANY wants to use the same storage provisioning method for all components to limit variability.
 * OUR-COMPANY does not want to violate the warning issued by Red Hat about using RHEL NFS in Production.

<a name="argument"></a>
### Argument

TBD

<a name="implications"></a>
### Implications

"VMware-tools" must be installed on all RHEL-based VMs for OpenShift 3.11.x.  open-vm-tools package will be utilized to fulfill this requirement.

<a name="notes"></a>
## Notes

https://docs.openshift.com/container-platform/3.11/install_config/configuring_vsphere.html

Cannot access SDK URL at https://servername/sdk --- It only responds to SOAP requests.
https://kb.vmware.com/s/article/1003218
