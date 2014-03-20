heartbeat
=========

Sets up the groundwork for a Highly Available service based on DRBD and Heartbeat.

### NOTE:
This role is not standalone, it is designed as a dependency on any upper layer service that requires High Availability.

Requirements
------------

The target machines for this role (exactly 2) must appear on the inventory file grouped under one and only one host group, the `group_names` variable is used to configure HA resources.

This role partitions block devices to be used as distributed RAID devices, make sure to define the correct block device descriptors via the `block_device` role variable. This and other prerequisites are defined by the role variables.

Role Variables
--------------

- `configure_resources` - Whether to upload this role's template for `/etc/heartbeat/haresources` (`True`) or leave unconfigured for a client role to fill in (`False`). Defaults to `True`.
- `hb_auth` - A random string of characters for heartbeat node authentication. While it has a default value, it is expected to be overriden on a HA cluster basis.
- `hb_ipaddr_suffix` - If the target environment is **not** OpenStack HA handover is handled by the IPaddr script, this variable holds the CIDR netmask and interface parameters; it defaults to `/24/eth0` and it can be overriden on the mysql and nfs roles or on the global scope.
- `OS_PROJECT` - OpenStack project (tenant) name. Undefined, required only if the target environment is deployed on OpenStack.
- `OS_USERNAME` - OpenStack user name. Undefined, required only if the target environment is deployed on OpenStack.
- `OS_PASSWORD` - OpenStack password for the above user. Undefined, required only if the target environment is deployed on OpenStack.
- `ALLOWED_IPS` - **List** of IPs of the machines allowed to access HA services. Defined here as they are usually common to all HA services in a given deployment; it can be overriden on client roles.
- `OS_FLOATIP` - Floating (Virtual) IP assigned to the HA cluster. Expected to be declared on dependent roles or overriden on the global scope.
- `heartbeat_service_name` - Name of the service to run on top of the Heartbeat/DRBD infrastructure. Expected to be declared on dependent roles or overriden on the global scope.
- `IS_TARGET_OPENSTACK` - Boolean defining whether the target environment is deployed on OpenStack. Expected to be declared on the global scope.
- `primary` - Flag that indicates the target host in which to run tasks that only need to be executed in one node. This is expected to be an inventory variable within the host group.

Dependencies
------------

Variables read from other roles:
- `IS_TARGET_OPENSTACK`
- `OS_FLOATIP`
- `hb_ipaddr_suffix`
- `heartbeat_service_name`

Example Playbook
-------------------------

To use this role from a playbook:

```
    - hosts: servers
      roles:
         - { role: heartbeat }
```

To use this role from another role that overrides heartbeat resource configuration, on `meta/main.yml`:

```
---
dependencies:
  - { role: heartbeat, configure_resources: False }
```

Author Information
------------------

[Forge Servicelab Team](http://forgeservicelab.fi)