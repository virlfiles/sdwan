# Cisco SDWAN VIRL Topology
Example Enterprise SDWAN deployment with single HQ and two remote sites.

> Note: This topology requires your VIRL server have images for vmanage, vbond, vsmart, vedge, and CSR1000v. And configuration of the SDWAN network will be required after `virl up`

## Requirements

* [virlutils](https://github.com/CiscoDevNet/virlutils)
* A Viptela license file and the Organization name associated with that license file.  The Organization name should be provided where you see: `<your org name>`

## Table of Contents
* [Topology](#topology)
* [Scenario](#scenario)
* [Deploy Topology](#deploy-topology)

## Topology

![Alt Text](diagram.png)

## Scenario
Enterprise has a single HQ and two remote sites.  All routing at the remote sites is handled by the vEdge.  The HQ does full internet peering, so the vEdge there is in a DMZ and exchanges routes with the HQ Core router via OSPF.

The Viptela control plane (i.e. vmanage1, vbond1, vsmart1) is hosted in the DMZ at the enterprise HQ.

### Network Setup
Since this is being done in VIRL, the first interface all of devices have a connection to the `flat` management network.  The subsequent interfaces are put into the simulated network.  For Viptela, the management interfaces are moved to VPN 512 to simulate OOB management access.  The following networks are allocated to the topology:

* 192.168.0.0/16: Enterprise private
  * 192.168.1.0/24: Enterprise Site 1
  * 192.168.2.0/24: Enterprise Site 2
  * 192.168.255.0/24: Enterprise Loopbacks/TLOCS
* 10.0.0.0/8: Enterprise routable
  * 10.0.0.0/24: Enterprise HQ DMZ
  * 10.0.1.0/24: Enterprise HQ DC
  * 10.0.255.0/24: Enterprise HQ P-T-P links
* 172.20.0.0/16: Internet Core

### Test Nodes
There are several nodes in the topology that can be used for testing:
* server1: A server sitting in the HQ data center that can be reached both internally from within the enterprise and externally.
* service1: A server sitting in an SP data center that represents a service that is not associated with the Enterprise.
* host1/2: These hosts are sitting internal to the enterprise.

_The default username/password is `admin/admin`_

## Deploy Topology

_NOTE: The Topology requires images for vmanage, vbond, vsmart, vedge, and CSR1000v_

### To deploy with virlutils

Install [virlutils](https://github.com/CiscoDevNet/virlutils)

Create a .virlrc:
```
VIRL_USERNAME=guest
VIRL_PASSWORD=guest
VIRL_HOST=your.virl.server
```

### Start the Topology
```
virl up --provision virlfiles/sdwan
```

Verify the nodes are built (Note: These are the MGMT IP addresses to be used later, Note: Your addresses will be different!):

```
# virl nodes
Here is a list of all the running nodes
╒═══════════╤══════════╤═════════╤═════════════╤════════════╤══════════════════════╤════════════════════╕
│ Node      │ Type     │ State   │ Reachable   │ Protocol   │ Management Address   │ External Address   │
╞═══════════╪══════════╪═════════╪═════════════╪════════════╪══════════════════════╪════════════════════╡
│ ~mgmt-lxc │ mgmt-lxc │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.161       │ 199.66.188.162     │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ vedge1    │ vEdge    │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.169       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ vedge2    │ vEdge    │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.170       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ vbond1    │ vBond    │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.167       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ host2     │ lxc      │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.174       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ host1     │ lxc      │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.173       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ vmanage1  │ vManage  │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.171       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ sp1       │ CSR1000v │ ACTIVE  │ REACHABLE   │ telnet     │ 199.66.188.166       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ service1  │ lxc      │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.176       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ hq        │ CSR1000v │ ACTIVE  │ REACHABLE   │ telnet     │ 199.66.188.164       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ server1   │ lxc      │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.175       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ vsmart1   │ vSmart   │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.172       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ core      │ CSR1000v │ ACTIVE  │ REACHABLE   │ telnet     │ 199.66.188.163       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ vedge-hq  │ vEdge    │ ACTIVE  │ REACHABLE   │ ssh        │ 199.66.188.168       │ N/A                │
├───────────┼──────────┼─────────┼─────────────┼────────────┼──────────────────────┼────────────────────┤
│ internet  │ CSR1000v │ ACTIVE  │ REACHABLE   │ telnet     │ 199.66.188.165       │ N/A                │
╘═══════════╧══════════╧═════════╧═════════════╧════════════╧══════════════════════╧════════════════════╛
```
