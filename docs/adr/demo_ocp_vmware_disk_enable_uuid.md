# Enable disk.EnableUUID on OpenShift VMs in VMware

Contents:


  - [Summary](#summary)
    - [Issue](#issue)
    - [Decision](#decision)
    - [Status](#status)
  - [Details](#details)
    - [Constraints](#constraints)
    - [Implications](#implications)
  - [Notes](#notes)

<a name="summary"></a>
## Summary

<a name="issue"></a>
### Issue

VMware does not enable **disk.EnableUUID=TRUE** by default. This is needed for persistent storage migration from VM to VM when pods move. Enabling this switch in VMware is necessary so that the VMDK always presents a consistent UUID to the VM, thus allowing the disk to be mounted properly within pods (as the pods may move to different nodes in the cluster).

<a name="decision"></a>
### Decision

The setting **disk.EnableUUID=TRUE** to all VMs provisioned for OpenShift.

<a name="status"></a>
### Status

* During the deployment of the VM, the switch will be enabled via Ansible and the vmware_guest module.
* The template **should** have the setting enabled.

<a name="details"></a>
## Details

During the deployment of the VMs using Ansible playbooks and utilizing the [vmware_guest](https://docs.ansible.com/ansible/latest/modules/vmware_guest_module.html) module, the **disk.EnableUUID=TRUE** option is added. The Ansible playbook will override the value set in the template. This setting is required for nodes to have the persistent storage mounted and availble for pod access.  This setting may also be added manually via vSphere, however a power cycle is required of the VM if this change is performed after VM deployment

### Manual process -- by hand

**For each of the virtual machine nodes (VMs) participating in the cluster, perform the following steps:
1. Power off the guest VM
1. Highlight/Select the guest VM
1. Select 'Edit Settings'
1. Select the 'Options' tab
1. Under 'Advanced' section, select 'General'
1. On the right hand side, select 'Configuration Parameters'
1. Inspect the paramter disk.EnableUUID to ensure it is set to 'TRUE'
   - If the parameter is not visible, then select 'Add Row' and add disk.EnableUUID and set it to TRUE
1. Power on the guest VM

### Manual process -- using Ansible and govc, if available

* TBD


<a name="constraints"></a>
### Constraints

* VMs and pods will fail to provision their persistent storage if the VMs are moved to clusters that do not have the datastore assigned, even when the **disk.EnableUUID=TRUE** option is configured.

<a name="implications"></a>
### Implications

If disk.EnableUUID is not performed, VMs in the cluster will not be able to reliably attach their PVC and will not function correctly.

<a name="notes"></a>
## Notes

Search "disk.EnableUUID" in the following web page:

https://docs.openshift.com/container-platform/3.11/install_config/configuring_vsphere.html#vsphere-prereqs

