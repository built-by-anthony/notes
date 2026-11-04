# Infrastructure

## Cloud Foundations and AWS Global Infrastructure

## Learning Objectives 

- Describe how AWS organizes its global infrastructure using Regions, Availability Zones (AZs), and Edge Locations 

- Explain hoe these infrastructure layers support high availability, scalability, ad performance. 

## Introduction 

Before you do anything in the cloud you must choose where it will live. In AWS, ***where*** means much more than simply selecting a data center - it means selecting from a hierarchy of global infrastructure layers designed for reliability, security, and low-latency access.

Understanding how AWS organizes its infrastructure is key to designing resilient and performant sytems. It also plays a role in cost optimization, compliance, and disaster recovery planning. This lesson explores the core physical ayers of AWS: **Regions, Availability Zones,** and **Edge Locations.**

## AWS Regions

An **AWS Region** is a geographically distinct area where AWS clusters its infrastructure. Each Region contains multiple **Availability Zones**, and is designed to be isolated from other Regions for fault tolerance and data sovereignty.

### Key charactersitics 

- Regions are **isolated from each other** to prevent localized failures from impacting global workloads

- Customers can choose Regions based on **compliance, latency,** or **cost**

- Some AWS services are **global** (e.g., IAM), but most are **Region-specific**

## Availability Zones (AZs)

An **Availability Zone** is one or more discrete data centers within a Region. Each AZ has its own power, networking, and cooling but is connected to other AZs in the Region via low-latency links.

When AWS says a Region has three AZs, it means there are three physically separate locaitons that can indepdenently run workloads, while still being close enough to coordinate traffic and replication.

### Why it matters

- AZs provide **high availability and fault tolerance**

- By spreading applications across multiple AZs, you reduce the risk of service interruption

- Many AWS services (like RDS or ELB) are designed to work **across AZs automatically**

## Edge Locations 

**Edge locations** are AWS data centers designed to serve content as close to end users as possible. These are not full Regions of AZs - they're much smaller, geographically distributed facilities used by services like: 

- **CloudFront** (CDN): caches static content at the edge
- **Route 53** (DNS): routes users to the lowest-latency endpoint
- **AWS Shield & WAF**: block malicious traffic as close to the source as possible

Edge Locations are crucial to **global performance** and **user experience**, especially for frontend assets, media, or APIs exposed over the public internet.

### Key differences 

- Edge Locations are **optimized for read performance** and fast response 
- They're used in **content delivery and DNS resolution,** not for running general workloads
- There are **far more Edge locations** than AZs or Regions - hundreds around the globe

## Summary 

AWS organizes its infrastructure to be both **globally scalable** and **locally resilient.** Regions provide geographic isolation. Availability Zones allow for redundancy within region. Edge Locations deliver fast content delivery to users around the world. Understanding this hierarchy helps you make better architectural decisions, especially when designing for high availability, performance, and compliance.