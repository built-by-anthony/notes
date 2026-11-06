# What is a component? 

## Learning Objectives

- Explain what a GitLab pipeline component is and how it fits into a CI/CD workflow. 

## What is a Pipeline Componenet? 

A **pipeline componenet** in GitLab is a reusable piece of a CI/CD pipelien that defines one or more jobs in a standardized, configurable format. Componenets are designed to: 

- Promote **reuse** 
- Reduce **duplication**
- Make pipelines easier to manage 

Think of a component like a **function or module in code**

- It performs a specific task (e.g., compiile, run tests, linkt code, deploy)
- It has **inputs** that make it configurable
- It can be included in multiple piplines with consistent behavior

## Benefits of Using Components

CI/CD **componenets** introduce a more **modular, reusable, and self-contained** approach:

- **Encapsulation** - Comonenets define (`inputs:`) and enscapulsate logic, making pipelines more structured.

- **Better Reusability** - Instead of copy-pasting YAML, componenets provide an API-like interface for CI/CD logic. 

- **Versioning Support** - Components can be vesrioned and included in multiple pipelines reliability. 

- **Encourages Containzerization** - Rather than relying on external script repositories, components encourage bundling logic into images. 

Componenets bring a **modular, API-like appraoch** to GitLab CI/CD, reducing duplication, improving reusability, and making pipelines more scalable. 

Using components helps:

- Reduce boiler plate across `.gitlab-ci.yml` files
- Ensure **consistency** across projects 
- Make pipelines easier to **maintain**
- Enable teams to **consume** shared, tested CI logic.

## How Componenets are Used 

In the typical GitLab pipline: 

- Developers use ` include: componenet:` to bring in reusable, versioned pipeline logic. 

- Each componenet defines *inputs* (like script command options, environment variables, or flags)

- A full pipeline is composed by combining multiple components, each responsible for a specific stage of the CI/CD process. 

### Use cases 

Componenets in your organization's CI Catalog will typically cover the most common use cases for pipeline configurations. They can range from single functionality definitions to nest full end-to-end pipelines. SOme examples include: 

- **Build**
    - Build Java Projects with Maven or Gradle
    - Produce static files for Node projects (`/build`, `/dist`)
    - Build Docker images

- **Test**
    - Run jUnit tests
    - Run Node-based test suits

- **Application Security**
    - Run Static Application Code Security Tests (SAST)
    - Dependency Scanning 
    - Container Scanning 

- **Deployment**
    - GitOps deployment 
    - Terraform configuration applies

- **Utilitites**
    - Cloud Credential Injection 
    - Secure Credential Retrieval (e.g., from AWS Secrets Manager)
    - CLU Utilitites (e.g., `kubectl`, `aws`, `jq` helpers)

Each project includes only the components it needs and configures them using project-specific inputs. This helps teams avoid repeating logic while staying aligned with organizational standards. 

| **Feature** | **Decription** | 
|-------------|----------------|
| `componenet.yml` | Defines metadata, inputs, and job behavior | 
| **Single-responsibility** | Each Componenet focuses on one task | 
| **Versioned** | Referenced by tag or commit SHA | 
| **Reusable** | Can be used across projects | 
| **Composable** | Multiple components from a complete pipeline| 

GitLab CI/CD Componenets are simply pre-defined pipelines configurations that you can include in your own pipeline.
