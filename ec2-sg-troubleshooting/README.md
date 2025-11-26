# EC2 Security Group Troubleshooting Lab

This project simulates a real-world AWS Cloud Support scenario where EC2 instances are unable to communicate due to incorrect Security Group configurations.

The goal is to identify the root cause, apply the correct fix, and validate restored connectivity.

---

## Problem Statement
An EC2 instance was unreachable from another EC2 instance inside the same VPC.
- Ping requests were failing
- SSH access was unavailable
- HTTP connectivity was blocked

---

## Environment
- AWS EC2 (Amazon Linux 2)
- Same VPC and subnet
- Security Groups
- Linux CLI tools (ping, curl, ssh)

---

## Tests Performed
From Instance A:
- `ping <private-ip>`  
- `curl http://<private-ip>`  
- `ssh ec2-user@<private-ip>`

All tests failed initially due to incorrect inbound rules.

---

## Root Cause
The Security Group attached to Instance B was missing required inbound rules:
- ICMP was blocked
- SSH (port 22) was not allowed
- HTTP (port 80) had an incorrect source CIDR

---

## Solution
Updated Security Group rules on Instance B:
- Allowed SSH (22) from my IP
- Allowed HTTP (80) from anywhere
- Allowed **All ICMP – IPv4**

---

## Validation
After applying the fix:
- Ping requests succeeded
- SSH access was restored
- HTTP connectivity worked as expected

---

## Key Learnings
- Understanding ICMP vs TCP traffic
- How AWS Security Groups handle inbound rules
- Troubleshooting silent network drops
- Applying structured cloud support methodology
