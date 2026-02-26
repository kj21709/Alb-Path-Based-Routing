I stopped building demo cloud projects. I started building
production-style architectures.

This week I implemented a secure, multi-AZ Advanced Request Routing
architecture on AWS with:

• Custom VPC (public + private subnets) • Internet-facing ALB with HTTPS
• TLS termination via ACM • Route 53 alias record integration • Private
EC2 Auto Scaling Groups • Host-based and path-based routing • IAM
least-privilege S3 access • Modular nested CloudFormation stacks

Traffic flow: Internet → Route 53 → ALB (HTTPS) → Target Groups →
Private EC2 → S3

Why this matters: Instead of launching a single EC2 instance, I focused
on:

✔ Multi-AZ high availability\
✔ Tier isolation and security boundaries\
✔ HTTPS encryption in transit\
✔ Rolling updates with ASG\
✔ Infrastructure modularization

Estimated low-traffic cost: \~\$70/month (NAT being the largest fixed
cost).

Next improvements: • WAF integration •Scaling policies with CloudWatch • 
Blue/Green deployment automation

Design like it's production --- even when it's practice.

#AWS #CloudArchitecture #CloudComputing #CloudFormation #DevOps
#InfrastructureAsCode
