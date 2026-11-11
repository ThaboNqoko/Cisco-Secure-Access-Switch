Cisco-: Secure Access Switch Baseline - Cisco Catalyst 3560 (48-port)

A hands-on network engineering project that builds and hardens a single **48-port Cisco Catalyst access switch** for a small branch office, using one console/admin PC and one guest PC. It covers VLAN segmentation, inter-VLAN routing, a DHCP server, Layer 2 security features, ACL-based guest isolation, and a hardened management plane, backed by a full test plan.


<img width="4032" height="3024" alt="IMG_3740" src="https://github.com/user-attachments/assets/a4e91e28-bac3-4932-8dab-6121ce1c221b" />



 Project summary

| | |
|---|---|
| **Scenario** | Branch office with trusted staff and untrusted guest users |
| **Device** | Cisco Catalyst 3560, 48 x 10/100 + 2 x Gigabit (`BR1-SW01`) |
| **Hosts** | PC-A (console client + staff host), PC-B (guest host) |
| **Focus** | Segmentation, Layer 2 security, secure management, documentation |
| **Config** | [`configs/BR1-SW01.cfg`](configs/BR1-SW01.cfg) |

Objectives

1. Segment staff, guest and management traffic with VLANs.
2. Route between VLANs on the switch and run DHCP locally.
3. Isolate guests from internal networks with an ACL.
4. Secure management: SSHv2 only, ACL-restricted, console out-of-band.
5. Mitigate common Layer 2 attacks: rogue DHCP, ARP spoofing, MAC flooding, loops, unauthorised ports.
6. Document, test and evidence everything.

Logical design

<img width="1536" height="1024" alt="Branch Office Secure Network Topology" src="https://github.com/user-attachments/assets/63661882-f836-4356-aa98-8f16c5a493ff" />




| VLAN | Name | Subnet | Gateway | Ports |
|------|------|--------|---------|-------|
| 10 | STAFF | 192.168.10.0/24 | 192.168.10.1 | Fa0/1-24 |
| 20 | GUEST | 192.168.20.0/24 | 192.168.20.1 | Fa0/25-40 |
| 99 | MANAGEMENT | 192.168.99.0/24 | 192.168.99.1 | Fa0/41-44 |
| 999 | BLACKHOLE | - | - | Fa0/45-48, Gi0/1-2 (shutdown) |

Features implemented

| Area | Implementation |
|------|----------------|
| Switching | VLANs, Rapid-PVST+, VTP transparent, deterministic root priority |
| Routing | `ip routing` with SVIs per VLAN |
| Addressing | On-switch DHCP pools with excluded ranges |
| Access security | Sticky port security (max 1), PortFast, BPDU guard, storm control, DTP disabled |
| Attack mitigation | DHCP snooping with rate limiting, Dynamic ARP Inspection |
| Policy | `GUEST-IN` extended ACL blocks guest access to internal ranges |
| Management | SSHv2 only, `MGMT-ACCESS` ACL on VTY, login throttling, timeouts, banner, HTTP disabled |
| Operations | Errdisable auto-recovery, buffered logging, unused ports parked and shut down |

Documentation

| Document | Description |
|----------|-------------|
| [01 - Design](docs/01-design.md) | Requirements, equipment, IP/VLAN/port plan, security rationale |
| [02 - Implementation guide](docs/02-implementation-guide.md) | Phase-by-phase build with commands |
| [03 - PC setup](docs/03-pc-setup.md) | Console and host configuration |
| [04 - Test plan](docs/04-test-plan.md) | 19 tests with expected results and evidence mapping |
| [05 - Troubleshooting](docs/05-troubleshooting.md) | Common issues, causes and fixes |
| [Final config](configs/BR1-SW01.cfg) | Complete sanitised configuration |
| [Evidence](evidence/) | Screenshots from the physical lab |

Repository structure

```
.
├── README.md
├── LICENSE
├── configs/
│   └── BR1-SW01.cfg
├── diagrams/
│   ├── physical-topology.svg
│   └── logical-topology.svg
├── docs/
│   ├── 01-design.md
│   ├── 02-implementation-guide.md
│   ├── 03-pc-setup.md
│   ├── 04-test-plan.md
│   └── 05-troubleshooting.md
└── evidence/
    ├── README.md
    └── screenshots/
```

Future improvements

- Add a second switch to introduce 802.1Q trunking, EtherChannel and real STP convergence.
- Add a router or firewall uplink for NAT and internet access, plus a stateful guest policy.
- Centralise logging with a syslog server and add NTP.
- Replace local accounts with AAA (RADIUS/TACACS+) and add 802.1X.

Author

**Thabo Nqoko** - Network Engineer (Level 0), Durban, South Africa
[LinkedIn](https://linkedin.com/in/thabo-nqoko-a7403a442) | [Portfolio](https://thabonqoko.github.io) | [GitHub](https://github.com/1of1Thabo)

Licensed under the [MIT License](LICENSE).
