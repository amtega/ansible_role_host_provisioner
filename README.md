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

Tests are based on [molecule with docker containers](https://molecule.readthedocs.io/en/latest/installation.html).

Due to the complexity of the role and the set of involved services, currently it's only posible to run tests aginst a real configured platform as described above. You must use a host with all services and point the environment variable `HOST_PROVISIONER_TEST_HOST` to this host.

You must point variable `HOST_PROVISIONER_TEST_VMS` to json list with the hostnames of the virtual machines to provisione. This testing virtual machines must be configured in an inventory with the structure required by the `amtega.vmware_provisioner` and each host must have their network config defined in the inventory according to `amtega.network_interfaces` role.

To provide the inventory with the testing virtual machines and the required role variables you must fill also these environment variables:

- `ANSIBLE_INVENTORY`: path to an inventory
- `ANSIBLE_VAULT_PASSWORD_FILE`: path to the file containing the vault password required for the previous inventory

```shell
cd amtega.host_provisioner

HOST_PROVISIONER_TEST_HOST=myhost HOST_PROVISIONER_TEST_VMS="['testingvm1', 'testingvm2']" ANSIBLE_INVENTORY=~/myinventory ANSIBLE_VAULT_PASSWORD_FILE=~/myvaultpassword molecule test --all
```

## License

Copyright (C) 2020 AMTEGA - Xunta de Galicia

This role is free software: you can redistribute it and/or modify it under the terms of:

GNU General Public License version 3, or (at your option) any later version; or the European Union Public License, either Version 1.2 or – as soon they will be approved by the European Commission ­subsequent versions of the EUPL.

This role is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details or European Union Public License for more details.

## Author Information

- Juan Antonio Valiño García.
