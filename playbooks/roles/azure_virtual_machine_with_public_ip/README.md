azure_virtual_machine_with_public_ip
==================

A role to Create/Delete/Configure an Azure Virtual Machine.

Requirements
------------

* Azure User Account with valid permission

Role Variables
--------------

* **azure_virtual_machine_with_public_ip_operation**: Operation to perform. Valid values are 'create', 'delete', 'power_on', 'power_off', 'deallocate', 'restart'. Default is 'create'.
* **azure_virtual_machine_with_public_ip_remove_on_absent**: Specify which resources to remove when `operation='delete'`. 'all' removes all resources attached to the VM being removed; 'all_autocreated' removes the resources that were automatically created while provisioning the VM (public ip, network interface, security group). To remove only specific resources, use the values 'network_interfaces', 'virtual_storage', or 'public_ips'. The default value is 'all'.
* **azure_virtual_machine_with_public_ip_resource_group**: Resource group on/from which the virtual machine will reside. When `operation='create'`, this resource group will be created if it does not exist.
* **azure_virtual_machine_with_public_ip_region**: An Azure location for the resources.
* **azure_virtual_machine_with_public_ip_tags**: Dictionary of string:string pairs to assign as metadata to the resource group.
* **azure_virtual_machine_with_public_ip_vm**: Object used to provide details for a virtual machine. Contains the following:
  - **name**: (Required) Name of the virtual machine.
  - **admin_username**: Administrator's login name of a server. Required for creation.
  - **admin_password**: Password of the administrator login. Not required when `os='Linux'` and `ssh_pw_enabled='false'`.
  - **size**: Valid Azure VM size. Choices vary depending on the subscription and location.
  - **network_interfaces**: List of network interfaces to add to the VM. Can be a string of name or resource ID of the network interface, can also be a dict containing 'resource_group' and 'name' of the network interface. A default network interface will be created if not provided.
  - **os**: Type of Operating System. Default is 'Linux'
  - **availability_set**: Name or ID of existing availability set to add the VM to.
  - **image**: The image used to build the VM. For custom images, the name of the image. To narrow the search to a specific resource group, a dict with the keys name and resource_group. For Marketplace images, a dict with the keys publisher, offer, sku, and version. Set version=latest to get the most recent version of a given image.
  - **ssh_pw_enabled**: Enable/disable SSH passwords. Valid values are 'yes', 'no'. Default value is 'yes'.
  - **ssh_public_keys**: List of SSH keys when `os='Linux'`. Accepts a list of dicts where each dictionary contains two keys, 'path' and 'key_data'. Set path to the default location of the authorized_keys files. For example, path=/home/<admin username>/.ssh/authorized_keys. Set key_data to the actual value of the public key.
  - **data_disks**: List of data disks.
    - **lun**: Logical unit number for data disk. Must be unique for each data disk attached to a VM.
    - **caching**: Type of data disk caching. Options are 'ReadOnly' (default) or 'ReadWrite'.
    - **disk_size_gb**: Initial disk size in GB for blank data disks. Cannot be larger than 1023 GB. Can only be modified when VM is deallocated.
    - **managed_disk_type**: Managed data disk type.
    - **storage_account_name**: Name of an existing storage account that supports creation of VHD blobs.
    - **storage_blob_name**: Name of storage blob used to hold the OS disk image of the VM.
    - **storage_container_name**: Name of the container to use within the storage account to store VHD blobs. Default is 'vhds'
* **azure_virtual_machine_with_public_ip_availability_set**: Object used to provide details for an availability set to be used by a VM. Contains the following:
  - **name**: (Required) Name of the availability set.
  - **platform_fault_domain_count**: Fault domains define the group of virtual machines that share a common power source and network switch. Should be between 1 and 3.
  - **platform_update_domain_count**: Update domains indicate groups of virtual machines and underlying physical hardware that can be rebooted at the same time.

Limitations
------------

- NA

Dependencies
------------

- NA

Example Playbook
----------------

    - hosts: localhost
      roles:
        - name: Create a load balanced virtual machine with a default network interface
          role: cloud.azure_ops.azure_virtual_machine_with_public_ip
          azure_virtual_machine_with_public_ip_operation: 'create'
          azure_virtual_machine_with_public_ip_region: eastus
          azure_virtual_machine_with_public_ip_resource_group: 'my_resource_group'
          azure_virtual_machine_with_public_ip_vm:
            name: "example-vm"
            admin_username: 'azureuser'
            admin_password: 'adminpass'
            image:
              offer: RHEL
              publisher: RedHat
              sku: 7-LVM
              version: latest
            size: Standard_DS1_v2
            load_balancer_backend_address_pools:
              - name: 'default'
                load_balancer: 'existing-lb'

        - name: Delete virtual machine and all autocreated resources
          role: cloud.azure_ops.azure_virtual_machine_with_public_ip
          azure_virtual_machine_with_public_ip_operation: 'delete'
          azure_virtual_machine_with_public_ip_remove_on_absent: 'all_autocreated'
          azure_virtual_machine_with_public_ip_vm:
            name: "example-vm"

License
-------

GNU General Public License v3.0 or later

See [LICENCE](https://github.com/redhat-cop/cloud.azure_ops/blob/main/LICENSE) to see the full text.

Author Information
------------------

- Ansible Cloud Content Team