# Introduction to ROSA

## Objectives 

- Understand what ROSA is and why it's used in modern cloud-native architectures
- Log in and navigate the OpenShift web console 
- Identify the differences between `kubectl` and `oc` CLI tools 
- Use the basic CLI commands to manage applications in a ROSA cluster 
- Manually deploy a sample application using YAML manifests 

Red Hat Openshift Service on AWS (ROSA) is a managed Kubernetes platform built on Red Hat OpenShift and hosted on Amazon Web Services. It gives developers and DevOps teams a powerful, production-ready Kubernetes environment without the complexity of managing the Control Plane. 

With ROSA, your team can: 

- Build and deploy cloud-native apps faster 
- Rely on built-in monitoring, logging, and security 
- Use OpenShift's developer-friendly console alongside standard Kubernetes tools like `kubestl`

# What is ROSA? 

Red Hat OpenShift on AWS (ROSA) is a **fully managed Kubernetes platform** that runs on Amazon Web Services. It combines the power of Kubernetes with the enterprise-grasde features of OpenShift - all without needs to install, manage, or upgrade your cluster infrastructure. 

> **Note:** **OpenShift** is a pre-packaged, turnkey enterprise distribution of Kubernetes. 

## why Use ROSA? 

Oranizations adopt ROSA for several reasons. The main four reasons are as follow: 

### 1. Managed by Red Hat and AWS 

You don't need to worry about provisioning noders, configuring networking, or upgrading the control plane - it's all handled for you. 

### 2. Production-Ready Out of the Box 

Rosa clusters come with: 
- Integrated monitoring and logging 
- Role-based access control (RBAC)
- Image and build management 
- Built-in developer tools like source-to-imnage (S2I), templates, and GitOps

### 3. Supports Enterprise DevOps Workloads

ROSA works well with CI/CD tools like GitLab, ArgoCD, Jenkins, and Tekton, making it a popular choice for teams building and deploying microservices.

### 4. Secure and Scalable 

Becuase it runs on AWS, you can scale ROSA clusters as needed and integrate with sergices like RDS, S3, IAM, and more, all while taking advantage of OpenShift's strict security model.

## Summary 

ROSA gives you the benefits of Kubernetes with less operational overhead. It's ideal for teams who want to **build, deploy, and sca le applications quickly** in a production-gradse environment. 

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


# Routes

## Getting Traffic to Your Application 

You can deploy a perfectly health application to ROSA and still not be able to open it in your browser. A running pod is only reachable from ***inside*** the cluster. To expose it to the outside world, OpenShift uses a **Route**. 

A **Route** is an OpenShift object that maps a public hostname to a Service inside your project. It is OpenShift's answer to the Kubernetes Ingress object, and it is one of the OpenShift-specific resources you can only manage with `oc`.

## Service vs. Route 

|**Object**|**Scope**|What it does|
|----------|---------|------------|
|**Service**|Internal to cluster| Gives your pods a single, stable internal address and load-balances across them| 
|**Route**|External to cluster|Publishs a hostname on the internet and forwards that traffic to a Service|

The chain of traffic looks like this 

```
Browser -> Route (public hostname) -> Service (stable internall address) -> Pod(s)
```

Each link depends on the one after it. A route that points at a Service that doesn't exist will return an error, and a Service that selects no pods will return an error, even though the Route itself looks fine. 

> ***Why Three Manifests?***
> This is why the exercise applies three manifests, and why the order matters conceptually: `deploy.yaml` creates the pods, `service.yaml` gives them a stable internal address, and `route.yaml` publishes that address to the outside world. 

## Anatomy of a `route.yaml`

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata: animal-shelter
  name: animal-shelter
spec:
  to: 
    kind: Service
    name: animal-shelter        # must match the Service name in serive.yaml 
  port: 
    targetPort: 8080            # must match a port exposed by the Service
  tls: 
    termination: edge
    insecureEdgeTerminationPolicy: Redirect
