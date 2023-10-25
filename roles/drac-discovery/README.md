# drac-discovery 

## Summary

This role connects to a Dell DRAC via dell|emc openmanage module and collects system information about the target server. 
The intention of this role is to use that collected information to identify the primary network boot device and extract it's MAC Address.
It will then template out a file called bare-metal.yaml used in the installation of OpenShift from this gathered information.

This role will:

* Connect to a Dell DRAC using the IP, username and password
* Collect system information about the target server
* Identify the boot device
* Extract the MAC Address associated with the boot device
* Some of these variables can be specified as hints to reduce the discovery time
* Template a yaml file with the discovered information

## Role Variables

The following variables must be set on each invocation. Any listed defaults are also in `defaults/main.yml`

| Variable | Type | Description | Example Value
| --- | --- | --- | ---
`drac_discovery_hostname` | string | Value used for the hosts's cluster name | {{ inventory_hostname }}
`drac_discovery_role` | string | The OpenShift role to assign to this server | master, worker
`drac_discovery_clustername` | string | The OpenShift cluster name | example
`drac_discovery_ipaddress` | string | IP Address of the DRAC device | 192.168.0.1
`drac_discovery_validate_certs` | bool | Whether to validate certs| true, false
`drac_discovery_credentials` | string | Credential name | example-drac-credentials
`drac_discovery_config_output` | string | Directory to store generated config files | "{{ playbook_dir }}/configs/"
`drac_discovery_boot_device` | string | FQDD name of the device to use for primary network booting | NIC.Integrated.1-2-1
`drac_discovery_macaddress` | string | MAC Address of the boot device | FE:ED:DE:AD:BE:EF

## Notes

* If `drac_discovery_boot_device` or `drac_discovery_macaddress` is not specified, the DRAC will be connected to and data discovered from it
* If `drac_discovery_boot_device` is specified but not `drac_discovery_macaddress`, the DRAC will be connected to and the corresponding MAC Adress matching the `drac_discovery_boot_device` will be obtained
* If `drac_discovery_boot_device` and `drac_discovery_macaddress` are both specified, then there is no reason to connect to the DRAC. In this case, the known MAC Address is simply templated into the yaml file, without discovery.