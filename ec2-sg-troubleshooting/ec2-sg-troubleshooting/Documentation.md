# EC2 Security Group Troubleshooting – Full Documentation

## 1. Objective
The objective of this project is to simulate a real-world AWS Cloud Support issue where EC2 instances are unable to communicate due to incorrect Security Group configurations, then troubleshoot and resolve the issue using a structured support methodology.

---

## 2. Environment Details

### AWS Services Used
- Amazon EC2
- AWS Security Groups
- VPC (default)
- Linux (Amazon Linux 2)

### Instances
- **Instance A** (Working instance)
- **Instance B** (Intentionally misconfigured instance)

Both instances were launched in the same VPC and subnet and communicate using private IP addresses.

---

## 3. Architecture Overview
- Two EC2 instances inside the same subnet
- Communication tested using ICMP, SSH, and HTTP
- Instance B initially configured with restrictive inbound rules to simulate failure

---

## 4. Initial Security Group Configuration

### Instance A – Security Group
Inbound rules:
- SSH (Port 22) – My IP
- HTTP (Port 80) – 0.0.0.0/0

Outbound rules:
- All traffic allowed (default)

---

### Instance B – Security Group (Broken State)
Inbound rules:
- HTTP (Port 80) – 10.0.0.0/32 (Incorrect CIDR)
- No SSH rule
- No ICMP rule

Outbound rules:
- All traffic allowed (default)

This configuration intentionally prevents network connectivity.

---

## 5. Issue Reproduction & Testing

All tests were performed from **Instance A**.

### ICMP Test (Ping)
Command:

Result:
- No response
- Packets dropped due to blocked ICMP traffic

---

### SSH Test
Command:

Result:
- Connection timed out
- SSH port not allowed in Security Group

---

### HTTP Test
Command:

Result:
- Connection failed
- HTTP rule restricted to incorrect source

---

## 6. Troubleshooting Process

The following troubleshooting steps were followed:

1. Verified that both EC2 instances were running and passed health checks
2. Confirmed both instances were deployed in the same VPC and subnet
3. Checked route tables and verified local routing
4. Reviewed Security Group inbound rules for Instance B
5. Identified missing ICMP and SSH permissions
6. Detected incorrect CIDR range in HTTP rule

---

## 7. Root Cause Analysis

The root cause of the connectivity failure was incorrect and incomplete Security Group inbound rules on Instance B:
- ICMP was completely blocked
- SSH access was not permitted
- HTTP access restricted to an invalid CIDR

AWS Security Groups silently dropped incoming packets, resulting in no error responses.

---

## 8. Fix Applied

Security Group inbound rules for Instance B were updated as follows:
- Added SSH (Port 22) – My IP
- Updated HTTP (Port 80) – 0.0.0.0/0
- Added **All ICMP – IPv4**

No changes were required to outbound rules.

---

## 9. Post-Fix Validation

After applying the fixes, all connectivity tests were re-executed.

### ICMP
- Ping received successful replies

### SSH
- SSH login succeeded

### HTTP
- Web service responded successfully to curl requests

Connectivity between the two instances was fully restored.

---

## 10. Conclusion

This project demonstrates a complete Cloud Support troubleshooting lifecycle:
- Issue identification
- Connectivity testing
- Root cause analysis
- Corrective action
- Validation

It highlights the importance of understanding AWS Security Groups, network protocols (ICMP vs TCP), and systematic troubleshooting in real AWS environments.
