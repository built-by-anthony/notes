# Pipeline Variables

## Learning Objectives

- Identify different use cases for vartiables in a GitLab CI/CD pipeline. 

## Overview of Variable Use

This lesson introduces common types of variables and how they are used. 

### Defining Variables in `.gitlab-ci.yml`

Variables can be defined directly in the pipeline file using the `variables:` keyword. 

### Other Ways to Define Variables

- **Project CI/CD Variables**:  Defined in the project's **Settings > CI/CD** section. Commonly used for secrets, tokens, or other configuration values you don't want to hard code.

- **Group CI/CD Variables**: Defined at the group or subgroup level. Useful when multiple repositories share common settings like credentials for deployment environments. 

- **Instance CI/CD Variables**: Available only in self-managed GitLab instances. These provide global variables the entire GitLab installation. 

## Dynamic Variable Setting

You can define variables dynamically during a pipeline run and pass them between jobs. This is especially helpful when a value is **only** known at runtime. 

If you're familiar with `.env` files, GitLab provides a similar feature. You can use the `artifacts:reports:dotenv` keyword to export environment variables from one job and make them available to another. 

## Predefined Environment Variables 

GitLab provides a rich set of **predefined variables** that are **automatically** available during each pipeline run.

Here are a few key examples:

1. `CI_PROJECT_DIR`
    - **Purpose**: Full path to the directory where the pipeline runs.
    - **Usage**: Used by tools like Kaniko for `--context` and `--dockerfile`.
    - **Example**: `/builds/group-name/project-name`

2. `CI_REGISTRY_IMAGE`
    - **Purpose**: Full image path in GitLab's container registry.
    - **Usage**: Used as the `--destination` target when pushing with Kaniko.
    - **Example**: `registry.gitlab.galvanize.com/group-name/project-name`

3. `CI_PIPELINE_IID`
    - **Purpose**: Unique ID for each pipline run within the project.
    - **Usage**: Useful for tagging images or tracking deployments. 
    - **Example**: `12345`