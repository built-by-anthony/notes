# Concepts 

#### Understanding ArgoCD Concepts 

ArgoCD is a continuous deployment tool that ensures Kubernetes applications remain in sync with their declared configuration in Git. This lesson introduces the core concepts of ArgoCD - **Projects, Repositories, and Applications** - and hwo they work together to manage deployments. 

## ArgoCD Projects

An **ArgoCD Project** is a logical grouping of applications, used to define boundaries and enforce policies: Projects can be configred to: 

- Restrict which **repositories** an application can pull from.

- Limit the **clusters and namespaces** an application can deploy to. 

- Define the **RBAC policies** for managing access. 

## ArgoCD Repositories 

ArgoCD monitors Git repositories to detect and apply changes to Kubernetes applications. These repositories store the **manifests** and configurations used to define the desired state of the application. 

## ArgoCD Applications 

An **ArgoCD Application** represents a deployed service in Kubernetes and is the primary unit of deployment in ArgoCD. It ties together: 

- The **Git repository** where the application's manifests are stored. 

- The **cluster and namespace** where the application should be deployed. 

- The **sync policy** that determines how updates from Git are applied. 

### Application Syncing 

Each ArgoCD applications maintains a **sync status**, which indicates whether the live state of the application mathces the desired state in Git: 

- **Synced:** The deployed state matches the repository.
- **OutOfSync:** The repository has changes that have not been applied.
- **Unknown:** The application status is unclear or has not been retrieved. 

Syncing can be **automatic** or **manual:**

- **Manual Synx:** Requires triggering an update to deploy changes. 
- **Automated Sync:** ArgoCD will detect changes and deploy them automatically dependingon the policy. 

### Applicaton Health &* Status 

Beyond syncing, ArgoCD evbaluates the **health** of an application: 

- **Healthy:** All components are running as expected. 
- **Progressing:** Deployment is in progress. 
- **Degraded:** One or more components have errors. 
- **Suspended:** The application has been intentionally paused. 

Understanding these statuses helps troubleshoot deployment issues effectively. 

### Managing Applications in ArgoCD

Since applications are controlled by the GitLab pipeline in our environment, you will interact with ArgoCD Applications primarily through:

- **The ArgoCD UI** (to check status, troubleshoot issues, and synx).
- **Manual pipeline jobs** (to trigger application creation, syncing, or deletion)

## Summary 

- **Projects** define boundaries and policies for applications. 
- **Repositories** store the manifests that define that application's state.
- **Applications** are the primary deployment units in ARgoCD, linking Git repositories to Kubernetes clusteres. 
- **Syncing** ensures the live application state matches the Git repository. 
- **Health status** indicates if an application is running correctly or requires attention. 