```

|Field|Purpose|
|-----|-------|
|`spec.to.name`|The service this route sends traffic to. **The most common source of a broken Route**| 
|`spec.port.targetPort`|Which port on that Service to use. Can be a port number or a named port|
`spec.host`|Optional. Leave it out and OpenShift generates a hostname for you.| 
|`spec.tls.termination`|How HTTPS is handled. `edge` is the typical choice for this training|

> **Point at the Service, Not they Deployment** 
> `spec.to.name` refers to the **Service** name, not your deployment name or your container name. These are often all the same word, which hides the mistake until the Route returns a `503` error. 

## Where Your Hostname Comes From 

If you omit `spec.host`, OpenShift builds a nostname for you using this pattern: 

```
<route-name>-<project-name>.<cluster-apps-domain>
```

So a Route named `animal-shelter` in the proejct `sdi-tony-quist` becomes something like: 

```
animal-shelter-sdi-tony-quist.apps.<cluster-domain>.openshiftapps.com
```

## A Note onm TLS 

`termination: edge` means that OpenShift router terminates HTTPS for you at the cluister edge, then forwards plain HTTP to your Service inside the container. Your applications does not need its own certificate - you get HTTPS for free. 

Adding `insecureEdgeTerminationPolicy: Redirect` sends anyone who arrives over `http://` to the `https://` address automatically. 

If you leave the `tls` block out entirely, your Route serves plain HTTP only. 

## Inspecting Routes

### Using the `oc` CLI

```bash
oc get route                        # List routes in your project 
oc get route <route-name> -o yaml   # See the full definition, incuding the generated host 
oc describe route <route-name>      # Show the targer Service, port, and TLS settings
```

The `HOST/PORT` column in `oc get route` is your application's public URL. 

## Creating a Route Without a Manifest 

The exercise gives you a `route.yaml`, which is the approach you wnat for real work because the file can be committed to GIt. For quick testing, `oc` can also generate a Route from an existing Service: 

```bash
oc expose service <service-name>
```

> ***Prefer a Committed Manifest*** `oc expose` is convenient, but the Route it creates exists only in the cluster. Nothing records how it was made. Prefer a checked-in `route.yaml` - this becomes essential in the next unit, when ArgoCD deploys your manifests straight from Git. 

## How Else Can Traffic Reach an Application? 

|Mechanism|Status on ROSA|When you'd encounter it| 
|---------|--------------|-----------------------|
|Route| Native and preferred|What you'll use throughout this training| 
|Ingress|Supported - converted into a Route for you| Third-party Helk charts and manifests written to be portable|
|Service `type: LoadBalancer`| Works, provisions a real AWS load balancer| Non-HTTP traffic; each one costs money, so it's rare for web apps| 
|Service `type: NodePort`|Generally restricted bby cluster policy|Legacy stups; not something you should reach for | 
|`oc port-forward`|Always available|Temporary local debugging - private to you, not a public URL|

### Ingress Objectes Become Routes 

You do not have to rewrite an Ingress to run it on ROSA. WHen you apply an Ingress object, the OpenShift Ingress Controller reads it and generates a matching route automatically. 

```bash
oc get ingress      # the object you applied
oc get route        # the Route OpenShift created from it 
```

### Using `port-forward` to Isolate a Problem 

`oc port-forward` deserves special mention as a debugging tool, not an exposire strategy. It tunnels a port on your machine straight to a pod or Service, bypassing the route entirely: 

```bash
oc port-forward service/<service-name> 8080:8080
```

Then open `http://localhost:8080`. This is the fastest way to answer the question **"is my application broken, or is my Route broken?"** - if the app works through the tunnel but not through its public hostname, the problem is in your Route or Service, not your code. 

## Common Route Problems 

|**Symptom**|**Likely Cause**|**Fix**|
|-----------|----------------|-------|
|`oc get route` returns nothing|`route.yaml` was never applied, or you're in the wrong project|Run `oc project` to confirm, then reapply the manifest|
|`503 Service Unavailable`|`spec.to.name` doesn't match a real Service|Compare `oc get route -o yaml` against `oc get svc` |
|`503` with a Service that doesn't exist|No health pods behind the Service|Check `oc get pods` and `oc logs <pod-name>`|
|Application loads but assets fail|App is generating `http://` links behind an `edge` Route| Configure your app to respect the `X-Forwarded-Proto` header|

## Summary 

- A **Service** gives your pods astable address ***inside*** the cluster; a **Route** publishes that Service ***outside*** the cluster. 
- Routes are OpenShift-specific, which is why you need `oc` rather thanb plain `kubectl` to manage them 
- `spec.to.name` must match your Service name exactly - mismathces surface as a `503`
- Omit `spec.host` and OpenShift generates a unique hostname from your Route and project names 
- `termination: edge` gives you HTTPS without your application handling certificates 
- An **Ingress object stills works on ROSA - OpenShift converts it into a Route for you - but a Route can express OpenShift features that ingress cannot
- `oc port-forward` is for debugging, not exposure; use it to tell an application pr oblem apart form a Route problem

 