# Custom Zabbix Templates

The templates are Zabbix 7.0 exports in YAML. Zabbix 7.0 and later can import them. Import each
file under **Data collection > Templates > Import**.

The old XML templates for Zabbix 3.2 to 5.0 are kept at the
[`legacy-xml`](https://github.com/samwiseg0/zabbix/tree/legacy-xml) tag. They are unmaintained.

## ArubaOS controllers

`zabbix-template/hpe-aruba/wifi/Template ArubaOS.yaml` monitors ArubaOS 8 mobility controllers and
conductors over SNMP.

* LLD for Access Points, Auth Servers, CPU, Fans, Licenses, PSU and Storage
* Uptime and reboot reason
* Config ID, model, hostname, hardware and software version, serial, switch role
* IPv4 and IPv6 address
* Temperature and memory
* Total APs, associations and users, and the AP and user share of the platform limit

Set these macros on each controller host:

* `{$SNMP_COMMUNITY}` is the SNMP community.
* `{$WLC_TOTAL_AP}` is the AP limit of the platform, for example 16 for a 7005 or 1024 for a 7220.
* `{$WLC_TOTAL_USER}` is the user limit of the platform, for example 1024 for a 7005 or 24576 for a
  7220.

`show license limits` on the controller prints both limits. Most thresholds are template
macros that start with `{$AOS.`. Override them on a host to tune one controller.

The template polls the controller. Point the host at the controller's IP, never at an AP.

The template links no other templates. Link Zabbix's `ICMP Ping` template and an SNMP interface
template to the controller hosts yourself.

An AP reboot raises one problem, "is DOWN", on the AP's active controller. The standby
controller's "Uptime has changed" and "Bootstrap number has changed" stay quiet for 10 minutes
after it sees the AP down. A bootstrap change alerts only when the AP did not reboot.

### ArubaOS cluster (optional)

`zabbix-template/hpe-aruba/wifi/Template ArubaOS cluster.yaml` watches all controllers of one
cluster together. It alerts on a config ID mismatch across the cluster, on a controller that stops
reporting, and on a recent controller restart. The config ID mismatch catches a managed device that
lost its tunnel to the conductor and stays on an old config.

1. Import `Template ArubaOS.yaml` first. The cluster template reads its items.
2. Put the controllers in one host group.
3. Create a host with no interface and link `Template ArubaOS cluster`.
4. Set `{$AOS.CLUSTER.GROUP}` to the host group name and `{$AOS.CLUSTER.MEMBERS}` to the number of
   controllers.

A controller restart makes the APs and auth servers on its peers flap. To keep those alerts quiet
during a restart, add a dependency on the cluster host's trigger "An Aruba controller restarted
recently" to these trigger prototypes in `Template ArubaOS`:

* `{#APNAME} Uptime has changed`
* `{#APNAME} Bootstrap number has changed`
* `Server {#SNMPVALUE} Uptime Changed on {HOST.NAME}`

The dependency names the cluster host. Renaming that host breaks a later re-import of
`Template ArubaOS`.

## Aruba ClearPass Policy Manager

`zabbix-template/hpe-aruba/cppm/Template Aruba ClearPass Policy Manager.yaml`

* LLD for Apps/Protocols
  * Throughput for Apps/Protocols (db, RADIUS, TACACS, etc.)
  * Listening port for Apps/Protocols
* LLD for Policy server authz table
* LLD for Protocol policy evaluation
* LLD for Radius server auth table
* LLD for System Services
* Uptime
* Cluster size
* Memory
* Number of CPUs
* System IPs
* System Zone
* System Role
* System MAC
* System Type
* System Version
* Time taken for all policies
* Number of evaluations performed
* RADIUS specific metrics

The triggers read these template macros. The load differs between a publisher and its subscribers,
so override them on each host as needed.

* `{$AUTHZ_TIME}`, default 200, alerts when the 3 minute average authorization time for one source
  reaches this many ms.
* `{$HIGH_FAILED_AUTHZ}`, default 10, alerts when failed authorizations for one source grow by this
  many between two polls.
* `{$POLICY_EVAL_TIME}`, default 300, alerts when the 3 minute average policy evaluation time for
  one protocol reaches this many ms.
* `{$RAD_TIME}`, default 300, alerts when the 3 minute average RADIUS authentication time for one
  source reaches this many ms.
* `{$HIGH_FAILED_RAD}`, default 10, alerts when failed RADIUS authentications for one source grow
  by this many between two polls.
* `{$SYS_TOTAL_FAILED}`, default 10, alerts when system wide failed authentications grow by this
  many between two polls.

Link Zabbix's `Linux by SNMP` template as well for CPU, memory and disk.

### ClearPass certificates and licenses over HTTP

`zabbix-template/hpe-aruba/cppm/Aruba ClearPass by HTTP.yaml` reads the ClearPass REST API.

* LLD for the enabled server certificates of each node (RADIUS, HTTPS, RadSec, Database)
* Certificate expiry, with a warning at 30 days and a high problem at 7 days
* Certificate validity
* LLD for application licenses, with a warning above 90% usage

One script item logs in and fetches everything in one run, once an hour. The template stores no
token. Link it to every ClearPass node, next to the SNMP template. License counts are cluster-wide,
so each node reports the same license values.

It needs a ClearPass API client with the client_credentials grant. An operator profile with only the
privileges `apigility`, `#cppm_certificates` and `#cppm_licenses` is enough. That profile was tested
on ClearPass 6.12. It reads both endpoints and gets 403 everywhere else.

Set `{$CPPM.API.CLIENT}` and `{$CPPM.API.SECRET}` on each host. The API defaults to
`https://{HOST.CONN}`. Set `{$CPPM.API.URL}` to reach it another way. Zabbix 7.0 does not verify the
ClearPass TLS certificate in script items, so reach the API over a trusted network.

The checks come from the ClearPass by HTTP template by [argusb](https://github.com/argusb/zabbix).

## HP ProCurve Aruba 3810M

`zabbix-template/hpe-aruba/switches/Template Aruba 3810M.yaml`

* LLD for switch inventory
* CPU, memory, temperature and fans
* Model, serial, ROM and OS version
* Uptime

Link Zabbix's `ICMP Ping` template and an SNMP interface template for ping and interface discovery.

## Retired templates

The APC UPS, Synology DSM, Unbound and UCD-SNMP-MIB templates are no longer maintained. They
remain at the [`legacy-xml`](https://github.com/samwiseg0/zabbix/tree/legacy-xml) tag. Zabbix 7.0
ships `APC UPS by SNMP` and `Linux by SNMP`.
