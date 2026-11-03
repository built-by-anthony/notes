# CLI Tools

## `kubectl` vs `oc`

When working with ROSA, you'll use command-line tools to manage Kubernetes resources. There are two main CLIs to know about: `kubectl` and `oc`.

## What's the Difference? 

|Tool|Description|Use Case|
|----|-----------|--------|
|`kubectl`|The standard Kubernetes CLI|Works with any Kubernetes cluster|
|`oc`|The OpenShift CLI - includes all `kubectl`features|Required for OpenShift specific tasks|

**Anything you can do with `kubectl`, you can also do with `oc`.** That means `oc` is a **suberset** of `kubectl`.

## Why Use `oc`?

ROSA is built on OpenShift, which adds capabilities beyond plain Kubernetes. The `oc` CLI includes commands for: 

- Creating and managing **routes** ***(OpenShift's ingress objects)***
- Working with **projects** ***(OpenShift's enhanced namespaces)***
- Managing **image streams** and **build configs**
- Viewing logs and events with enhanced formatting

## CLI Primer: Common Commands

### Inepecting the cluster 

```bash
oc whoami       # Show your current user
oc project      # Show your current project (namespace)
oc get pods     # List all pods
oc get svc      # List services
oc get deploy   # List deployments 
oc get route    # List OpenShift routes (only in oc)
```

### Applying Resources 

```bash
oc apply -f <filename.yaml>     # Create or update a resource from a file 
oc delete -f <filename.yaml>    # Remove a resource from a file
```

### Viewing Details 

```bash 
oc describe pod <pod-name>      # Detailed info on a pod 
oc logs <pod-name>              # View logs for a running pod 
```

> **Note:** Stick with `oc` - You can always use `kubectl` instead of `oc` for Kubernetes-native commands - but stick with `oc` for consistency in OpenShift. 

## Summary 

- `kubectl` is the universal Kubernetes CLI
- `oc` extends it with OpenShift-specific capabilities
- Use `oc` for everytying in ROSA - it works for both Kubernetes and OpenShift features 