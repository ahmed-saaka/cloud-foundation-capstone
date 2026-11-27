# AWS Well-Architected Review - CF01 Capstone

**Project:** Internal Web Application  
**Region:** Europe (Ireland) - eu-west-1

This review identifies three areas where the capstone application could be improved. The findings cover the Security and Reliability pillars.

| Pillar | Risk found | Remediation | Trade-off |
|---|---|---|---|
| Security | The Application Load Balancer accepts HTTP traffic only. Data sent between users and the application is not encrypted while in transit. | Use AWS Certificate Manager (ACM) to provide an SSL/TLS certificate. Add an HTTPS listener on port 443 and redirect HTTP traffic on port 80 to HTTPS. | Requires certificate and domain management. |
| Security | The S3 bucket uses SSE-S3 encryption, where Amazon S3 manages the encryption keys. This provides encryption at rest but offers less direct control over key policies and access. | Use AWS Key Management Service (AWS KMS) with SSE-KMS to manage encryption keys and control their use through key policies. | KMS can introduce additional charges and key-management responsibilities. |
| Reliability | The Auto Scaling group has a fixed minimum, desired and maximum capacity of two instances. The CPU alarm sends notifications but does not automatically add capacity when demand increases. | Configure an EC2 Auto Scaling target-tracking policy based on average CPU utilization. | Additional instances increase running costs during periods of high demand. |

## Conclusion

The application demonstrates important AWS best practices, including deployment across two Availability Zones, private EC2 instances, an Application Load Balancer, private S3 access through an IAM role, and CloudWatch monitoring with SNS notifications.

Implementing the improvements identified above would strengthen the application's security and reliability as its usage grows.