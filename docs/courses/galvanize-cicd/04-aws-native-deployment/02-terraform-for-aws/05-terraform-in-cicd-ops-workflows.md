# Terraform in CI/CD and GitOps Workflows

## Learning Objectives

- Describe how Terraform integrates into a CI/CD pipeline
- Explain how Terraform supports GitOps workflows for infrastructure
- Understand how Terraform complements Kubernetes and ArgoCD

## Terraform and CI/CD 

Terraform can be executed **manually** from the CLI, or it can be automated inside a **CI/CD pipeline** such as GitLab CI. In this approach: 

1. Configuration files are stored in Git
2. Terraform runs are triggered by pipeline stages
3. Changes are reviewed in merge requests before being applied
4. Infrastructure is treated just like application code

This enables **reviewable, testable, and auditable** infrastructure changes - a key goal in DevOps.

Typical GitLab stages for Terraform might include: 

- `terraform fmt` for formatting 
- `terraform validate` to check config syntax
- `terraform plan` to preview changes
- `terraform apply` (manual approval) to provision 

## Terraform and GitOps

**GitOps** is a workflow where **Git is the source of truth**, and **all changes happen through pull requests**. Terraform supports GitOps because: 

- It is fully declarative 
- Infrastructure is defined in code and stored in Git 
- State changes can be tracked and reviewed 
- Environments can be managed consistently across teams 

With GitOps, a merge to the main branch **triggers a pipeline**, which then applies the infrastructure changes using Terraform - muc like ArgoCD applies Kubernetes manifests. 

## Terraform and ArgoCD

Terraform and ArgoCD serve **complementary roles**:

- **Terraform** provisions infrastructure: VPCs, databases, load balancers, IAM roles, etc. 

- **ArgoCD** deploys applications into Kubernetes clusters using GitOps 

In a GitOps workflow: 

- Terraform might create the cluster, networking, and cloud resources
- ArgoCD deploys your application ***into*** the cluster using manifests stored in Git

You might even use Terraform to provision the ArgoCD setup itself. 

## Putting It All Together

Here's how it might look in a full GitLab -> Terraform -> Kubernetes -> ArgoCD pipeline:

1. Developer pushes changes to `.tf` files
2. GitLab pipeline runs `terraform plan` in a preview job 
3. An engineer approves and merges to main 
4. GitLab runs `terraform apply` to provision infrastructure
5. ArgoCD notices updated application or deployment manifests and syncs them to the cluster.

