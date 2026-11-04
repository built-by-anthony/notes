# IAM 

## Identity and Access Management (IAM)

## Learning Objectives 

- Explain the role of IAM in securing AWS services
- Identify key IAM componenets such as users, groups, roles, and policies 
- Describe how IAM enables automation with short-lived credentials via STS and GitLab OIDC

## Introduction 

AWS Identity and Access Management (IAM) is the core service that enables you to manage who can access with AWS resources, and under what conditions. 

## IAM Components 

IAM allows you to manage identities and their permissions. It follows a common pattern used across cloud providers: 

- **Users** represent individual people and have long-term credentials
- **Groups** are collections of users with shared permissions
- **Policies** are JSON documents that define allowed or denied actions
- **Roles** are assumed by trusted entities (users, services, or external identities) and include temporary credentials
- **Trust policies** define who is allowed to assume a given role

These componenets allow organizations to apply the principle of least privilege - granting only the access that is needed, for the time it's needed.

## IAM and Developers

While IAM users are sometimes created for developers in learning environments, they are not commonly used in production. Instead, access is granted through roles that are assumed temporarily. This prevents long-lived credentials from being exposed and simplifies key rotation and auditing. 

When deploying infrastructure through GitLab CI/CD, we don't want to embed AWS credentials directly into the pipeline. Instead, we configure the pipeline to assume a role using temporary, short-lived credentials. 

## Temporary Credentials with STS

The AWS Security Token Service (STS) provides temporary credentials for IAM roles. These credentials expire automatically after a defined duration and reduce the risk of credential leakage.

STS is particularly important when integrating AWS with external systems - like GitLab. Instead of storing access keys, you can use STS to generate credentials on demand during a pipeline run. 

## GitLab OIDC and IAM Roles for CI/CD

The preferred way to allow GitLab pipelines to access AWS is through **OpenID Connect (OIDC)**. GitLab can act as an identity provides that AWS trusts. This setup avoids the need to create IAM users or store long-lived secrets. 

Here's how it works: 

1. A GitLab runner requests a token from GitLab during a pipeline
2. AWS verifies the token against the trust policy of a preconfigured IAM role 
3. If valid, AWS issues temporary credentials scoped to that role
4. The pipeline uses those credentials to provision or modify AWS resources

This appraoch is secure, scalable, and well-suited to cloud-native, GitOps-style workflows. 

## Summary 

IAM enables secure access to AWS services through a flexible set of identites, policies, and roles. For develoeprs and automation pipelines, roles and short-lived credentials are the foundation of secure access. In modern CI/CD workflows, tools like GitLab integrate with AWS IAM using OIDC to safely assume roles at runtime - eliminating the need for hardcoded keys and improving security posture. 