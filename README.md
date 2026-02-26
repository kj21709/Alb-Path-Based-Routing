# Advanced Request Routing with ALB (HTTPS + Route 53 + ACM)

## Project Overview

This project demonstrates a production-style, multi-AZ web architecture
on AWS using:

-   Custom VPC with public and private subnets
-   Internet-facing Application Load Balancer (ALB)
-   HTTPS termination using AWS Certificate Manager (ACM)
-   Route 53 DNS integration (Alias record)
-   Private EC2 Auto Scaling Groups
-   Host-based and path-based routing
-   IAM least-privilege S3 access
-   Modular nested CloudFormation stacks

The objective was to design infrastructure that mirrors real-world
production environments rather than a single-instance demo.

------------------------------------------------------------------------

## Architecture Diagram

Internet \| v Route 53 (Alias Record) \| v Application Load Balancer
(HTTPS - TLS via ACM) \| +----------------------+ \| \| Red Target Group
Blue Target Group \| \| Red ASG (Private) Blue ASG (Private) \| Amazon
S3 (Static Assets)

------------------------------------------------------------------------

## Core Architecture Components

### Networking

-   Custom VPC (10.0.0.0/16)
-   2 Public Subnets (ALB tier)
-   2 Private Subnets (Application tier)
-   Internet Gateway
-   NAT Gateway (controlled outbound access)
-   Dedicated route tables per tier

### Load Balancing

-   Internet-facing ALB
-   HTTP (80) redirects to HTTPS (443)
-   TLS termination using ACM certificate
-   Path-based routing (/red, /blue)
-   Host-based routing (red.example.com, blue.example.com)
-   Default traffic forwarded to Blue target group

### DNS & Security

-   Route 53 Alias A record pointing to ALB
-   No hardcoded IP addresses
-   ACM-managed certificate lifecycle
-   TLS encryption in transit
-   EC2 instances in private subnets (no public IPs)
-   Security groups enforcing tier isolation

### Compute Layer

-   Launch Templates
-   Auto Scaling Groups (min:1, max:3, desired:1)
-   ELB health checks enabled
-   Rolling update strategy
-   CloudWatch logs enabled for troubleshooting

------------------------------------------------------------------------

## Nested Stack Structure

main.yaml ├── vpc.yaml ├── security.yaml ├── iam.yaml ├── alb.yaml └──
ASG.yaml

Each stack is modular and independently maintainable.

------------------------------------------------------------------------

## Deployment Requirements

-   ACM Certificate ARN (same region as ALB)
-   Route 53 Hosted Zone ID
-   DNS Record Name (e.g., app.example.com)
-   S3 bucket containing static assets

Deploy using CloudFormation root stack and pass nested template URLs.

------------------------------------------------------------------------

## Estimated Monthly Cost (us-east-1, low traffic)

-   2 t2.micro instances: \~\$16
-   Application Load Balancer: \~\$16
-   NAT Gateway: \~\$32
-   Data transfer: \~\$5
-   S3 storage: \<\$1

Estimated Total: \~\$70/month

Cost Optimization Options: - S3 Gateway VPC Endpoint (remove NAT
dependency) - Graviton instances (t4g.micro) - Scheduled scaling - Spot
instances

------------------------------------------------------------------------

## Key Takeaways

-   Proper VPC segmentation improves security posture.
-   ACM + Route 53 simplifies secure HTTPS deployments.
-   Health check timing directly impacts ASG stability.
-   Nested stacks improve maintainability and scalability.
-   Production-style design requires thinking beyond single-instance
    deployments.

------------------------------------------------------------------------

Built as part of hands-on Cloud Architect training and infrastructure
automation practice.


CLI command to create stack

aws cloudformation create-stack \ 
--stack-name arr-main \ 
--template-url https://my-arr-templates-123.s3.amazonaws.com/arr/main.yaml \ --capabilities CAPABILITY_NAMED_IAM \ 
--parameters \ 
ParameterKey=NetworkTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/vpc.yaml \ 
ParameterKey=SecurityTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/security.yaml \ 
ParameterKey=IAMTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/iam.yaml \ 
ParameterKey=ALBTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/alb.yaml \ 
ParameterKey=ASGTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/ASG.yaml


or

aws cloudformation create-stack \
  --stack-name arr-main \
  --template-url https://my-arr-templates-123.s3.amazonaws.com/arr/main.yaml \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameters \
    ParameterKey=NetworkTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/vpc.yaml \
    ParameterKey=SecurityTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/security.yaml \
    ParameterKey=IAMTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/iam.yaml \
    ParameterKey=ALBTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/alb.yaml \
    ParameterKey=ASGTemplateURL,ParameterValue=https://my-arr-templates-123.s3.amazonaws.com/arr/ASG.yaml \
    ParameterKey=BucketName,ParameterValue=arr-bucket12398 \
    ParameterKey=CertificateArn,ParameterValue=arn:aws:acm:us-east-1:843553758024:certificate/8c470956-6728-4f7e-bc59-fa1072d6f925 \
    ParameterKey=HostedZoneId,ParameterValue=Z00414272K7YBCYJ3PEG4\
    ParameterKey=RecordName,ParameterValue=arr.homenub.com