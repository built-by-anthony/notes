# Terraform Workflow

## Learning Objectives 

- Identyify the four main stages of the Terraform workflow: `init`, `plan`, `apply`, and `destroy`
- Describe what each command does and when it is used
- Explain how this workflow supports safe, declarative infrastructure provisioning 

## What is Declarative Provisioning? 

Terraform is a **declarative tool**, which means you describe ***what*** you want - not ***how*** to do it.

This model is powerful because it allows Terraform to detect and manage differences between the **desired state** (your `.tf` file) and the **actual state** (what exists in AWS), and the make only the necessary changes. 

## Terraform Workflow Overview

1. `terraform init` 

    Initializes the working directory. Downloads the appropriate provider plugin(s), sets up the backend for state storage, and prepares your configuration for execution. 

    ```bash
    terraform init
    ```

    Use this command **once per project or whenever you change providers or backends.**

2. `terraform plan`

    Analyzes the configuration and compares the desired state (from your `.tf` files) with the current infrastructure (from the state file and provider). It outputs an **execution plan** showing what Terraform will do - create, change, or destroy resources. 

    ```bash
    terraform plan 
    ```

    This is your **preview step** - no changes are made. It's best practice to run this before every apply. 

3. `terraform apply`

    Applies the changes proposed in the `plan` step. Terraform sends the necessary API requests to the cloud provider to create, modify, or delete infrastructure to match the declared state.

    ```bash
    terraform apply
    ```

    Terraform will prompt you to confirm before proceeding unless you pass the `-auto-approve` flag.

4. `terraform destroy`

    Removes all infrastructure defiend in your configuration. This is helpful for cleaning up development or temporary environments.

    ```bash
    terraform destroy
    ```

    Use with caution. This command can be destructive if run unintentionally. 







