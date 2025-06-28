# Cisco Switch Basic Configuration
## This configuration is for a Cisco Layer 2 switch

### 🔐 Security & User Management
```shell
enable secret cisco
username admin privilege 15 password cisco
service password-encryption
```
* Defines the enable secret and local admin user with encrypted password.
* service password-encryption ensures plain-text passwords are obfuscated.
### 🔑 AAA (Authentication, Authorization, Accounting)
```shell
aaa new-model
aaa local authentication attempts max-fail 3
aaa authentication login default local
```
* Enables AAA model.
* Limits failed login attempts to 3.
* Uses local authentication for user logins.

### 🕒 Timestamps and Logging
```shell
service timestamps debug datetime msec
service timestamps log datetime msec
no logging console
no logging monitor
```
* Adds detailed timestamping to logs for better troubleshooting.
* Disables console and monitor logging to avoid log spamming during terminal sessions.

### 🌍 Network Time and Domain Settings
```shell
clock timezone GMT 5 30
ip domain name cisco.local
ntp server 2.2.2.2
```
* Sets timezone to GMT+5:30 (Sri Lanka Standard Time).
* Defines local domain name.
* Configures NTP server for time synchronization.
### 🌐 IP Settings & Management VLAN
```shell
ip default-gateway 192.168.100.254
interface Vlan100
ip address 192.168.100.1 255.255.255.0
name MGT-VLAN
```
* Defines default gateway for L2 switch management.
* Assigns an IP address to VLAN100, designated as the Management VLAN.
### 🚫 Disable Unused Services
```shell
no ip http server
no ip http secure-server
```
* Disables both HTTP and HTTPS servers on the switch to reduce attack surface.
### 🔐 SSH Configuration
```shell
ip ssh version 2
```
* Enables SSH version 2 for secure remote access.
### 🔌 Interface Configurations
* Port-channel (LACP Trunk)
```shell
interface Port-channel1
description Uplink to DIST VSS Pair
switchport trunk native vlan 100
switchport trunk allowed vlan 10,14,100
switchport mode trunk
spanning-tree portfast trunk
``` 
* Creates a trunk port-channel (LACP) to connect to core/distribution layer.
### Access Port
```shell
interface GigabitEthernet1/0/1
description USER-ACCESS
switchport access vlan 10
switchport mode access
spanning-tree portfast
spanning-tree bpduguard enable
```
* Configures access port user devices in VLAN 10 with BPDU Guard enabled for security.
### Uplink Interfaces (10G)
```shell
interface TenGigabitEthernet1/1/1
interface TenGigabitEthernet1/1/2
channel-group 1 mode active
```
* Both interfaces are aggregated into Port-channel1 using LACP.
* Used for uplinks to the distribution/core switches.

### 📡 SNMP & Logging Configuration
```shell
logging host 192.168.100.1
logging host 192.168.100.2
logging snmp-trap informational
snmp-server group cisco v3 priv
snmp-server community cisco RO
```
* Configures remote log servers and SNMP traps for various severity levels.
* Adds SNMPv3 group with privacy (priv) and SNMP community for RO access.
### 🧾 Login Banner
```shell
banner login ^C
...
^C
```
* Displays a legal banner warning unauthorized users that activity is monitored.
```shell
line con 0
exec-timeout 0 30
login authentication default

line vty 0 4
line vty 5 15
transport input ssh
exec-timeout 0 30
login authentication default
```
* Applies authentication via AAA to both console and SSH (VTY) access.
* Enables SSH-only remote access.

## 📌 full configurations

```shell
hostname SERVER-ROOM

enable secret cisco
username admin privilege 15 password cisco
service password-encryption

aaa new-model
aaa local authentication attempts max-fail 3
aaa authentication login default local

service timestamps debug datetime msec
service timestamps log datetime msec

no logging console
no logging monitor

clock timezone GMT 5 30

ip domain name cisco.local

ip default-gateway 192.168.100.254


no ip http server

no ip http secure-server

ip ssh version 2

interface Port-channel1
 description Uplink to DIST VSS Pair
 switchport trunk native vlan 100
 switchport trunk allowed vlan 10,14,100
 switchport mode trunk
 spanning-tree portfast trunk
exit


interface GigabitEthernet1/0/1
 description USER-ACCESS
 switchport access vlan 10
 switchport mode access
 spanning-tree portfast
 spanning-tree bpduguard enable
exit


interface TenGigabitEthernet1/1/1
 description Uplink to Distribution (10G SFP)
 switchport trunk native vlan 100
 switchport trunk allowed vlan 10,14,100
 switchport mode trunk
 channel-group 1 mode active
 spanning-tree portfast trunk
exit

interface TenGigabitEthernet1/1/2
 description Uplink to Distribution (10G SFP)
 switchport trunk native vlan 100
 switchport trunk allowed vlan 10,14,100
 switchport mode trunk
 channel-group 1 mode active
 spanning-tree portfast trunk
exit

interface Vlan100
ip address 192.168.100.1 255.255.255.0
name MGT-VLAN
exit

ip default-gateway 192.168.100.254


logging facility local6
logging snmp-trap emergencies
logging snmp-trap alerts
logging snmp-trap critical
logging snmp-trap errors
logging snmp-trap warnings
logging snmp-trap notifications
logging snmp-trap informational
logging snmp-trap debugging
logging host 192.168.100.2
logging host 192.168.100.3

snmp-server group cisco v3 priv
snmp-server community cisco RO






banner login ^CC
*******************************************************************
*              NETWORK OPERATIONS CENTRE-COMPANY                  *
*-----------------------------------------------------------------*
*       ALL CONNECTIONS ARE MONITORED AND RECORDED                *
*   DISCONNECT IMMEDIATELY IF YOU ARE NOT AN AUTHORIZED USER!     *
*                                                                 *
* Anyone using this system expressly consents to such monitoring  *
* and is advised that if such monitoring reveals possible         *
* evidence of criminal activity, system personnel may provide the *
* evidence of such monitoring to law enforcement officials.       *
*******************************************************************
^C

line con 0
exec-timeout 0 30
login authentication default
exit


line vty 0 4
transport input ssh
exec-timeout 0 30
login authentication default
exit

 
line vty 5 15
transport input ssh
login authentication default
exit


ntp server 2.2.2.2
```
