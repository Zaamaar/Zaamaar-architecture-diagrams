# Zaamaar-architecture-diagrams
# Monolithic Single-Tier AWS Architecture

A highly available, scalable **single-tier monolithic** web application deployed on AWS using a single VPC across multiple Availability Zones.

This design is ideal for:
- Simple web apps or MVPs where all logic (UI, business rules, data access) lives in one codebase/deployable unit.
- Learning AWS fundamentals (VPC, EC2 Auto Scaling, Load Balancing, multi-AZ HA).
- Small-to-medium traffic workloads before evolving to microservices or multi-tier.

![Monolithic Single-Tier AWS Design](monolithic-single-tier-design.drawio.svg)

*(Click the image above to zoom — vector format for crisp details. Edit in diagrams.net/draw.io by opening the file directly from this repo.)*

## Architecture Overview

- **Region**: us-east-1
- **VPC**: Single VPC spanning multiple AZs (us-east-1a & us-east-1b) for high availability
- **Internet Gateway**: Provides public internet access
- **Public Subnets** (one per AZ): Host the Application Load Balancer (ALB)
- **Auto Scaling Group (ASG)**: Launches EC2 instances running the monolithic app (e.g., Node.js, Python Flask/Django, Java Spring, PHP, etc.)
  - Instances placed in **public subnets** (simple setup; for production, move to private subnets + NAT)
  - Scaling based on CPU/metrics or scheduled
- **Application Load Balancer (ALB)**: Distributes traffic across AZs, performs health checks, terminates HTTPS (via ACM cert)
- **Security Groups**: Least-privilege rules (ALB allows 80/443 from anywhere; instances allow app port only from ALB)
- **Other assumed components** (not shown but recommended):
  - Route 53 for DNS
  - CloudWatch for monitoring & alarms
  - Optional: CloudFront CDN for static assets

## Key Features & Benefits

- **High Availability**: Multi-AZ deployment — if one AZ fails, traffic routes to healthy instances in the other AZ.
- **Auto Scaling**: Automatically adds/removes EC2 instances based on demand.
- **Elasticity**: Handles variable traffic without manual intervention.
- **Simplicity**: Single codebase, single deployment unit — fast iteration for startups or prototypes.
- **Cost-Effective Starter**: Minimal services (no separate DB tier yet; could use local DB or attach RDS later).

## Trade-Offs & Limitations

- **Single Point of Failure Risk**: Entire app scales or fails together (monolithic nature).
- **Scaling Inefficiency**: Can't scale individual components (e.g., heavy DB queries affect everything).
- **Tight Coupling**: Hard to update parts independently or adopt different tech stacks.
- **Security**: If instances are in public subnets (as shown), they have public IPs → use private subnets + NAT Gateway in production.
- **Evolution Path**: Good foundation — later refactor to multi-tier (add RDS, ElastiCache) or microservices.

## How to View/Edit the Diagram

1. Open this repo in GitHub.
2. Click the file: `monolithic-single-tier-design.drawio.svg`
3. To **edit**: Go to https://app.diagrams.net → File → Open → GitHub → select this repo/file.
   - The diagram loads fully editable (thanks to embedded XML data).

## Next Steps / Improvements

- Move EC2 instances to **private subnets** + add NAT Gateway(s) for outbound internet.
- Add **Amazon RDS** (Multi-AZ) or DynamoDB for persistent data.
- Enable **HTTPS** with AWS Certificate Manager (ACM) + ALB redirect.
- Attach **AWS WAF** to ALB for basic protection.
- Add **CloudFront** for caching/static content delivery.
- Monitor with CloudWatch + set scaling alarms.
- Explore **diagram-as-code** (e.g., Python Diagrams library) for programmatic updates.

## License

MIT — feel free to use, modify, and share.

Questions or suggestions? Open an issue or PR!

Built with ❤️ using diagrams.net (draw.io) and AWS icons.
