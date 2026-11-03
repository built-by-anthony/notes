# Introduction 

## Learning Objectives 

- Describe how standard Kubernetes Deployments work by default.
- Modify standard Kubernetes Deployments into Argo Rollouts.
- Explain the purpose and flow of Blue-Green and Canary deployments. 
- Define and apply a Blue-Green deployment strategy using Rollout manifests. 
- Use ArgoCD UI to monitor, promote, and rollback changes. 
- Compare progressive delivery strategies to standard rolling updates. 
- Define and apply a Canary deployment strategy using Rollout manifests. 

In this unit you'll explore strategies that help reduce risk, minimize downtime, and give you more control during releases. You'll implement **Blue-Green** deployments using **Argo Rollouts**, and learn how to apply it using GitOps practices within a ROSA cluster.

You'll also learn about **Canary** deployments - another progressive delivery strategy supported by Argo Rollouts. Canary deployments allow you to gradually shift traffic to a new version, making it easier to detect issues before a full rollout. 