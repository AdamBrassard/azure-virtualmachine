Create/Update VM in Azure
=========

[![Build Status](https://travis-ci.org/AdamBrassard/azure-virtualmachine.svg?branch=master)](https://travis-ci.org/AdamBrassard/azure-virtualmachine)

This Role creates/updates VM(s) in Azure

<https://docs.ansible.com/ansible/latest/modules/azure_rm_virtualmachine_module.html>

Requirements
------------

Requires Azure_rm Module
ansible >=2.8

User Defined Role Variables
--------------

``` yaml
#Default required variables:
resource_group:
location:

# Vm information
vm:
  - name: # unique vm name
    size: # Example: "Standard_D4s_v3" # Defining the size of the VM to be created
    offer: # Example: "RHEL" # defining the specific flavour of OS and company that supports it
    publisher: # Example: "RedHat" # Company name that published the image
    sku_name: # Example: "7-RAW" # Main version of the OS to be used
    sku_version: # Example: "latest" # Specific version of OS
    os_type: # Example: Windows, Linux
    managed_disk_type: # Type of storage for the managed disk: Standard_LRS or Premium_LRS. If not specified the disk is created Standard_LRS
    os_disk_caching: #Type of OS disk caching. Set to 'ReadOnly' on default, can also be "ReadWrite". Does not need to be define if default is wished.
    os_disk_size_gb: # The size of the disk with the OS on it, windows 2012 requires minumum 127 and will not allow override to lower  Does not need to be define if default is wished.
    vm_username: # Example: "azureadmin"
    vm_password:  #Define a password
    avset_name: #Define an availability set if desired. else this will be ignored
    nic_name: #enter the name of an existing NIC


```

Dependencies
------------

If you don't define the network_interface name it will create one as long as there is a network in the resource group.
You can define the name of the NIC if it exists prior

Example Playbook
----------------

> Creating Multiple VM in one playbook

``` yaml
---
- name: Azure Playbook creating multiple vm's
  hosts: localhost

  pre_tasks:
    - name: Creating multiple Vm's in 1 resource group
      set_fact:
        #Default required vars
        resource_group: "MyRG"
        location: "canadacentral"

        #VM var structure for multiple VM input
        vm:
          - name: NewVM
            size: "Standard_D4s_v3"
            offer: "RHEL"
            publisher: "RedHat"
            sku_name: "7-RAW"
            sku_version: "latest"
            os_type: Linux
            managed_disk_type: Standard_LRS
            os_disk_size_gb: 150
            vm_username: "TempAdmin"
            vm_password:  "YouShouldntUseClearTextPasswords@!"
            avset_name: "myavset4"

          - name: SecondGreatVm
            size: "Standard_D4s_v3"
            offer: "RHEL"
            publisher: "RedHat"
            sku_name: "7-RAW"
            sku_version: "latest"
            os_type: Linux
            managed_disk_type: Premium_LRS
            os_disk_size_gb: 200
            vm_username: "TempAdmin"
            vm_password:  "YouShouldntUseClearTextPasswords@!"
            avset_name: "myavset4"

          - name: MyThirdVM
            size: "Standard_DS2_v2"
            offer: "WindowsServer"
            publisher: "MicrosoftWindowsServer"
            sku_name: "2019-Datacenter"
            sku_version: "latest"
            os_type: "Windows"
            managed_disk_type: StandardSSD_LRS
            os_disk_size_gb: 200
            vm_username: "TempAdmin"
            vm_password:  "YouShouldntUseClearTextPasswords@!"
            avset_name: "windowsAVset"

  connection: local
  roles:
    - azure-virtualmachine
```

> Creating a VM using a preexisting NIC

``` yaml
---
- name: Azure Playbook creating multiple vm's
  hosts: localhost

  pre_tasks:
    - name: Creating a Vm using an existing NIC and specifying os disk caching type
      set_fact:
        #Default required vars
        resource_group: "MyRG"
        location: "canadacentral"

        #VM var structure for VM input
        vm:
          - name: NewVM
            size: "Standard_D4s_v3"
            offer: "RHEL"
            publisher: "RedHat"
            sku_name: "7-RAW"
            sku_version: "latest"
            os_type: Linux
            os_disk_caching: "ReadOnly"
            managed_disk_type: Standard_LRS
            os_disk_size_gb: 150
            vm_username: "TempAdmin"
            vm_password:  "YouShouldntUseClearTextPasswords@!"
            avset_name: "myavset4"
            nic_name: "oldnic2019"
  connection: local
  roles:
    - azure-virtualmachine
```  

License
-------

MIT

Author Information
------------------

Adam Brassard: Abrass on IRC
