This repository is for a demo packer build of CentOS 7 using the vmware-iso builder and ESXi.

In order to use this repository you need the following:
* Install packer from [this link](https://www.packer.io/downloads.html)
* Install ovftool from [this link](https://my.vmware.com/web/vmware/details?productId=353&downloadGroup=OVFTOOL400)
* Download a copy of the CentOS 7 minimal .iso and put is into the iso/ directory. You can find more details about this by looking at the iso/info.txt file in this repository.
* A ESXi 5.5 or 6.0 host with standard portgroups for building templates on. DVSwitch portgroups are not currently supports with packer. You will need to perform the steps listed at [the Packer vmware-iso builder page](https://www.packer.io/docs/builders/vmware-iso.html#building-on-a-remote-vsphere-hypervisor) on this ESXi host.
* A DHCP server on the port group you are using to build templates. Packer uses SSH to interact with the template once the OS is installed, so the template needs to be assigned a IP address in order to use provisioners to configure it.
* (*optional*) A vSphere server if you want to use the vsphere post-processor to automatically deploy the packer generated template. If you do not want packer to automatically deploy the generated template to vSphere, you can remove the following post-provisioner block from templates/centos-7.0-puppet-4.3.2.json
```
  "post-processors": [
    {
      "type": "vsphere",
      "host": "{{user `packer_vcenter_host`}}",
      "username": "{{user `packer_vcenter_username`}}",
      "password": "{{user `packer_vcenter_password`}}",
      "datacenter": "{{user `packer_vcenter_dc`}}",
      "cluster": "{{user `packer_vcenter_cluster`}}",
      "datastore": "{{user `packer_vcenter_datastore`}}",
      "vm_folder": "{{user `template-name`}}",
      "vm_name": "{{user `template_name`}}-{{user `version`}}",
      "vm_network": "{{user `packer_vcenter_portgroup`}}",
      "overwrite": "true",
      "insecure" : "{{user `packer_vcenter_insecure`}}"
    }
  ],
```

* (*optional*) Create a file to include the environmental variables for the specifics of your the ESXi host, used for building the template, and vsphere host ,used for deploying the template that is built. Here is an example file with the environmental variables referenced in the Packer template:
```
[root@packer ~]# cat ~/.packer-remote-creds 
export ROOT_PASSWD='P@ssword123'
export PACKER_ESXI_HOST='192.168.1.51'
export PACKER_ESXI_USERNAME='root'
export PACKER_ESXI_PASSWORD='password'
export PACKER_ESXI_DATASTORE='datastore-01'
export PACKER_ESXI_PORTGROUP='VM_Network'
export PACKER_VCENTER_HOST='192.168.1.52'
export PACKER_VCENTER_USERNAME='administrator@vsphere.local'
export PACKER_VCENTER_PASSWORD='P@ssword123'
export PACKER_VCENTER_DC='datacenter-01'
export PACKER_VCENTER_CLUSTER='cluster-01'
export PACKER_VCENTER_DATASTORE='datastore-01'
export PACKER_VCENTER_FOLDER='templates'
export PACKER_VCENTER_PORTGROUP='VM_Network'
export PACKER_VCENTER_INSECURE='true'
[root@packer ~]# 
```
Once the file containing the environmental variables is create you can source it before running a `packer build` like this:
```
source ~/.packer-remote-creds; packer build templates/centos-7.0-puppet-4.3.2.json
```
If you do not want to use environmental variables, you can hard code the values in the variable block of templates/centos-7.0-puppet-4.3.2.json:
```
  "variables": {
    "template_name": "centos-70-puppet-432",
    "username": "root",
    "password": "P@ssword123",
    "version": "0.0.1",

    "packer_esxi_host": "192.168.1.51",
    "packer_esxi_username": "root",
    "packer_esxi_password": "password",
    "packer_esxi_datastore": "datastore-01",
    "packer_esxi_portgroup": "VM_Network",

    "packer_vcenter_host": "192.168.1.52",
    "packer_vcenter_username": "administrator@vsphere.local",
    "packer_vcenter_password": "P@ssword123",
    "packer_vcenter_dc": "datacenter-01",
    "packer_vcenter_cluster": "cluster-01",
    "packer_vcenter_datastore": "datastore-01",
    "packer_vcenter_folder": "centos-70-puppet-432",
    "packer_vcenter_portgroup": "VM_Network",
    "packer_vcenter_insecure": "true"
  }  
```

This template has been tested with ESXi 6.0 and packer 0.12.0 on CentOS 6.8.
The ESXi 6.0 server I used for building packer templates was a nested ESXi vm deployed using William Lam's ESXi 6.0U2 .ovf template documented [here](http://www.virtuallyghetto.com/2015/12/deploying-nested-esxi-is-even-easier-now-with-the-esxi-virtual-appliance.html).


