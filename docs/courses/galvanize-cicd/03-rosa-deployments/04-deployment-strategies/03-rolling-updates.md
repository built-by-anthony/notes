# Rolling Updates

## How Kubernetes Deployments Work 

Before you learn how to improve deployment strategies, you need to understand how Kubernetes handles updates by default. 

### What is a Deployment? 

A `Deployment` is a controller in Kubernetes that manages a set of identical pods. It ensures that the desired number of replicas are running and can automatically repalce pods when the template (usually the image version) changes.

### What Happens During an Update? 

When you upush a change (like a new image tag), Kubernetes triggerse a **rolling update**: 

1. It creates a new `ReplicaSet` based on the updated template. 
2. It gradually creates new pods and removes old ones. 
3. It waits for new pods to become **Ready** before continuing. 

This behavior is governed by two key settings: 

- `maxSurge`: How many extra pods can be created temporarily during the update
- `maxUnavailable`: How many pods can be unavailable during the update. 

These settings are typically percentages or fixed values. For example: 

```yaml
strategy: 
    type: RollingUpdate
    rollingUpdate:
        maxSurge: 1
        maxUnavailable: 0
```

**Explanation of these attributes:** 

- `strategy`: Defines how updates are performed. Here,, it's set to `RollingUpdate`, which is the default and means pods are updated gradually. 

- `maxSurge`: The maximum number of extra pods that can be created above the desired number of pods during the update. This allows new pods to start before old ones are removed, helping maintain availablilty. 

- `maxUnavailable`: The maximum number of pods that can be unavailable during the update. Setting this to 0 ensures that all pods remain avilable while new ones are coming up. 

This tells Kubernetes to create on extra pod at at ime and not take down any pods unless a new one is already running. 

## What a Rolling Update Looks Like in Action

When using `maxUnavailable: 1` and `maxSurge: 1` with `replicas: 3`, a rolling update proceeds carefully - no more than one pod is taken down or added at a time. Below is a real example you might see in a terminal: 

```bash 
% oc apply -f dev/deploy.yaml; kubectl rollout status deployment/animals
deployment.apps/animals configured 
Waiting for deployment "animals" rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for deployment "animals" rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for deployment "animals" rollout to finish: 1 old replicas are pending termination... 
Waiting for deployment "animals" rollout to finish: 2 of 3 updated replicas are available...
deployment "animals" successfully rolled out 
```

This reflects the pacing of the update: 

- Kubernetes starts 1 new pod (dur to `maxSurge`)
- It waits for readiness, then terminates 1 old pod (honoring `maxUnavailable`)
- This repeats until all pods are replaced and healthy. 

This step-by-step strategy helps reduce downtime and avoid breaking live traffic during updates. 

### The Role of `readinessProbe` in Rolling Updates

One of the most important parts of a rolling update is how Kubernetes decides when a new pod is ready to serve traffic. This is where the `readinessProbe` comes in. 

The `readinessProbe` is a check (like an HTTP request of a command) that Kubernetes runs on each pod to determine if it's ready to accept requests. During a rolling update, Kubernetes will: 

- Start a new pod 
- Wait for the `readinessProbe` to pass 
- Only then, start sending traffic to the new pod and remove an old one 

If the `readinessProbe` fails, Kubernetes will not count the pod as "Ready" and will pause the update until the pod passes the check or is fixed. This helps prevent users from hitting a pod that isn't fully initialized or healthy. 

You can customize the `readinessProbe` to match your application's needs - such as checking a specific endpoint, waiting for a database connection, or verifying that background jobs have started. This ensures that only healthy pods are included in the service during updates, reducing the risk of downtime or errors. 

In summary, the `readinessProbe` is a critical safety net in rolling updates, making sure new pods are truly ready before they replace old ones.

## Built-in Rollback 

If a pod in the new version fails its readiness or liveness checks, Kubernetes can automatically pause the rollout. You can then manually roll back to the previous version using: 

```bash
kubectl rollout undo deployment <name>
```

## Limitations of Native Rolling Updates

- You don't have fine-grained control over traffic shifting. 
- You can't easily test the new version before exposing it to all users. 
- Rollbacks may not be instantaneous.

These limitations are why tools like Argo Rollouts exist. 

## Example `deploy.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: animals
spec: 
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  selector:
    matchLabels:
      app: animals
  template:
    metadata:
      labels:
        app: animals
    spec: 
      containers:
      - name: animal-shelter
        image: public.ecr.aws/b3q1w2q1/kubernetes/rollouts/animal-shelter:go-working
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "24Mi"
            cpu: "25m"
          limits:
            memory: "64Mi"
            cpu: "200m"
        readinessProbe: 
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 1
          periodSeconds: 5
          failureThreshold: 3
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 3
          periodSeconds: 10 
          failureThreshold: 5
        env:
        - name: DB_TYPE
        - value: "inMemory"
```

Two details in that manifest are worth noticing now, because they matter later.

`initialDeplySeconds: 1` on the readiness probe is only possible because this image is a single static binary that is listening in well uunder a second. An application that needs fifteen or twenty seconds to warm up has to declare that, and every rolling update then waits out that delay for every pod in turn. Startup time is not just a local-development annoyance - it is a direct multiplier of how long a rollout takes.

The requests are small for the same reason. `24Mi` and `25m` are what this app actually uses; asking for `256Mi` and half a core when a pod needs a fraction of that reserves capacity nobody can use, and on a shared cluster it is the requests, not the real usage, the decide when scheduling stops.


