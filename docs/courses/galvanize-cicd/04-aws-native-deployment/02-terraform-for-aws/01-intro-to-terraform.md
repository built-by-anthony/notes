# Intro to Terraform 

## Learning Objectives 

- Explain what Terraform is an why it's used in modern DevOps worklows
- Describe how Terraform supports public cloud providers like AWS
- Compare Terraform to AWS CloudFormation at a high level 

## What is Terraform? 

Terraform is a tool for Infrastructure as Code (IAC) that allows you to provision, manage, and update cloud infrastructure using a declarative configuration language called HCL.

It allows you to define the desired state of your infrastructure and then automatically bring that state into reality by provisioning the necessary resources. 

Rather than manually configuring resources through cloud provider dashboards, Terraform lets teams codify infrastructure. This brings benefits like: 

- Repeatability and consistency 
- Version control of infrastructure changes
- Reduced risk of human error 
- Automation and faster deployment cycles

## Use Cases 

Terraform is widely used across the software industry to: 

- Provision multi-tier applications (web servers, databses, load balancers)
- Manage Kubernetes clusters and VPCs
- Create ephemeral (temporary) test or demo environments
- Support multi-cloud and hybrid infrastructure strategies
- Enable self-service infrastructre for developers 

## Why Choose Terraform? 

Terraform works across many plarforms. This provider model makes it highly flexible nad portable, especially useful for organizations with hybrid or multi-cloud architectures. 

