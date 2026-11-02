# LACP EtherChannel and link redundancy | Project 12
Combine two Gigabit switch links into one logical Layer 2 connection using LACP. Verify that PCs can still communicate when one member link is disabled.

![Two-switch topology](images/topology.png)

## Design
| Connection | Ports |
|---|---|
| PC1 to S1 | PC Fa0 to switch Fa0/1 |
| PC2 to S2 | PC Fa0 to switch Fa0/1 |
| First switch link | S1 Gi0/1 to S2 Gi0/1 |
| Second switch link | S1 Gi0/2 to S2 Gi0/2 |

PC1 is 192.168.50.11/24; PC2 is 192.168.50.12/24. PC access ports remain in default VLAN 1. A router is unnecessary for this same-subnet communication. Both member ports are trunks using channel-group 1 mode active; Port-channel1 is a trunk. The port mapping is the lesson cabling plan; port labels are not shown in the screenshot.

## Results
| State | EtherChannel evidence | PC ping |
|---|---|---|
| Both members bundled | Po1(SU), Gi0/1(P), Gi0/2(P) on S2 | Success reported |
| S1 Gi0/1 shut down | Po1(SU), Gi0/1(D), Gi0/2(P) | 4/4 replies, 0% loss |
| S1 Gi0/1 restored | Po1(SU), both members (P) on S1 | Separate ping not supplied |

S = Layer 2; U = in use; P = bundled; D = down; I = individual. LACP active initiates negotiation. Matching settings on both switches are needed. The initial fault was missing configuration on S2; applying it formed the bundle.

## Why it matters
The logical connection remains available with one member down. EtherChannel can share traffic across members, but a single flow is normally assigned to one member. This lab demonstrates connectivity and member-link resilience, not a measured speed increase or zero interruption during the exact failure instant.

## Open and reproduce
Open [Project-12-EtherChannel.pkt](lab/Project-12-EtherChannel.pkt). Follow [build and test commands](configs/Build-and-Test-Commands.txt). Use show etherchannel summary on each switch, and ping 192.168.50.12 from PC1. Restore G0/1 before saving the final lab.

[Switch configurations](configs/) · [Verification and troubleshooting](tests/) · [Combined evidence JPG](images/Project-12-Network-Evidence.jpg)

## Evidence scope
The original .pkt and screenshot bytes are included unchanged; the .pkt has a shorter Project 12 filename. The simulation was not independently run or internally inspected during packaging. Configurations are transcribed from supplied text with repeated empty separators removed; S2's VTY continuation is included, but its final end marker was not supplied. CLI image panels are recreated text rather than original terminal screenshots. No throughput benchmark is claimed.
