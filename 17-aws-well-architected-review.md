# AWS Well-Architected Review - CF01 Capstone

**Project:** Internal Web Application  
**Region:** Europe (Ireland) - eu-west-1

**Project documentation:** See [README.md](README.md) for the architecture overview, implementation details and complete evidence index.

This review identifies three areas where the capstone application could be improved. The findings cover the Security and Reliability pillars.

## Findings

| Pillar | Risk found | Remediation | Trade-off |
|---|---|---|---|
| Security | The Application Load Balancer accepts HTTP traffic only. Data sent between users and the application is not encrypted in transit. The application access evidence shows HTTP access (see [01 - Application access](01-application-accessible-through-load-balancer.png)). | Use AWS Certificate Manager (ACM) to provide a TLS certificate. Add an HTTPS listener on port 443 and redirect HTTP traffic on port 80 to HTTPS. | Requires certificate and domain management. |
| Security | The S3 bucket uses SSE-S3 encryption, where Amazon S3 manages the encryption keys. This provides encryption at rest but offers less direct control over key policies and access. The S3 configuration and restricted IAM access are documented in [07 - IAM policy](07-iam-policy-for-private-s3-read-access.png) and [09 - S3 Block Public Access](09-s3-block-public-access-enabled.png). | Use AWS Key Management Service (AWS KMS) with SSE-KMS to manage encryption keys and control their use through key policies. | KMS can introduce additional charges and key-management responsibilities. |
| Reliability | The Auto Scaling group has a fixed minimum capacity of 2, desired capacity of 2 and maximum capacity of 2 instances. The CloudWatch CPU alarm threshold is greater than 70%. The alarm sends notifications but does not automatically add capacity when demand increases. See [02 - Instances across two Availability Zones](02-ec2-instances-across-two-availability-zones.png), [04 - Replacement activity](04-auto-scaling-group-replacement-activity.png), [05 - Replacement instance launched](05-auto-scaling-group-successfully-launches-replacement.png) and [12 - CPU alarm configuration](12-cloudwatch-cpu-alarm-configuration.png). | Configure an EC2 Auto Scaling target-tracking policy based on average CPU utilisation. | Additional instances increase running costs during periods of high demand. |

## Conclusion

The application demonstrates important AWS practices:

- **Multi-AZ deployment:** EC2 instances were deployed across two Availability Zones ([02 - Availability Zones](02-ec2-instances-across-two-availability-zones.png)).
- **High availability:** The Auto Scaling Group replaced a terminated instance while the application remained available ([04 - Replacement activity](04-auto-scaling-group-replacement-activity.png), [06 - Application availability after replacement](06-application-available-after-instance-replacement.png)).
- **Private S3 access:** An IAM policy provided restricted read access to the application object ([07 - IAM policy](07-iam-policy-for-private-s3-read-access.png)).
- **S3 security:** Block Public Access was enabled ([09 - S3 Block Public Access](09-s3-block-public-access-enabled.png)).
- **Monitoring:** CloudWatch and SNS were configured and the notification test was successful ([12 - CPU alarm configuration](12-cloudwatch-cpu-alarm-configuration.png), [15 - SNS email received](15-sns-email-notification-received.png)).

Implementing the improvements identified above would strengthen the application's security and reliability as its usage grows.

For the full implementation evidence and teardown records, see the [project README](README.md).
