# Monolithic Single-Tier AWS Architecture

A simple, highly available **single-tier monolithic** web application design on AWS.

This architecture runs the entire application (frontend + backend logic + any local data handling) on EC2 instances behind a load balancer, all within one VPC across multiple Availability Zones. Ideal for prototypes, small apps, learning AWS, or workloads that don't yet need separation of concerns.

![Monolithic Single-Tier Design](Monolithic%20single%20tier%20design.drawio.svg)

*(Vector SVG with embedded diagram data — zoomable on GitHub. Edit directly in diagrams.net by opening this file from the repo via File → Open → GitHub.)*

## Core Components

- **AWS Account / Region**: us-east-1 (multi-AZ for fault tolerance)
- **VPC**: Single VPC containing everything
- **Internet Gateway**: Attached to the VPC for public internet access
- **Public Subnets** (us-east-1a and us-east-1b): 
  - Host the Application Load Balancer (ALB)
  - Host EC2 instances launched by the Auto Scaling Group (simple setup; production often moves instances to private subnets)
- **Route Tables**: 
  - Associated with public subnets
  - Default route (0.0.0.0/0) points to the Internet Gateway (IGW)
- **Application Load Balancer (ALB)**: 
  - Distributes incoming traffic across healthy instances
  - Performs health checks
  - Placed across both subnets for AZ redundancy
- **Auto Scaling Group (ASG)**: 
  - Automatically launches and terminates EC2 instances ("servers")
  - Ensures minimum/desired/maximum capacity
  - Spread across AZs for high availability
- **EC2 Instances ("servers")**: 
  - Run the monolithic application
  - Grouped under the ASG

## Design Goals & Benefits

- **High Availability**: Multi-AZ setup — if one AZ fails, the ALB routes traffic to surviving instances in the other AZ.
- **Scalability**: ASG handles traffic spikes by adding instances automatically.
- **Simplicity**: Everything in one tier → fast to deploy and manage for early-stage apps.
- **Internet-Facing**: Direct public access via IGW and public subnets.

## Trade-Offs & Production Recommendations

- **Security**: Instances in public subnets get public IPs (larger attack surface).  
  → Recommended upgrade: Move ASG instances to private subnets + add NAT Gateway(s) for outbound internet.
- **Monolithic Nature**: Single deployable unit → scaling/failure affects the whole app.  
  → Future: Evolve to multi-tier (add RDS/DynamoDB) or microservices.
- **Missing in this basic design** (easy additions):
  - HTTPS termination + ACM certificate on ALB
  - Health check path on instances
  - CloudWatch monitoring & scaling policies
  - Route 53 DNS alias to ALB
  - AWS WAF for protection
  - Static assets offloaded to S3 + CloudFront

## How to View / Edit the Diagram

1. Click the SVG preview above (or open the file in this repo).
2. To edit: Go to https://app.diagrams.net → **File → Open → GitHub** → select this repository and file.
   - Loads fully editable thanks to embedded diagram data.

## Why This Design?

This is a foundational pattern for many real-world AWS web apps before adding complexity. It teaches key concepts: VPC networking, multi-AZ redundancy, load balancing, and auto-scaling — all in a minimal setup.

Feel free to fork, PR improvements, or use as inspiration!

Built with diagrams.net (draw.io) + AWS icons.  
Questions? Open an issue.
