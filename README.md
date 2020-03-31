# Ansible host_provisioner role

This is an [Ansible](http://www.ansible.com) role that provisione hosts orchestrating several roles and backends.

Currently only VMware backend is supported.

## Requirements

1. A dhcpd host configured with [amtega.dhcpd](https://galaxy.ansible.com/amtega/dhcpd) role. Within this host you need a network for provisioning, which name you must pass to the `host_provisioner_dhcpd_prov_net_name` variable (see `defaults/main.yml` for details)

2. A cobbler host configured with [amtega.cobbler](https://galaxy.ansible.com/amtega/cobbler) role. Within this host you must configure all distros and profiles you need to install new hosts

3. Server with access to the backends platforms supported (e.g VMWARE vCenter/ESX). You must configure in your inventory all connection variables required by the backends (e.g [amtega.vmware_provisioner](https://galaxy.ansible.com/amtega/vmware_provisioner))

4. All the hosts to provisione must have their network config defined in the inventory according to `amtega.network_interfaces` role.

Point 1 to 3 of the previous requirements can be fulfilled with multiple machines or in a single host.

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

Due to the complexity of the role and the set of involved services, currently it's only posible to run tests aginst a real configured platform as described above. You can use a single host with all services and pass the variable `host_provisioner_testing_single_host` to the tests or, alternatively, use different servers for each service passing the following variables:

- `host_provisioner_testing_backends_host`: server with access to the backends platforms supported (see above for details)
- `host_provisioner_testing_dhcpd_host`: server with dhcpd service (see above for details)
- `host_provisioner_testing_cobbler_host`: server with cobbler service (see above for details)

Also, you need to the following variables defined in the inventory:

- `host_provisioner_testing_vms`: list of dicts with the vms for testing. The structure is the same as the one required by the `amtega.vmware_provisioner` role.
- Each host in `host_provisioner_testing_vms` must have their network config defined in the inventory according to `amtega.network_interfaces` role.

For example, you can run the tests with the following commands:

```shell
$ cd host_provisioner/tests
$ ansible-playbook main.yml -e single_host=my_single_host
```

## License

Copyright (C) 2020 AMTEGA - Xunta de Galicia

This role is free software: you can redistribute it and/or modify it under the terms of:

GNU General Public License version 3, or (at your option) any later version; or the European Union Public License, either Version 1.2 or – as soon they will be approved by the European Commission ­subsequent versions of the EUPL.

This role is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details or European Union Public License for more details.

## Author Information

- Juan Antonio Valiño García.
