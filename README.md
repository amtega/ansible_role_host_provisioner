# Ansible host_provisioner role

This is an [Ansible](http://www.ansible.com) role that provisione hosts orchestrating several roles and backends.

Currently only VMware backend is supported.

The provisioning flow is the following:

1. Using a provisioning backend, create a host (typically a virtual machine) with a single network interface within a provisioning network
2. Setup a host in the dhcpd server for the host to provisione
3. Setup a system in cobbler to install the host to provisione
4. Power on the host to provisione
5. The host will get a temporary network address form dhcpd server
6. The host to provisione will be installed using cobbler6.
7. Apply to the provisioned host a set of mandatory roles (see `host_provisioner_roles_mandatory` variable in `vars/main.yml`)
8. Using a backend, configure the host with the final network interfaces.
9. Poweroff the host to provisione
10. Poweron the host to provisione

## Requirements

1. A dhcpd host configured with [amtega.dhcpd](https://galaxy.ansible.com/amtega/dhcpd) role. Within this host you need a network for provisioning, which name you must pass to the `host_provisioner_dhcpd_prov_net_name` variable (see `defaults/main.yml` for details)

2. A cobbler host configured with [amtega.cobbler](https://galaxy.ansible.com/amtega/cobbler) role. Within this host you must configure all distros and profiles you need to install new hosts

3. Server with access to the backends platforms supported (e.g VMWARE vCenter/ESX). You must configure in your inventory all connection variables required by the backends (e.g [amtega.vmware_provisioner](https://galaxy.ansible.com/amtega/vmware_provisioner))

All the previous requirements can be fulfilled with multiple machines or in a single host.

## Role Variables

A list of all the default variables for this role is available in `defaults/main.yml`.

The role also setups the following facts:

- `host_provisioner_vmware_vms_filtered`: dict indicating the vmware backend hosts to provisione and the ones considered stalled
- `host_provisioner_vmware_macs`: dict mapping hostnames with the mac provided by VMware backend
- `host_provisioner_vmware_nics`: list of dicts with the nics vmware backend will configure
- `host_provisioner_vmware_dhcpd_hosts`: list of dicts with the hosts vmware backend will pass to the dhcpd server
- `host_provisioner_vmware_cobbler_systems`: list of dicts with the systems vmware backend will pass to the cobbler server

## Usage

To use the role you must setup a playbook that calls the role multiple times against different hosts and provisioning stages. See variable `host_provisioner_stage` in `defaults/main.yml` for details about the involved hosts and stages. Also, you can find a playbook sample in `files/site.yml`

## Testing

Due to the complexity of the role and the set of involved services, currently it's only posible to run tests aginst a real configured platform as described above. You can use a single host with all services and pass the variable `single_host` to the tests or, alternatively, use different servers for each service passing the following variables:

- `backends_host`: server with access to the backends platforms supported (see above for details)
- `dhcpd_host`: server with dhcpd service (see above for details)
- `cobbler_host`: server with cobbler service (see above for details)

Also, you need to pass the following variables with the testing virtual machine config:

- `host_provisioner_testing_vm_name`: hostname (default is `host-provisioner-testing-vm`
- `host_provisioner_testing_vm_folder`: vmware folder (default is "/")
- `host_provisioner_testing_vm_annotation`: vm annotation (default is "Ansible host_provisioner role testing machine")
- `host_provisioner_testing_vm_datacenter`: vmware datacenter
- `host_provisioner_testing_vm_cluster`: vmware cluster
- `host_provisioner_testing_vm_guest_id`: vmware guest id
- `host_provisioner_testing_vm_disk_size`: vm disk size (default is 30)
- `host_provisioner_testing_vm_disk_type`: vm disk type (default is thin)
- `host_provisioner_testing_vm_datastore`: vmware datastore
- `host_provisioner_testing_vm_memory_mb`: vm memory (default is 2048)
- `host_provisioner_testing_vm_num_cpus`: vm cpus number (default is 1)
- `host_provisioner_testing_vm_num_cpu_cores_per_socket`: vm number cores per socket (default is 1)
- `host_provisioner_testing_vm_net_name`: vm network device name
- `host_provisioner_testing_vm_net_type`: vm network device type (default is "vmxnet3")
- `host_provisioner_testing_vm_net_gateway`: vm network device gateway
- `host_provisioner_testing_vm_net_logicalname`: vm network device logical name (default is "eth0")
- `host_provisioner_testing_vm_net_address`: vm network device ip address
- `host_provisioner_testing_vm_net_cidr`: vm network device netmask in cidr format

For example, you can run the tests with the following commands:

```shell
$ cd host_provisioner/tests
$ ansible-playbook main.yml -e single_host=my_single_host
```

## License

Copyright (C) 2019 AMTEGA - Xunta de Galicia

This role is free software: you can redistribute it and/or modify it under the terms of:

GNU General Public License version 3, or (at your option) any later version; or the European Union Public License, either Version 1.2 or – as soon they will be approved by the European Commission ­subsequent versions of the EUPL.

This role is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details or European Union Public License for more details.

## Author Information

- Juan Antonio Valiño García.
