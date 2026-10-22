# Project Report: AWS VPC Peering with Private EC2 Connectivity

**Project type:** Hands-on cloud networking lab  
**Cloud provider:** Amazon Web Services (AWS)  
**Region used:** Europe (Stockholm), `eu-north-1`  
**Status:** Core connectivity validated during the lab session  
**Live project showcase:** https://adarsh24dz.github.io/aws-vpc-peering-project/

> The live link is a static GitHub Pages portfolio/demo page. The private EC2 instances are not exposed as public web applications. The report's screenshot appendix is available in the submission PDF supplied separately; the PDF and binary screenshot files are not yet uploaded into this GitHub repository.

## 1. Objective

Build two non-overlapping VPCs and connect them using VPC Peering. Keep the application EC2 instances in private subnets without public IP addresses, use a bastion host for SSH administration, and validate private connectivity in both directions.

## 2. Architecture and addressing

| Component | Name | CIDR / private IP | Role |
|---|---|---|---|
| VPC A | `ewb-vpc-a` | `10.10.0.0/16` | Bastion and App-A |
| VPC B | `ewb-vpc-b` | `10.20.0.0/16` | App-B |
| VPC A public subnet | `ewb-a-public` | `10.10.1.0/24` | Bastion subnet |
| VPC A private subnet | `ewb-a-private` | `10.10.2.0/24` | App-A subnet |
| VPC B private subnet | `ewb-b-private` | `10.20.1.0/24` | App-B subnet |
| Bastion instance | `ewb-bastion` | `10.10.1.128` | Controlled SSH jump host |
| App-A instance | `ewb-app-a` | `10.10.2.10` | Private application host |
| App-B instance | `ewb-app-b` | `10.20.1.10` | Private application host |
| Internet Gateway | `ewb-igw-a` | Attached to VPC A | Internet access for the public subnet |
| VPC Peering | `ewb-vpc-peering-a-b` | Active during verification | Private routing between VPCs |

## 3. Implementation summary

1. Created VPC A (`10.10.0.0/16`) and VPC B (`10.20.0.0/16`).
2. Created one public and one private subnet in VPC A, plus one private subnet in VPC B.
3. Attached an Internet Gateway to VPC A for the bastion's public-subnet connectivity.
4. Created a VPC Peering connection between the two VPCs and confirmed it was Active.
5. Added peering routes in the VPC A public route table, VPC A private route table, and VPC B private route table as needed for the intended traffic paths.
6. Configured security groups to limit SSH and allow the ICMP checks used during testing.
7. Launched Amazon Linux 2023 EC2 instances: one bastion and two private application instances.
8. Used the bastion to SSH to App-A and App-B.
9. Validated ICMP connectivity between App-A and App-B in both directions.

## 4. Routing configuration

| Route table | Destination | Target |
|---|---|---|
| `ewb-rt-a-public` | `10.10.0.0/16` | local |
| `ewb-rt-a-public` | `0.0.0.0/0` | `ewb-igw-a` |
| `ewb-rt-a-public` | `10.20.0.0/16` | VPC Peering connection |
| `ewb-rt-a-private` | `10.10.0.0/16` | local |
| `ewb-rt-a-private` | `10.20.0.0/16` | VPC Peering connection |
| `ewb-rt-b-private` | `10.20.0.0/16` | local |
| `ewb-rt-b-private` | `10.10.0.0/16` | VPC Peering connection |

## 5. Security considerations

- App-A and App-B were placed in private subnets and launched without public IP addresses.
- SSH administration was performed through the bastion rather than by directly exposing the private instances.
- App-B allowed SSH from the bastion private IP `10.10.1.128/32` during the lab.
- SSH should remain limited to the administrator's trusted IP and the intended jump-host path. Do not allow SSH from `0.0.0.0/0`.
- The PEM private key must remain private and must not be committed to GitHub.
- Review inbound ICMP sources and narrow them to the required peer host IPs if broad VPC CIDRs are no longer needed for the demonstration.

## 6. Test results

| Test | Observed result |
|---|---|
| VPC Peering status | Active |
| Reachability Analyzer (bastion to App-B TCP/22) | Reachable after the route was added to VPC A's public route table |
| TCP port probe from bastion to App-B | SSH port open |
| SSH from bastion to App-B | Successful |
| SSH from bastion to App-A | Successful |
| Ping App-A (`10.10.2.10`) → App-B (`10.20.1.10`) | 4/4 packets received; 0% packet loss |
| Ping App-B (`10.20.1.10`) → App-A (`10.10.2.10`) | 4/4 packets received; 0% packet loss |

The results above were observed in the lab session. They describe the state at test time; they are not a live availability check.

## 7. Evidence to attach

Add captured AWS console and terminal screenshots to the repository's `evidence/` folder. Suggested evidence includes:

- VPC CIDRs and subnet CIDRs.
- Active VPC Peering connection.
- Routes in each relevant route table.
- Internet Gateway attached to VPC A.
- Three EC2 instances and private IPs.
- Security group inbound rules.
- Successful SSH session to App-B.
- Both successful ping tests.

Do not include private keys, access tokens, passwords, or screenshots containing credentials.

## 8. Conclusion

The lab demonstrated private communication between EC2 instances in two different VPCs through VPC Peering. Correct routes on both sides, compatible security-group rules, and the bastion access path were essential to validate the topology. Bidirectional ping and SSH tests succeeded during the recorded lab session.

## 9. Cost and cleanup

EC2, EBS storage, and public IPv4 addresses may incur charges. After collecting evidence and confirming submission requirements, terminate only project-created instances if they are no longer needed, then delete project-only networking resources in dependency order. Do not remove unrelated learning resources.
