# Consuming Components 

## Learning Objectives 

- Consume pipeline components using the `include component:` syntax 

## Using a Component 

You use the `include:` keyword with `component:`

```yaml
include:
  - component: my-group/ci-catalog/java/java-build@~latest
    inputs:
      build-script: "./mvnw package -DskipTests"
```

This example: 

- Includes the `java-build` component from your catalog 
- Uses `~latest`, the newest version released to the catalog 
- Provides a custom `build-script` input 

## How It Works

When GitLab processes your pipeline: 

- It fetches the referenced component from the repo and version specified 
- It replaces input references (e.g., `$[[ inputs.build-script ]]`) in the component with the values you provide
- It adds the component's job(s) to your pipeline just like any other job

## Inputs and Configuration 

Each component has its own list of required and optional inputs. You can view these by looking at the `spec:` block in the component source file or catalog documentation.

Here's how you pass values:

```yaml
include:
  - component: my-group/ci-catalog/docker/docker-build-push@~latest
    inputs: 
      dockerfile_name: "Dockerfile"
      image_tag: "latest"
      build_context: "."
```

## Versioning Components 

When including a component, you must specify a version by appending `@<ref>` to the component path: 

```yaml
include:
  - component: ci-catalog/java/java-test@1.0.2
```

You can reference: 

- `~latest` (e.g., `@~latest`) - the newest version released to the catalog
- An **exact version** listed in the catalog (e.g., `@1.0.2`)
- A **partial version** (e.g., `@1` or `@1.0`) - the newest release that starts with those numbers. 
- A **branch** (e.g., `@main`) - ***Not recommended for production use***
- A **commit SHA** (e.g., `@a1b2c3d`)