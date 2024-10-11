# AWS Cloud Infrastructure Overview

This document explains the AWS cloud infrastructure shown in the architecture diagram. The setup includes multiple services hosted in both public and private subnets to manage a scalable, secure, and performant application.

## Architecture Components

The infrastructure is divided into two main subnets: **Public Subnet** and **Private Subnet**. Below, we describe each of these components in detail.

### VPC (Virtual Private Cloud)

- The entire infrastructure resides within an AWS Virtual Private Cloud (VPC). This VPC is segmented into different subnets:
    - **Public Subnet**: Resources that need direct access from the internet, like load balancers and S3, are placed here.
    - **Private Subnet**: This subnet hosts components that do not require direct internet access, such as EKS clusters, databases, and caching services.

### Public Subnet Components

1. **WAF (Web Application Firewall)**
    - AWS WAF is used to protect the application from web exploits by filtering and monitoring HTTP/S traffic.
    - It acts as a layer of defense against common threats such as SQL injection and cross-site scripting (XSS).

2. **CloudFront (CDN)**
    - Amazon CloudFront is used as a Content Delivery Network (CDN) to cache and deliver both static and dynamic content to users with low latency.
    - Incoming requests are first filtered through the WAF, and then passed on to CloudFront to cache and distribute content more efficiently.

3. **Application Load Balancer (ALB) with Ingress Controller**
    - The **ALB** is used for load balancing incoming application traffic across multiple services running in the private subnet (EKS).
    - The **Ingress Controller** ensures that requests are directed to the appropriate backend services running in EKS. This setup allows for load balancing, path-based routing, and SSL termination.

4. **Amazon S3**
    - Amazon S3 is used for storing static content such as images, CSS, and JavaScript files.
    - Requests for static content can be served directly by S3, thereby reducing the load on the backend application.

### Private Subnet Components

1. **Amazon EKS (Elastic Kubernetes Service)**
    - EKS is used for orchestrating containerized workloads.
    - It manages multiple microservices and automatically scales based on traffic demands.
    - The EKS cluster is not exposed to the internet directly for security purposes.

2. **Karpenter**
    - **Karpenter** is an open-source cluster autoscaler for Kubernetes that automatically adjusts the number of nodes in an EKS cluster based on workload requirements.
    - It ensures efficient scaling of the infrastructure, providing cost savings and performance improvements.

3. **Amazon RDS (MySQL)**
    - Amazon RDS MySQL is used as a relational database to store structured data.
    - It provides managed database capabilities such as backup, replication, and automatic failover.

4. **Amazon DynamoDB**
    - DynamoDB is used for NoSQL data storage needs, especially for unstructured or rapidly changing data.
    - It can be used to store application state, configurations, and session data.

5. **Amazon ElastiCache (Redis)**
    - ElastiCache using Redis is utilized as a caching layer to improve application performance.
    - It helps in caching frequently accessed data, thus reducing the latency and load on databases like RDS and DynamoDB.

## Traffic Flow Overview

1. **Incoming Traffic**: All incoming requests first hit **AWS WAF** to filter out any malicious or unwanted traffic.
2. **CloudFront**: The filtered requests are then routed to **Amazon CloudFront**, which delivers cached static content to users from edge locations.
3. **Dynamic Content**: For dynamic content requests, **CloudFront** forwards them to the **ALB**.
4. **Application Load Balancer (ALB)**: ALB then distributes the incoming requests to the appropriate services running in **Amazon EKS**.
5. **Static Content from S3**: Any requests for static resources (like images or scripts) are handled by **Amazon S3**.

## Data Flow Overview

- The application running in **EKS** can interact with both **Amazon RDS MySQL** for relational data and **Amazon DynamoDB** for NoSQL data.
- To improve performance, **ElastiCache Redis** is used to cache frequently accessed data, reducing latency and ensuring quicker response times.

## Security Considerations

- The **Public Subnet** contains only those components that need direct access from the internet.
- Components like the **EKS cluster**, **RDS**, and **ElastiCache Redis** reside in the **Private Subnet**, making them inaccessible directly from the internet for added security.
- **WAF** is used to prevent malicious traffic from reaching the application, mitigate DDoS attacks, and protect against common web exploits, rate limiting rules, and IP blacklisting.
- Using **CloudFront** helps mitigate DDoS attacks and ensures global content distribution.

## Scalability Considerations

- **Amazon EKS** combined with **Karpenter** ensures that the infrastructure can scale according to incoming traffic demands.
- The use of **ALB** and **CloudFront** allows efficient load distribution and content caching, respectively.

## Conclusion

This architecture offers a robust and scalable solution for hosting an application in AWS, providing improved performance, security, and the ability to handle both static and dynamic content effectively. The combination of WAF, CloudFront, ALB, EKS, and caching mechanisms ensures a secure and fast user experience.

