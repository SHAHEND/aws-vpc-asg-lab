# AWS VPC, Auto Scaling & Monitoring Lab

Hands-on lab built on AWS (eu-north-1) covering networking, high availability, and monitoring.

## Architecture
- Custom VPC (10.0.0.0/16) with 2 public + 2 private subnets across 2 AZs
- EC2 in public subnet (public IP) and EC2 in private subnet
- VPC Peering between the custom VPC and the Default VPC
- Auto Scaling Group behind an Internal ALB in private subnets
- CloudWatch alarm + SNS email notification

## Part 1: VPC & EC2
1. Created `Lab-vpc` with 2 public and 2 private subnets.
2. Launched one EC2 in a public subnet (with public IP) and one in a private subnet.


![EC2 instances](screenshots/02-ec2-instances.png)



## Part 2: Security Group
Allowed SSH (port 22) to the public EC2 only from the private EC2's security group (least privilege).


![SG rules](screenshots/03-security-group-rules.png)



## Part 3: VPC Peering
- Requester: Lab-vpc (10.0.0.0/16), Accepter: Default VPC (172.31.0.0/16)
- Accepted the request, then added routes in both directions.


![Peering](screenshots/04-vpc-peering-active.png)
![Architecture](architecture.png)




![Routes](screenshots/05-route-tables.png)



## Part 4: Auto Scaling + Internal ALB
- Launch template, target group, internal ALB (private subnets)
- ASG: desired 2, min 1, max 3, target tracking on CPU at 50%


![ASG](screenshots/09-asg-details.png)



## Part 5: Monitoring
CloudWatch alarm on EC2 CPUUtilization (< 50) sending notifications to an SNS topic.


![Alarm](screenshots/10-cloudwatch-alarm.png)



## What I learned
- Public vs private subnets and route tables
- Security group referencing instead of open CIDRs
- Peering requires routes on both sides and non-overlapping CIDRs
- ASG, target groups, and internal load balancing
- Cost control: terminate ASG, ALB, and unused resources after labs

## Challenges
- Peering route pointed to 0.0.0.0/0 by mistake; fixed to the peer VPC CIDR
- Service-linked role delay when creating the scaling policy
- "AlarmActions must not contain repeats" error (duplicate SNS action)
