# CI/CD Catalog 

## Learning Objectives 

- Understand what the CI/CD componenet catalog is, how to explore it, and how to find the path and verison needed to include components in a pipeline. 

## What Is The Component Catalog? 

The **CI/CD Componenet Catalog** is a shared GitLab respository or interface that stores reusable, versioned pipeline components. These components are organized by purpose or language and are amde available to all teams working in the same GitLab instance. 

Think of the catalog as your organization's **shared library of reusable CI/CD logic**

## Why Use It? 

- Ensures consistency across projects 
- Promotes reuse of best practices
- Saves time by providing prebuilt pipeline steps 
- Simplifies onboarding for new teams

## Viewing a Component's Details 

When you click a component in the catalog, you'll be taken to a detailed view with tabs, typically including: 

- A **README** - Documentation that describes what the componenet does, how to include it, and how to configure it.

- A view of the component's `spec:` block, showing available `inputs`, default values, and descriptions. 

## How to Use a Component from the Catalog

To include a component, you need

1. The **component path** (e.g., `ci-catalog/docker/docker-build-push`)
2. The **version**: `@~latest`, or an exact version listed in the catalog (e.g., `@1.0.2`) 
3. Any required **inputs** 

```yaml
include:
  - component: ci-catalog/docker/docker-build-push@~latest
    inputs: 
      dockerfile_name: "Dockerfile"
      build_context: "."
      iamge_tag: "latest"
```

## Summary 

- The **CI/CD component catalog** stores reusable pipeline components for your team organization. 

- You'll explore the catalog to locate the **component path**, **version**, and **inputs**.

The **README** and **Component tabs** show you how to use each component correctly. 

