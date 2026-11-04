# Managing Resources and Best Practices with Terraform 

## Learning Objectives 

- Describe how Terraform provisions and manages AWS resources
- Understand the importance of tracking and protecting state
- Identify common best practices: version control, state backends, and environment separation

## How Terraform Manages AWS Resources 

When you define AWS resources in `.tf` files and run `terraform apply`, Terraform communicates with the **AWS API** through the AWS provider. It sends **create, update, or delete** requests to manage resources based on the difference between: 

- The desired state (defined in your `.tf` files)
- The actual state (recorded in `terraform.tfstate`)

Terraform stores this state and updates it automatically after each change.

## Why the State File Matters

The `terraform.tfstate` file is central to how Terraform works. It includes metadata like: 

- Resource IDs
- Dependency graphs 
- Configuration values

Without an accurate state file, Terraform can't safely plan changes. For this reason:

- Preventing simultaneous edits (via **state locking**)
- Sharing infrastructure updates across team members
- Protecting state with versioning and ac cess control 

For AWS, the most common backend is an **S3 bucket** with optional state locking via **DynamoDB**.

## Notes on GitLab and Scalr 

You'll use **GitLab-managed state** to store and track Terraform infrastructure saefely. GitLab supports remote state storage and integrates with Terraform workflows through CI/CD pipelines. 

You may use **Scalr**, which provides a platform specifically designed for managing Terraform operations at scale. Scalr acts as a **remote operations backend**, offering features like centralized state storage, policy enforcement, team isolation, and enhanced governance. 

No matter the platform, the key goals to remote state remain the same: 

- Centralize state for collaboration 
- Use locking to avoid conflicts
- Protect state with versioning and access controls 
- Separate environments to minimize risk

## Best Practices Summary 

### Use Version Control 

Store your Terraform code in Git. Track changes like any other codebase. This supports code reviews and rollback if needed.

### Use Remote Backends in Teams

Avoid storing state locally when working with others. Use a remote backend (e.g., GitLab, S3, or Scalr) for shared access and locking. 

### Separate Environments

Avoid managing dev, staging, and prod from a single state file. Use **workspaces**, separate backends, or distinct directories/repos for environment isolation.

### Use Variables and Outputs

Avoid hardcoding values in `.tf` files. Use `variables` for config and `outputs` to surface important information (like VPC IDs or URLs).

### Lock Versions 

Pin provider and module versions in your code to prevent accidental upgrades:


```HCL
provider "aws" {
    version = ">= 5.0"
    region  = var.region
}
```

