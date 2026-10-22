# AWS VPC Peering Project

A hands-on AWS networking project demonstrating private communication between two Virtual Private Clouds (VPCs) using VPC Peering, private EC2 instances, route tables, security groups, and a bastion host.

## Project goals

- Build two isolated VPCs with non-overlapping CIDR ranges.
- Create public and private subnets.
- Establish a VPC Peering connection between the VPCs.
- Configure routes in both directions.
- Keep application EC2 instances in private subnets without public IP addresses.
- Use a bastion host to administer the private instances over SSH.
- Verify connectivity using SSH and ICMP ping tests.

## Architecture

```text
                              Internet
                                 |
                         Internet Gateway
                                 |
                    VPC A: 10.10.0.0/16
                                 |
                +----------------+----------------+
                |                                 |
       Public subnet                       Private subnet
       10.10.1.0/24                        10.10.2.0/24
                |                                 |
      Bastion: 10.10.1.128                App-A: 10.10.2.10
                |                                 |
                +----------- VPC Peering ---------+
                                 |
                    VPC B: 10.20.0.0/16
                                 |
                         Private subnet
                         10.20.1.0/24
                                 |
                         App-B: 10.20.1.10
```

> The diagram documents the intended logical topology. Actual resource IDs and live AWS configuration can change; refer to screenshots you capture during deployment.

## AWS resources

| Resource | Name | Configuration |
|---|---|---|
| VPC A | `ewb-vpc-a` | `10.10.0.0/16` |
| VPC B | `ewb-vpc-b` | `10.20.0.0/16` |
| VPC A public subnet | `ewb-a-public` | `10.10.1.0/24` |
| VPC A private subnet | `ewb-a-private` | `10.10.2.0/24` |
| VPC B private subnet | `ewb-b-private` | `10.20.1.0/24` |
| Internet Gateway | `ewb-igw-a` | Attached to VPC A |
| VPC Peering | `ewb-vpc-peering-a-b` | Active at the time of validation |
| Bastion EC2 | `ewb-bastion` | Private IP `10.10.1.128`; public access used for administration |
| App-A EC2 | `ewb-app-a` | Private IP `10.10.2.10`; no public IP |
| App-B EC2 | `ewb-app-b` | Private IP `10.20.1.10`; no public IP |

## Routing

The VPC route tables need routes for both sides of the peering connection.

| Route table | Destination | Target |
|---|---|---|
| `ewb-rt-a-public` | `10.10.0.0/16` | local |
| `ewb-rt-a-public` | `0.0.0.0/0` | Internet Gateway |
| `ewb-rt-a-public` | `10.20.0.0/16` | VPC Peering connection |
| `ewb-rt-a-private` | `10.10.0.0/16` | local |
| `ewb-rt-a-private` | `10.20.0.0/16` | VPC Peering connection |
| `ewb-rt-b-private` | `10.20.0.0/16` | local |
| `ewb-rt-b-private` | `10.10.0.0/16` | VPC Peering connection |

The public route table has a peering route because the bastion host is in VPC A's public subnet and needs a route to App-B. The private route tables provide the routes for private subnet traffic.

## Security design

- Bastion SSH access is restricted to an administrator source IP where possible.
- App-A and App-B are kept in private subnets without public IP addresses.
- SSH to App-B is allowed from the bastion's private IP (`10.10.1.128/32`).
- ICMP rules allow the required private-network ping tests between VPCs.
- Do not open SSH (TCP/22) to `0.0.0.0/0`.
- Never commit private keys (`.pem`), AWS access keys, credentials, or other secrets to this repository.

## Validation and test results

The following checks were completed during the lab:

| Test | Result |
|---|---|
| VPC Peering connection status | Active |
| Reachability Analyzer path from bastion toward App-B TCP/22 | Reachable |
| TCP/22 probe from bastion to App-B | SSH port open |
| SSH from bastion to App-B | Successful |
| SSH from bastion to App-A | Successful |
| Ping from App-A to App-B | 4 packets received, 0% packet loss |
| Ping from App-B to App-A | 4 packets received, 0% packet loss |

These are results observed during the lab session. They are not a guarantee that resources remain running or that configuration has not changed since then.

## Evidence screenshots

The `evidence/` folder contains a checklist. Add screenshots you captured from your own AWS console and terminal. Recommended evidence:

1. VPC A and VPC B details with CIDRs.
2. All three subnet details.
3. VPC Peering connection showing Active status.
4. Route tables: `ewb-rt-a-public`, `ewb-rt-a-private`, and `ewb-rt-b-private`.
5. Internet Gateway `ewb-igw-a` attached to VPC A.
6. The three EC2 instances and their private IPs.
7. Security group inbound rules for the bastion, App-A, and App-B.
8. Successful SSH session from bastion to App-B.
9. Successful ping in both directions.
10. Reachability Analyzer showing the successful path.

Do not add screenshots that reveal private keys, passwords, tokens, or credentials.

## Reproduction outline

1. Create VPC A and VPC B with the CIDRs above.
2. Create the required subnets, and attach an Internet Gateway to VPC A.
3. Create and accept the VPC Peering connection.
4. Add the required peering routes to the relevant route tables.
5. Configure security groups with least-privilege SSH and ICMP rules.
6. Launch the bastion and private application instances with the intended private IPs.
7. Connect to the bastion, then use SSH to connect to App-A and App-B.
8. Run bidirectional ping tests and capture the output.

This repository is a lab record, not an infrastructure-as-code deployment. It currently documents console-built resources; no Terraform or CloudFormation templates are included.

## Cost and cleanup

AWS resources can incur charges, including EC2 instances, EBS volumes, and public IPv4 addresses. When the project evidence is complete, review the resources and terminate only the resources created for this project if they are no longer needed. Do not terminate unrelated learning instances. Then remove project-only peering connections, route resources, gateways, subnets, and VPCs as appropriate.

## Hosted project page

The static website is in [`index.html`](index.html). To publish it, open **Settings → Pages**, choose **Deploy from a branch**, select **main** and **/(root)**, then save. After GitHub Pages finishes building, GitHub will show the published URL in this section of repository settings.

## Project report

See [docs/PROJECT_REPORT.md](docs/PROJECT_REPORT.md) for the internship-style report draft.

## License

See [LICENSE](LICENSE).
