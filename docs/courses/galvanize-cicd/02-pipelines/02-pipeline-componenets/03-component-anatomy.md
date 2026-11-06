# Componenet Anatomy 

## Learning Objectives 

- Identify and understand the structure of a GitLab componenet, including the `spec:` block and job body. 

- Explain how component inputs are defined and used within job definitions

## Anatomy of a GitLab Componenet 

A GitLab pipeline **componenet** is a reusable `.yml` file that defines a job or set of jobs, plus the configuration inputs it accepts. 

Each component consists of **two parts**, seperated by `---`: 

1. `spec:` **block** - declares the file as a component and defines inputs. 
2. **Job definition(s)** - one or more jobs that use the inputs. 

> **Note**: The `---` separator is required when both parts are defined in the same file. Without it, GitLab won't interpret the file as a valid component. 

Alternatively, you can split the component across **two separate YAML files**:

- A `componenet.yml` file containing the `spec:` block
- A `.gitlab-ci.yml` file containing the job definitions 

### Example: 

```yaml 
spec:
  inputs: 
    stage:
      type: string
      default: "package"
      description: "Stage to run the job in"
    
    image_tag:
      type: string
      default: "latest"
      description: "Tag to use for the build Docker image" 
    
    dockerfile_name:
      type: string
      default: "Dockerfile"
      description: "Directory to use as the Docker build context"
    
    build_context:
      type: "string"
      default: "."
      description: "Directory to use as the Docker build context"

---

cont-image-build-push:
  iamge: 
    name: gcr.io/kaniko-project/executor:v1.15.0-debug
    entrypoint: [""]
  stage: $[[ inputs.stage ]]
  script: 
    - kaniko/executor
        --context "${CI_PROJECT_DIR}/$[[ inputs.build_context ]]"
        --dockerfile "${CI_PROJECT_DIR}/$[[ inputs.dockerfile_name ]]"
        --destination "${CI_REGISTRY_IMAGE}:$[[ inputs.image_tag ]]"
        --cleanup
```

## Understanding the `spec:` Block 

The `spec:` block is what tells GitLab that this file is a **component**. 

The `spec:` block contains: 

| **Field** | **Purpose** | 
|-----------|-------------|
| `inputs`  | A collection of named input fields for configuring the component | 
| `default` | A fallback value if no input is provided | 
| `type`    | (Optional) Specifies the exepcted value type (e.g., `string`, `booleann`)|

Inputs are accessed in the job using: 

```bash
$[[ inputs-input-name ]]
```

## Why Use Inputs? 

Inputs make your componenet **configurable**. Instead of hardcoding values, you define **parameters** that other team members can override. 

Benefits: 

- **Reusable** across different projects
- **Maintainable** - update once, apply everywhere
- **Flexible** - works in many environments with different needs

## Summary 

When reviewing or writing componenets, look for: 

- A `spec:` block at the top (or in a separate `component.yml` file)
- An `inputs:` section with `type`, `default`, and `description`
- A `---` separator if using a single-file format 
- One or more job definitions using `$[[ inputs.name ]]`

This structure ensures your components are well-documented, flexible, and easy to use. 

