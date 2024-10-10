# Microservices AWS Architecture for PHP8 Application

## Table of Contents
1. [Overview](#overview)
2. [Architecture Components](#architecture-components)
3. [Deployment Steps](#deployment-steps)
4. [Security Considerations](#security-considerations)
5. [Scalability and Performance](#scalability-and-performance)
6. [Monitoring and Logging](#monitoring-and-logging)
7. [Disaster Recovery](#disaster-recovery)
8. [Cost Optimization](#cost-optimization)

## Overview

This document outlines a comprehensive microservices-based AWS architecture for deploying a PHP8 application. The architecture is designed to provide high availability, scalability, security, and performance while meeting the following requirements:
- MySQL for relational data storage
- Redis for session storage and caching
- DynamoDB for key-value storage
- Efficient solution for large file downloads and static content delivery
- Application code hosted on GitHub
- Local development using Docker and docker-compose

## Architecture Components

### 1. Virtual Private Cloud (VPC)
**Purpose**: Provides a logically isolated section of the AWS Cloud for secure deployment of resources.

**Configuration**:
- Public and private subnets across multiple Availability Zones (AZs) for high availability and fault tolerance.
- NAT Gateways in public subnets for outbound internet access from private subnets.

**Rationale**: VPC allows for network isolation and segmentation, enhancing security and providing control over network architecture. Multiple AZs ensure high availability and resilience against datacenter failures.

**Reference**: [Terraform AWS: High Availability VPC & EKS kubernetes](https://www.youtube.com/watch?v=zaBsF-YNQ1o)

### 2. Amazon EKS (Elastic Kubernetes Service)
**Purpose**: Orchestrates and manages the deployment of microservices.

**Configuration**:
- Cluster placed in private subnets for enhanced security.
- Integrated with ECR for secure container image storage.
- Karpenter for efficient auto-scaling of worker nodes.

**Rationale**: EKS provides a managed Kubernetes platform, reducing the operational overhead of running Kubernetes. It offers seamless integration with other AWS services and provides a scalable, highly available environment for microservices.

**Reference**: [AWS EKS Setup with Terraform: Kubernetes, Ingress, Karpenter & Metrics Server](https://www.youtube.com/watch?v=0QMLviWD7Qs)

### 3. Application Load Balancer (ALB) with Ingress Controller
**Purpose**: Distributes incoming application traffic across multiple microservices.

**Configuration**:
- ALB Ingress Controller for dynamic routing based on Kubernetes Ingress resources.
- Configured with HTTPS listeners for secure communication.

**Rationale**: ALB with Ingress Controller provides intelligent routing capabilities, SSL termination, and seamless integration with EKS. It allows for efficient traffic distribution and enables advanced routing strategies.

**Reference**: [AWS WAF for Kubernetes EKS: ALB Ingress & ASG with ALB](https://www.youtube.com/watch?v=uMqS6Co_1G4)

### 4. Amazon RDS for MySQL
**Purpose**: Provides a managed relational database for the application's structured data storage needs.

**Configuration**:
- Multi-AZ deployment for high availability and automatic failover.
- Placed in private subnets for enhanced security.
- Automated backups and maintenance.

**Rationale**: RDS offers a fully managed MySQL database, reducing administrative tasks while providing high availability, automated backups, and easy scalability. It's ideal for structured data that requires ACID compliance.

**Reference**: [Terraform V-12: AWS RDS MySQL Tutorial for Beginners](https://www.youtube.com/watch?v=WqrdbobQIKc)

### 5. Amazon ElastiCache for Redis
**Purpose**: Offers in-memory caching to improve application performance and handle session storage.

**Configuration**:
- Deployed as a Redis Sentinel cluster for high availability.
- Placed in private subnets for security.

**Rationale**: ElastiCache provides sub-millisecond latency for data retrieval, making it ideal for session storage and caching frequently accessed data. This reduces load on the primary database and improves application response times.

**Reference**: [Kubernetes Redis High-Availability Cluster: Step-by-Step Guide to Setup Redis Sentinel](https://www.youtube.com/watch?v=eVa1Hbxice8)

### 6. Amazon DynamoDB
**Purpose**: Provides a fully managed NoSQL database for key-value and document storage.

**Configuration**:
- Global tables for multi-region redundancy.
- Auto-scaling enabled for read and write capacity.

**Rationale**: DynamoDB offers millisecond response times, infinite scalability, and a serverless model. It's ideal for high-volume data that doesn't require complex relationships, such as user profiles, session data, or real-time analytics.

### 7. Amazon S3 with CloudFront
**Purpose**: Stores and serves large files and static assets (e.g., images, CSS, JavaScript) with low latency globally.

**Configuration**:
- S3 buckets with versioning and lifecycle policies.
- CloudFront distribution with S3 as origin.
- HTTPS enforced and geo-restriction as needed.

**Rationale**: S3 provides durable, scalable object storage, while CloudFront acts as a Content Delivery Network (CDN). This combination ensures fast, secure delivery of static assets and large files to users worldwide, reducing load on application servers and improving user experience.

**Reference**: [Terraform Cloudfront with OAI: Proxy Your S3 Private Bucket Effortlessly!](https://www.youtube.com/watch?v=RU7dzWXXDzA)

### 8. AWS WAF (Web Application Firewall)
**Purpose**: Protects the application from common web exploits and attacks.

**Configuration**:
- Associated with ALB and CloudFront.
- Rule sets for SQL injection, cross-site scripting (XSS) prevention, and other common attacks.

**Rationale**: WAF adds an essential layer of security, filtering malicious web traffic before it reaches the application. This helps prevent attacks, reduces the risk of data breaches, and ensures application availability.

**Reference**: [AWS WAF for Kubernetes EKS: ALB Ingress & ASG with ALB](https://www.youtube.com/watch?v=uMqS6Co_1G4)

## Deployment Steps

1. **Set up VPC and Networking**
   - Use Terraform to create VPC with public and private subnets across multiple AZs.
   - Set up Internet Gateway, NAT Gateways, and route tables.

2. **Deploy EKS Cluster**
   - Use Terraform to create EKS cluster in private subnets.
   - Set up Karpenter for auto-scaling.

3. **Configure Security Groups and NACLs**
   - Create security groups for ALB, EKS nodes, RDS, ElastiCache, and other components.
   - Set up NACLs for additional subnet-level security.

4. **Set up Database and Caching Layers**
   - Deploy RDS MySQL instance in private subnets using Terraform.
   - Create ElastiCache Redis Sentinel cluster in private subnets.
   - Set up DynamoDB tables with required indexes.

5. **Configure S3 and CloudFront**
   - Create S3 buckets for file storage and static assets.
   - Set up CloudFront distribution with S3 as origin using Terraform.

6. **Deploy Application Load Balancer and Ingress Controller**
   - Set up ALB Ingress Controller in EKS.
   - Configure listeners and target groups for microservices.

7. **Implement CI/CD Pipeline**
   - Set up GitHub Actions for CI with OIDC integration for AWS ECR.
   - Implement ArgoCD for continuous deployment to EKS.

   **CI with GitHub Actions and OIDC Integration:**
   - Configure GitHub Actions workflows to build and push Docker images to Amazon ECR.
   - Use OpenID Connect (OIDC) for secure, token-based authentication between GitHub Actions and AWS.
   - Implement Terragrunt for managing multi-environment infrastructure as code.

   **Benefits:**
   - Enhanced security by eliminating the need for long-lived AWS credentials.
   - Streamlined CI process with automatic pushing of images to ECR.
   - Simplified management of multi-environment deployments with Terragrunt.

   **Reference**: [Terragrunt GitHub Actions OIDC Integration with AWS ECR](https://www.youtube.com/watch?v=jLFR7tVChwA)

   **CD with ArgoCD:**
   - Set up ArgoCD in the EKS cluster for GitOps-based deployments.
   - Configure ApplicationSets for managing multiple similar applications.

   **Reference**: [ArgoCD ApplicationSets kubernetes](https://www.youtube.com/watch?v=pkzth8gvVWA)

8. **Configure Monitoring and Logging**
   - Set up Prometheus and Grafana for monitoring and alerting.
   - Implement ELK stack or Grafana Loki for centralized logging.
   - Configure Slack integration for alerts.
     **References**:
      - [Kubernetes Monitoring with Lens | Application Alerting on Slack via Prometheus & Grafana](https://www.youtube.com/watch?v=bEIcYqiyfHU)
      - [FinOps Effortless GKE Kubernetes Logs Costs Reduction with Grafana Loki](https://www.youtube.com/watch?v=A8_T-w6t-wQ)
      - [Cut Logging Costs with ELK Stack & Fluent-Bit on EKS!](https://www.youtube.com/watch?v=Q6_qZ06dUts)

9. **Implement Security Measures**
   - Enable AWS WAF and configure rule sets.
   - Implement AWS Shield for DDoS protection.
   - Set up HashiCorp Vault for secrets management.
     **Reference**: [HashiCorp Vault HA TLS Setup on Kubernetes with Secrets Injection in Application Pods](https://www.youtube.com/watch?v=8GPsE90ZlUg)

## Security Considerations

- Implement IAM roles with least privilege access for all components.
- Use IRSA (IAM Roles for Service Accounts) in EKS for fine-grained permissions.
- Enable encryption at rest for all data stores (RDS, S3, EBS volumes).
- Implement network policies in EKS for inter-service communication control.
- Regularly update and patch all systems, including EKS nodes and containers.
- Implement Istio for enhanced security and traffic management.
  **Reference**: [EKS Istio Ambient Mesh L4 (Ztunnel) | End-to-End Encryption](https://www.youtube.com/watch?v=23a6SD-K_L8)

## Scalability and Performance

- Utilize Karpenter for efficient EKS cluster auto-scaling.
- Implement horizontal pod autoscaling (HPA) for individual microservices.
- Use ElastiCache Redis for caching and session management to reduce database load.
- Implement read replicas for RDS to offload read traffic.
- Use CloudFront for global content delivery and API acceleration.
- Optimize Kubernetes resource requests and limits for efficient resource utilization.

## Monitoring and Logging

- Set up Prometheus and Grafana for comprehensive Kubernetes monitoring.
- Use CloudWatch Container Insights for EKS monitoring.
- Implement distributed tracing with AWS X-Ray or Jaeger for microservices observability.
- Set up alerts for critical metrics across all components, with Slack integration.
- Use AWS CloudTrail for auditing and compliance.
- Implement Grafana Loki or ELK stack for centralized logging:
  **Reference**: [Cut Logging Costs with ELK Stack & Fluent-Bit on EKS!](https://www.youtube.com/watch?v=Q6_qZ06dUts)
- Use Lens for Kubernetes cluster management and monitoring with prometheus and grafana:
  **Reference**: [Kubernetes Monitoring with Lens](https://www.youtube.com/watch?v=bEIcYqiyfHU)

## Disaster Recovery

- Implement multi-region strategy for critical microservices.
- Use RDS Multi-AZ and read replicas for database failover and recovery.
- Set up S3 cross-region replication for important data.
- Implement regular backups for all stateful components.
- Create and regularly test a disaster recovery plan.
- Use Velero for Kubernetes cluster and persistent volume backups.

## Cost Optimization

- Use Spot Instances with Karpenter for non-critical workloads to reduce compute costs.
- Implement auto-scaling at the pod and node level to match capacity with demand.
- Use S3 Intelligent-Tiering for optimal storage costs based on access patterns.
- Regularly review and optimize resource allocation for all microservices.
- Utilize AWS Cost Explorer and Budgets for ongoing cost management and forecasting.
- Implement FinOps practices for Kubernetes cost management:
  **Reference**: [FinOps Effortless GKE Kubernetes Logs Costs Reduction](https://www.youtube.com/watch?v=A8_T-w6t-wQ)