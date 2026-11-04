# Workflows

## AWS Services in CI/CD Workflows

## Learning Objectives

- Describe how AWS services are provisioned and configured using Terraform and GitLab CI/CD
- Explain the role of IAM roles and short-lived credentials in automated pipelines
- Recognize common deployment patterns involving AWS services in cloud-native applications.

## Introduction 

You'll learn how cloud infrastructure is automated using Terraform, GitLab CI/CD, and AWS IAM - and how services like EC2, Lambda, and S3 are deployed, configured, and secured through pipeline automation. 

## Provisioning with Terraform in Pipelines 

Terraform is the primary tool used to provision AWS infrastructure in this course. It allows teams to define infrastructure in code, store it in version control, and execute it automatically from CI/CD jobs. 

A typical GitLab CI/CD pipeline might include: 

- A `plan` stage to preview Terraform changes
- An `apply` stage to create or update resources in AWS
- A `destroy` stage to tear down environment when they're no longer needed

Terraform filfes are executed form GitLab runners, and the AWS resources are created dynamically using configuration variables defined in `.tfvars` or in pipeline CI/CD variables. 

## Secure Access with IAM and OIDC

To execute Terraform commands that affect AWS infrastructure, the GitLab runner must be authroized. Rather than hardcoded credentials, we use short-live credentials provided by AWS STSs and assumed via OpenID (OIDC). 

- GitLab runners act as **federated identities** 
- AWS verifies the GitLab identity using a **trust policy**
- If the token is valid, AWS issues temporary credentials for the configured IAM role
- The Terraform job uses these credentials to perform authorized actions 

This avoids long-lived secrets, minimizes risk, and keeps infrastructure automation aligned with best practices.



