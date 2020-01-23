# Use RHEL Appliances for OpenShift 3.11 Nodes

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

<a name="issue"></a>
### Issue

Using a 'RHEL Appliance' as our base OS for our OpenShift nodes.

OpenShift 4.x is our end goal and all nodes will be CoreOS appliances, using the Red Hat CoreOS containerized OS.  In the meantime, we are using OpenShift 3.11, which uses the RHEL OS for OpenShift nodes.

This OS build requires a "Minimal" package build and a very specific configuration, including SELinux, NetworkManager.   

<a name="decision"></a>
### Decision

Since we are going to be migrating to 4.x in the very near future (2019 Q4), we are going to proceed with using a minimal build of RHEL 7.6 as a 'RHEL Appliance'.  This will allow us to craft a specific OpenShift build which will be internally managed by OpenShift using Ansible control nodes.  This is effectively the same way that OpenShift 4.x will work.

Using RHEL Appliance nodes will prevent churn/spin on making CFEngine work for OpenShift 3.11 nodes when this work will not be necessary for OpenShift 4.x moving forward.


<a name="status"></a>
### Status

* Moving forward with 'RHEL Appliances' in OpenShift LAB
* Will request Security approval for Test/Stage

<a name="details"></a>
## Details

### Appliance Build 
The OpenShift RHEL Appliance is:
* A 'Minimal' installation of RHEL 7.6 from ISO
* Customized partitioning for Openshift
* Standard OUR-COMPANY password 
* Registered to an OpenShift Satellite content view (includes RHEL 7.6 repos)


### Security Controls
* Very small attack plane (Minimal install)
* Command and Control from Ansible Control nodes
* VMWare tools included, can be managed as a standard VM

Details to be determined:
* [IT-SECOPS]: Inclusion of a security scanner user account
* [IT-SECOPS]: Create Update schedule with Red Hat
* [IT-SECOPS]: OpenSCAP scanning (via Satellite?)
* [IT-SECOPS]: Include hosts in IT-CMDB?

<a name="assumptions"></a>
### Assumptions

* Moving to OpenShift 4.2 in 2019 Q4

<a name="constraints"></a>
### Constraints

* Using OUR-COMPANY standard builds for OpenShift nodes would involve significant work and retooling of CFEngine to allow OpenShift to work
* Dynamic state-based changes of OpenShift nodes are not recommended.  OpenShift is intended to be self-managed, which is the required standard in OpenShift 4.x

<a name="implications"></a>
### Implications

<a name="notes"></a>
## Notes

OpenShift host requirements: 
* https://docs.openshift.com/container-platform/3.11/install/prerequisites.html
* OUR-COMPANY IT Security Approval: http://jira.our-company.com/browse/TIKI-8675309

