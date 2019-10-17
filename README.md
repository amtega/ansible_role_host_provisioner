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

<!-- A description of how to run tests of the role if available. For example: -->

Tests are based on docker containers. You can setup docker engine quickly using the playbook `files/setup.yml` available in the role [amtega.docker_engine](https://galaxy.ansible.com/amtega/docker_engine).

Once you have docker, you can run the tests with the following commands:

```shell
$ cd thisrole/tests
$ ansible-playbook main.yml
```

## License

Copyright (C) 2019 AMTEGA - Xunta de Galicia

This role is free software: you can redistribute it and/or modify it under the terms of:

GNU General Public License version 3, or (at your option) any later version; or the European Union Public License, either Version 1.2 or – as soon they will be approved by the European Commission ­subsequent versions of the EUPL.

This role is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License for more details or European Union Public License for more details.

## Author Information

- Juan Antonio Valiño García.
