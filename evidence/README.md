# AWS VPC Peering Evidence Screenshots

The evidence selection for the internship report has 20 descriptive screenshots. The screenshots have been reviewed for overlap; the administrator public IP and Bastion public IPv4 are redacted in the portfolio copies.

## Screenshot filenames

- `01-vpc-a-details.png` - VPC A CIDR and details
- `02-vpc-b-details.png` - VPC B CIDR and details
- `03-subnet-a-public.png` - VPC A public subnet
- `04-subnet-a-private.png` - VPC A private subnet
- `05-subnet-b-private.png` - VPC B private subnet
- `06-peering-active.png` - Active VPC Peering connection
- `07-route-a-private.png` - VPC A private route table
- `08-route-b-private.png` - VPC B private route table
- `09-bastion-instance-redacted.png` - Bastion instance details; public IPv4 redacted
- `10-app-a-instance.png` - App-A private instance
- `11-app-b-instance.png` - App-B private instance
- `12-route-a-public.png` - VPC A public route table with IGW and peering routes
- `13-security-group-bastion-redacted.png` - Bastion security group; source values redacted
- `14-security-group-app-a.png` - App-A security group
- `15-security-group-app-b.png` - App-B security group
- `16-vpc-a-resource-map.png` - VPC A resource map
- `17-internet-gateway-map.png` - Resource map showing `ewb-igw-a`
- `18-ssh-and-ping-app-a-to-b.png` - SSH and App-A-to-App-B ping proof
- `19-ping-app-b-to-a.png` - App-B-to-App-A ping proof
- `20-reachability-analyzer.png` - Reachability Analyzer result

## Uploading the images

The evidence binaries are packaged in the companion `AWS_VPC_Peering_Submission_Package.zip`. After extracting it, upload the PNG files from its `evidence/` directory to this GitHub directory using **Add file -> Upload files**, then commit to `main`. Until that upload is done, this file is a manifest, not a claim that the image binaries are already committed here.

## Privacy

Never upload PEM private keys, AWS credentials, passwords, tokens, or browser-session data. The screenshots document results observed on 22 October 2026; resources and configuration may change after capture.
