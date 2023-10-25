# ansible-openshift-baremetal

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
`baremetal_role` | string | The OpenShift role to assign to this server | master, worker
`baremetal_clustername` | string | The OpenShift cluster name | example
`baremetal_ipaddress` | string | IP Address of the DRAC device | 192.168.0.1
`baremetal_validate_certs` | bool | Whether to validate certs| true, false
`baremetal_credentials` | string | Credential name | example-drac-credentials
`baremetal_config_output` | string | Directory to store generated config files | "{{ playbook_dir }}/configs/"
`baremetal_boot_device` | string | name of the device to use for primary network booting | NIC.Integrated.1-2-1
`baremetal_macaddress` | string | MAC Address of the boot device | FE:ED:DE:AD:BE:EF

## Notes

* If `baremetal_boot_device` or `baremetal_macaddress` is not specified, the DRAC will be connected to and data discovered from it
* If `baremetal_boot_device` is specified but not `baremetal_macaddress`, the DRAC will be connected to and the corresponding MAC Adress matching the `baremetal_boot_device` will be obtained
* If `baremetal_boot_device` and `baremetal_macaddress` are both specified, then there is no reason to connect to the DRAC. In this case, the known MAC Address is simply templated into the yaml file, without discovery.