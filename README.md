# Advanced Request Routing with ALB (Host & Path Based) + Auto Scaling

This project demonstrates the deployment and hosting of a static website on S3, leveraging various resources on AWS to implement security, high availability and scalability. 

## Overview

The following resources are used to setup this environment:
-   Virtual Private Cloud
-   Application Load Balancer (ALB)
-   Host based routing
-   Path based routing
-   Auto Scaling with Target Tracking policies
-   Route 53 DNS integration
-   ACM managed HTTPS
-   Private subnets for compute
-   Multi-AZ deployment
-   Infrastructure as Code using CloudFormation (nested stacks)

![Architecture Diagram](images/Path%20Based%20Routing%20Diagram.png)
------------------------------------------------------------------------

## Auto Scaling Validation

Each Auto Scaling Group uses:

-   Metric: ALBRequestCountPerTarget
-   Target Value: 50 requests per target
-   Instance Warmup: 300 seconds
-   Min: 1
-   Max: 3

Sustained load was generated to trigger scaling events.

for i in {1..1000}; do
  curl -sk -o /dev/null -w "%{http_code}\n" https://red.homenub.com/red/index.html

### Scaling Activity Evidence

![ASG Activity](images/ASG%20activity%20history.png)

CloudWatch alarm transitioned to ALARM state and desired capacity
increased from 1 to 3 instances automatically.

------------------------------------------------------------------------

## Host-Based Routing Validation

### Red Environment

![Red Host](images/host%20based%20red.png)

### Blue Environment

![Blue Host](images/host%20based%20blue.png)

Traffic routed correctly based on host headers:

-   https://red.homenub.com → Red Target Group
-   https://blue.homenub.com → Blue Target Group

------------------------------------------------------------------------

## Path-Based Routing Validation

### Red Path

![Red Path](images/path%20based%20red.png)

### Listener Rules

![Listener Rules](images/listener-rules.png)

Routing rules:

-   /red\* → Red Target Group
-   /blue\* → Blue Target Group

------------------------------------------------------------------------

## Target Groups & Load Balancer

![Target Groups](images/target-groups.png)

![Network Path](images/LB%20network%20path.png)

Traffic is distributed across multiple Availability Zones with private
EC2 instances behind the ALB.

------------------------------------------------------------------------

## Architecture Diagram

![Architecture Diagram](images/Path%20Based%20Routing%20Diagram.png)

This environment demonstrates:

-   Secure multi-AZ architecture
-   HTTPS termination using ACM
-   Route 53 DNS integration
-   Dynamic scaling based on real traffic
-   Modular nested CloudFormation stacks

------------------------------------------------------------------------

## Lessons Learned

1.  Target tracking requires sustained load, not burst traffic.
2.  Listener rule priority directly impacts routing behavior.
3.  Instance warmup prevents scaling instability.
4.  Nested stacks improve modularity but require careful parameter
    management.
5.  Always validate CloudFormation templates before deployment.

------------------------------------------------------------------------

## Production Enhancements (Future Improvements)

-   Add AWS WAF
-   Enable ALB access logs
-   Add CloudFront in front of ALB
-   Add Database tier
-   Enable detailed CloudWatch monitoring
