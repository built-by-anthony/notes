# Terraform Configuration Anatomy

## Learning Objectives 

- Identify the main components of a Terraform configuration: providers, resources, variables, and outputs
- Understand how Terraform configuration is structured used `.tf` files and HCL syntax
- Exlpain teh role of the terraform state like (`terraform.tfstate`)

## Configuration Structure

Terraform configuration is written using **HCL**, and typically lives in files ending with `.tf`. These files declare the desired infrastructure. 

A typical configuration is madse up of **blocks** that define various components. These include: 

### Provider Block

The provider tells Terraform ***which cloud or service*** it will communit care. For AWS< it looks like this: 

```HCL
provider "aws" {
    region = "us-west-2"
}
```

Terraform will use this information to download the appropriate plugin and authenticate with the AWS API. 

### Resource Block

A resource block defines a single piece of infrastructure, such as an EC2 instance or an S3 bucket: 

```HCL
resource "aws_s3_bucket" "my_bucket" {
    bucket = "my-terraform-bucket"
    acl    = "private
}
```

Each resource as a **type** (`aws_s3_bucket`), a **name** (`my_bucket`), and one or more configuration arguments.

### Variable Block 

Variables allow your configuration to be flexible and reusable. You define them like this: 

```HCL
variable "region" {
    default = "us-west-2"
}
```

Then you can use them in other blocks: 

```HCL
provider "aws" {
    region = var.region
}
```

### Output Block 

Outputs let you display or export values after a Terraform run. These are often used to surface information like IP addresses or resource IDs: 

```HCL
output "bucket_name" {
    value = aws_s3_bucket.my_bucket.bucket
}
```

## State File (`terraform.tfstate`)

Terraform keeps track of the infrastructure it manages using a file called `terraform.tfstate`. This file st ores the **actual state** of the infrastructure, so Terraform can detect drift and apply only the necessary changes. 

By default, this file is stored locally. In team environments, it's common to store it in a remote backend (like S3) to allow collaboration and avoid conflicts. 

