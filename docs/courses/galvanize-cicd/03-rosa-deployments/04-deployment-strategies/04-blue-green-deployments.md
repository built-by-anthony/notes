# Blue-Green Deployments

## Introduction to Blue-Green Strategy

Blue-Green is a deployment strategy designed to reduce risk and downtime when releasing new appication versions. Instead of updating pods in place, Blue-Green creates a second, parallel version of the application - the "Green" deployment - while the existing "Blue" version remains untouched. You test the new version in a controled preview environment, and only switch traffic once it's confirmed to be working as expected. 

`Rollback` is disabled in Production environments. In general, the preferred approach is to manage deployments through GitOps, ensuring the config repository reflects the state of what's running in production. 

> **Note on Abort**: The "Abort" feature is diabled in Argo Rollouts. ABroting in a device other than Git does not align with GitOps principles. Instead of aborting a rollout, best practice is to revert your change in Git or push a fix, allowing the rollout manager to apply the new desired state. Using "Abort" can lead to inconsistencies between your cluster and the Git repository, making it harder to track and manage deployment history.    

## How It Works 

1. **Blue**: The current production version is running and serving traffic. 
2. **Green**: A new version is deployed but not yet receiving traffic. 
3. **Testing**: You validate the green version using preview URLs or services. 
4. **Promote-Full**: If the green version passes validation, traffic is routed to it. 
5. **Rollback**: Do not promote new version if problems found.

## Why Use Blue-Green? 

- **No downtime**: Traffic is switched instantly between versions. 
- **Safer validation**: You can test the new version before exposing it to users. 

## What You Need 

To implement Blue-Green with Argo Rollouts, you must: 

- Ensure the Argo Rollouts controller is installed and enabled for your project/namespace.  
- Reaplces your `Deployment` with a `Rollout` resource. 
- Define a `blueGreen` strategy 
- Create two services: 
    - `activeRoute`: Points to the service receiving live traffic.
    - `previewRoute`: Points to the service used for validation. 

Here's a minimal example of the strategy section: 

```yaml
strategy:
  blueGreen:
    activeService: active-animal-shelter
    previewService: preview-animal-shelter
    autoPromotionEnables: false
    previewReplicaCount: 1  # Reduces the number of replicas for the preview
```

With `autoPromotionEnabled: false`, you control when traffic switches to the new version. 

## Example Rollout Manifest 

To convert a standard Kubernets Deployment to a Blue-Green Rollout, you'll need to make two key changes: 

1. Change `kind: Deployment` to `kind: Rollout`.
2. Add the `strategy.blueGreen` section and define two services: `activeService` and `previewService`.

Here's a minimal `rollout.yaml` example for the `animal-shelter` application: 

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: animal-shelter
  labels:
    app: animal-shelter
spec:
  replicas: 2
  strategy:
    blueGreen:
      activeService: active-animal-shelter
      previewService: preview-animal-shelter
      autoPromotionEnabled: false
      previewReplicaCount: 1   # Reduces the number of replicasw for the preview
  selector:
    matchLabels:
      app: animal-shelter
  template:
    metadata:
      labels:
        app: animal-shelter
    spec:
      containers:
        - name: animal-shelter
          image:  public.ecr.aws/b3q1w2q1/kubernetes/rollouts/animal-shelter:go-working
          ports:
            - containerPort: 8080
          env: 
            - name: DB_TYPE
              value: inMemory 
```

The file will deploy a new version of the app alongside the current one, using `preview-animal-shelter` to access it. Once validated, you can manually promote the new version to production. 

## Defininf the Preview and Active Services 

To support Blue-Green deployment, you must define two Kubernetes `Service` objects: 

- **Active Service**: Routes traffic to the current (blue) version. 
- **Preview Service**: Routes traffic to the new (green) version during rollout. 

Both services will initially select teh same label - for example, `app: animal-shelter`. Argo Rollouts will dynamically update the actual label selectors on these services. To point to the appropriate `ReplicaSet`. 

Here's what the service definitions might looks like: 

```yaml
# Active Service
apiVersion: v1
kind: Service 
metadata: 
  name: active-animal-shelter
spec: 
  selector: 
    app: animal-shelter
  ports:
    - port: 80
      targetPort: 8080
      protocol: TCP

---

# Preview Service
apiVersion: v1
kind: Service
metadata:
  name: preview-animal-shelter
spec:
  selector:
    app: animal-shelter
  ports:
    - port: 80
      targetPort: 8080
      protocol: TCP
```

> **Note**: You don't need to manually manage the selector 0 Argo Rollouts will update these behind the scenes as the rollout progresses. 
