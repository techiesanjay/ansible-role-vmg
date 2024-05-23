# Ansible role `vmg`  (Virtual-Machine Manifest Generator)

The **vmg** Ansible role  generates manifests for the ***libvirt_setup*** and ***cloud_init*** roles. This role is also available as part of the **techiesanjay.libvirt_infra** content collection on Ansible Galaxy.

Although manual creation of these manifests offer additional customization's they can be prone to typographic errors. This role uses a bare minimal set of variables with some Jinja2 based manipulations to create two manifests that can be used to override the role defaults for both the ***libvirt_setup*** and ***cloud_init*** roles. So, vmg just makes your life easier.

## Requirements
 - No specific requirements

## Role Variables
This role requires a set of variables along with the dictionary vmg_vms_infra to build the yaml manifests that will contain the two dictionaries **cloud_vms_info** and **libvirt**. These dictionries will be used to override the role/defaults of the respective role at a later point in time by subsequent playbooks. The ***libvirt_setup*** and the ***cloud_init*** roles are used in combination to create a VM infrastructure that can be used to support a complex set of requirements for various applications.  

### vmg_vms_infra.<VM_NAME> dictionary variables
This dictionary is used for creating VMs by supplying a set of dictionary key/value pairs that determines creation of VMs that use a certain common set of characteristics. These can be for VM naming, VM host naming, setting the VM's fqdn, cpu allocation, memory allocation and root disk size to name a few.

**Example:**
```Yaml
    vmg_vms_infra:
      'workstation':
         count: 1
         cpus: 2
         memory: 2048
         root_disk_size: 20G
         hostname_spec: ws
         alphabet: no
         snapshot: no
      'server':
         count: 2
         cpus: 2
         memory: 2048
         root_disk_size: 20G
         hostname_spec: server
         alphabet: yes
         snapshot: no
```
In the above example the dictionary ***vmg_vms_infra*** has two keys **workstation** and **server**. These two keys will determine the VM name and the key value pairs under it will determine some of the crucial VM build specifications for VM creation. In our first example, when understanding the key **workstation**, the **count** key under it is having a value 1. This will cause only one VM to be created and the VM itself will be called *workstation*. In our second example, when understanding the key **server**, the **count** key under it is having a value of 2. This will cause two VMs to be generated which will be given unique names as determined by the alphabet boolean. If the **alphabet** boolean is set to **True** then the two VMs generated will get the name *servera* and *serverb*. So, **a** and **b** are appended to the key **server** to create a unique VM names based on the value in the count. If in the above example count was 3 and boolean **alphabet** was set to **False** then the three VMs generated would get the name *server1*, *server2*, *server3*. Therefore, if alphabet is **False** then numbers are used instead of alphabets. If the count is 1 then the boolean alphabet will always be ignored.

| Variable | Comments |
| --  | -- |
| count | an integer that determines the number of VMs to be created |
| cpus | an integet that determines the cpu cores to be assigned to the VM |
| memory | Size of the memory in Mb to be assigned to a VM |
| root_disk_size | Total size of the first block device |
| hostname_spec    | a template to assigning the hostname to the VM. Will use a combination of count and alphabet boolean to determine a unique hostname for each VM.
| alphabet | Boolean value when set to False causes numbers to be appended to VM name. When set to True causes alphabets to be appended to the VM name. This boolean is also used to determine the VM hostname and a VMs fqdn |
| snapshot | Boolean when set to True cause a VM snapshot to be taken. |
| additional_nics | When defined determines additional network interface required by a VM. This is defined as a list where each element is the name of the libvirt network to use. There should not be more than 3 elements in this list. (Optional)|
| additional_disks    | When defined determines additional block devices required by the VM. additional_disks is defined as a list where each list element indicates the capacity if the block device (Optional)|



### Common Role variables for cloud_init role

|Variable|Comments  |
|--|--|
| vmg_vm_domain | Domain name used to create a VMs FQDN and for DNS requirements |
| vmg_vm_image_link | URL for a specific cloud image |
| vmg_vm_root_passwd | Password to set for the root user during the cloud-init process |
| vmg_vm_custom_cloud_user | Optional custom cloud user to be created during cloud-init process. Password access will be locked. User is sudo enabled |
| vmg_vm_ssh_authorized_keys    | List of ssh public keys installed for the root and custom cloud user created during the cloud-init process |
| vmg_vm_os_variant | Variant of the OS being installed. Run the **virt-install --os-variant list** command to see a set of supported OS variants. |
| vmg_vm_first_ip | Creates a static IP by setting the last octet start range for VMs IP address. Example, if the vmg_vm_network was 10.0.0.0 and if vmg_vm_first_ip is 10 then the first VM will get a static IP address of 10.0.0.10 and the second VM will get an IP address of 10.0.0.11 and so on |
| vmg_vm_network | Network for the VM infrastructure |
| vmg_vm_netmask | Subnet mask to be used with the static IP. Must be set if static IP is being used|
| vmg_vm_nm_controlled_network: 'yes'    | Can have values '**yes**' or '**no**'. 'Yes' is used compulsorily for RHEL8 family as they only use the NetworkManager service. For RHEL7 family you can set this to 'no'|
| vmg_vm_file_path | relative path to the cloud_init yaml manifest |
| vmg_vm_storage_pool | Name of the storage pool. Path bearing the pool name is created where VM boot disk and other block device based resources are stored. |


### Common Role variables for libvirt_setup role

|Variable|Comments  |
|--|--|
| vmg_nw_name | Name of the libvirt network to create |
| vmg_nw_bridge | Name of the network bridge to be created |
| vmg_nw_gateway | An ipv4 address to be used as a gateway to access external networks. Example **10.0.0.1** |
| vmg_nw_mask | The subnet mask to be used for the network |
| vmg_nw_dhcp_start    | An ipv4 address that specifies start range for a DHCP lease for a VM. Example **10.0.0.50**. This specific example case also assumes that you may want to use the range **10.0.0.2** to **10.0.0.49** for setting static IPs for your VMs since these are excluded from the DHCP range. |
| vmg_nw_dhcp_end | An ipv4 address that specifies end range for a DHCP lease for a VM. Example **10.0.0.254** |
| vmg_nw_file_path | relative path to the libvirt_setup yaml manifest |


#### Example. 
Additional example can be found in the tests folder

## Dependencies
No role dependencies exist. 

## License
BSD

## Author
**Sanjay Singh**
sanjay.kr.singh@gmail.com
> Written with [StackEdit](https://stackedit.io/).


