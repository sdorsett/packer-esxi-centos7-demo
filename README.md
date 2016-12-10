This repository is for a backup packer build of CentOS 7 using the vmware-iso builder and ESXi.

In order to use this repository you need the following:
* Install packer from [this link](https://www.packer.io/downloads.html)
* Install ovftool from [this link](https://my.vmware.com/web/vmware/details?productId=353&downloadGroup=OVFTOOL400)
* A ESXi 5.5 or 6.0 host with standard portgroups for building templates on. DVSwitch portgroups are not currently supports with packer. You will need to perform the steps listed at [the Packer vmware-iso builder page](https://www.packer.io/docs/builders/vmware-iso.html#building-on-a-remote-vsphere-hypervisor) on this ESXi host.
* A DHCP server on the port group you are using to build templates. Packer uses SSH to interact with the template once the OS is installed, so the template needs to be assigned a IP address in order to use provisioners to configure it.
* Modify /templates/centos-7.0-puppet-4.3.2.json to include the specifics of your ESXi host. Specifically `remote_host`, `remote_username`, `remote_password`, `remote_password` and `ethernet0.networkName`

This packer template can be build using the command `packer build templates/centos-7.0-puppet-4.3.2.json`

This template has been tested with ESXi 6.0 and packer 0.12.0 on CentOS 6.8.



