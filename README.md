# Custom Zabbix Templates
## Aruba ClearPass Policy Manager
* LLD for Apps/Protocols
  * Throughput for Apps/Protocols (db,RADIUS,tacacs,etc.)
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
* System Serial
* System Version
* Time taken for all policies
* Number of evaluations performed
* RADIUS specific metrics

In order for many of the triggers to work there are several macros that need to be set per host. This decision was made to be able to further tailor to each box since the load may not be the same across all subscribers or pulisher.
Host Specific Macros:

{$AUTHZ_TIME} #Time in ms for authorizations to complete for each source

{$HIGH_FAILED_AUTHZ} #Number of failed authorization requests

{$POLICY_EVAL_TIME} #Time in ms for a policy evaluation to complete for each source

{$RAD_TIME} #Time in ms for RADIUS to complete for each source

{$HIGH_FAILED_RAD} #Number of failed RADIUS requests

{$SYS_TOTAL_FAILED} #Number of failed system wide requests

NOTE:
Template SNMP OS Linux UCD-SNMP-MIB.xml is optional as it provides some additional memory and CPU info

```
Template/Supporting Files:
https://github.com/samwiseg00/zabbix/tree/master/zabbix-template/hpe-aruba/cppm
```

## Ubound DNS
* Histogram as queries per second
* Histogram as total queries
* AAAA queries	 	
* AD flag	 	
*	ANY queries	 	
*	A queries	 	
*	Cache hits	 	
*	CD flag	 	
*	CHAOS class	 	
*	CNAME queries	 	
*	DNSSEC OK	 	
*	EDNS OPT present
* IN class	 		
*	IPv6 queries	 		
*	Iterator module memory	 	
*	Message cache memory	 	
*	MX queries	 	
*	nodata rcode	 	
*	NOERROR rcode	 	
*	NS queries	 	
*	NXDOMAIN rcode	 		
*	PTR queries	 	
*	QR flag	 		
*	RA flag
*	RD flag	 	
*	RRset cache memory	 	
*	SERVFAIL rcode	 
* Service	status
*	SOA queries	 
*	SPF queries	 
*	SRV queries
*	TC flag	 		
*	TCP queries	 	
*	Total memory	 	
*	Total queries	 	
*	TXT queries	 	
*	Uptime	 	
*	Validator module and key cache memory
*	Z flag	 	

```
Template/Supporting Files:
https://github.com/samwiseg00/zabbix/tree/master/zabbix-template/unbound
```

## Aruba OS WLC 6.5+
* LLD for Storage
* LLD for Fans
* LLD for PSU
* LLD for Licences
* LLD for CPU
* LLD for Auth Servers
* Uptime
* Reboot Cause
* Linked Template for Interfaces
* Linked Template for ICMP
* ConfigID
* Base MAC
* License Count
* Memory
* Model
* Hostname
* Hardware Version
* Serial
* Switch Role
* Temperature
* Uptime
* IP Address
* IPv6 Address (Requires 8.x)
* Total APs
* Total Associations
* Total Users

NOTE: For AP and User calculations to work properly the following Macros need to be added (This should be the total number of items supported by the platform):

{$WLC_TOTAL_AP} ie. 16 for 7005, 1024 for 7220

{$WLC_TOTAL_USER} ie. 1024 for 7005, 24576 for 7220

```
Template/Supporting Files:
https://github.com/samwiseg00/zabbix/tree/master/zabbix-template/hpe-aruba/wifi
```

## APC UPS (Updated)
Updated version of https://share.zabbix.com/power-ups/apc/apc-ups     
Temperature, battery status, output load and more.

```
Template/Supporting Files:
https://github.com/samwiseg00/zabbix/tree/master/zabbix-template/apc_ups
```

## HP Procurve Aruba 3810M
Basic Monitoring including:
* Dynamic Interface Discovery
* Dynamic Inventory Discovery
* CPU, Mem, Temp, Fans

```
Template/Supporting Files:
https://github.com/samwiseg00/zabbix/tree/master/zabbix-template/hpe-aruba/switches
```

## Synology DSM 5+ (Updated)
Cleaned Up template that is originally from https://share.zabbix.com/storage-devices/synology/synology-dsm-5

Basic Monitoring including:
* CPU load average
* Template for each CPU discovery
* Template for Interface discovery
* Trigger for Updates
* Template for Disk Discovery

```
Template/Supporting Files:
https://github.com/samwiseg00/zabbix/tree/master/zabbix-template/synology
```
