# Canary Deployments

## Introduction to Canary Deployments

Canary deployments allow you to release a new version of your application to a **subset of users**, monitor its performance, and gradually roll it out to the rest of your traffic. This progressive delivery method helps detect issues early while limiting the impact on users.


Kubernetes Deployments perform a kind of implicit canary via rolling updates, but Argo Rollouts gives you more explicit control with `steps`, `pause`, and weight traffic shifts. 

## How It Works 

1. A new version is deployed alongside the old one.
2. A small percentage of traffic (e.g., 10%) is routed to the new version. 
3. You apuse, observe metrics/logs, and optionally promote to the next percentage. 
4. This continues until 100% of traffic is served by the new version. 

## Why Use Canary? 

- Early detection of bugs with minimal exposure. 
- Better user experience - bad versions rarely imapct all users. 
- Gradual confidence building as rollout progresses. 

## Steps on Enable and Perform a Canary Deployment 

1. **Enable Argo Rollouts in Your Namespace** 

- Ensure the Argo Rollouts controller is installed and enabled for your project/namespace.

2. **Convert Deployment to Rollout**

    - Change your Kubernetes resource from `kind: Deployment` to `kind: Rollout` in your manifest. 

3. **Define Canary Strategy** 

    - Adds a `strategy.canary.steps` section to specify traffic weights and pauses. 

4. **Configure Traffic Routing**

    - Traffic routing will be done through Argo Rollout Manager

5. **Apply the Rollout Manifest**

    - Commit and push your changes to Git (for GitOps) or apply directly with `kubectl argo rollouts apply -f rollout.yaml`

6. **Monitor and Promote**

    - Rollout is automatic based on strategy defined in `rollout.yaml`. No human intervention required. 

Here's an example snippet: 

```yaml
strategy:
  canary:
    steps: 
      - setWeight: 20 
      - pause: { duration: 30s }
      - setWeight: 50
      - pause: { duration: 30s }
```

### Explanation 

This canary strategy defines how traffic is shifted and paused during the rollout: 

- `setWeight: 20` shifts 20% of user traffic to the new version, while 80% stays on the old version. 

- `pause: { duration: 30s }` waits for 30 seconds, allowing you to monitor the new version for issues. 

- `setWeight: 50` increases the new version's traffic to 50%, splitting traffic evenly between old and new.

- Another pause gives you more time to observe before final promotion.

These steps can be customized to fit your risk tolerance and monitoring needs. The gradual rollout helps catch problems early and limits their impact. 

## Canary Service

In Argo Rollouts, a "canary service" is used to direct traffic specifically to the canary version of your application during the rollout. This service allows you to monitor and analyze only the new version, separate from the stable one. Typically, you define two services: 

- The stable service routes traffic to current production version. 
- The canary service routes traffic to the new version being tested. 

This separation is important for accurate analysis and monitoring, as it ensures metrics and checks are only applied to the canary deployment. 

### Defining Table and Canary Services 

To enable proper traffic routing, you need to define both a stable service and a canary service in your Kubernetes manifests. The stable service points to the pods running the current version, while the canary service targets the pods running the new version. 

Example service definition 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-stable
spec:
  selector:
    app: my-app
    role: stable
  ports:
    - port: 80
      targetPort: 8080

--- 
apiVersion: v1
kind: Service
metadata:
  name: my-app-canary
spec:
  selector:
    app: my-app
    role: canary
  ports:
    - port: 80
      targetPort: 8080
```

These services allow Argo Rollouts to manage traffic between the stable and canary versions during the rollout process. 

### Purpose: 

- Automate health checks during rollout steps 
- Reduce manual verification 
- Integrate with monitoring tools (Prometheus, Datadog, etc.)
- Abort or promote based on analysis results 

## Using analysis-template in Canary Deployments 

Argo Rollouts supports automated analysis during a canary rollout using an `analysis-template`. This allows you to define checks (like metrics, logs, or custom scripts) that run at each pause step, helping you decide whether to proceed, pause, or abort the rollout. 

### Example analysis-template YAML

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: success-rate-check
spec:
  metrics:
    - name: success-rate
      intervan: 1m 
      successCondition: result > 0.95
      provider:
        prometheus:
          address: http://prometheus.monitoring.svc:9090
          query: sum(rate(http_requests_total{status="200"}[5m])) / sum(rate(http_requests_total[5m]))
```

You reference this tempalte in your Rollout spec, so Argo can run the analysis at each pause and make decisions automatically. 

## Example Rollout Spec with Analysis Template

Below is an example of how to reference an analsis template in your Argo Rollouts canary strategy: 

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata: 
  name: my-app-rollout
spec: 
  replicas: 3
  strategy:
    canary:
      steps: 
        - setWeight: 20
        - pause: { duration: 2m }
        - analysis:
          templates:
            - templateName: success-rate-check
          args: 
            - name: service-name
              value: my-app-service
        - setWeight: 50
        - pause: { duration: 5m }
        - analysis: 
          templates:
            - templateName: success-rate-check
          args:
            - name: service-name
              value: my-app-service
        - setWeight: 100
```

This configuration runs the `success-rate-check` analysis at each pause step, helping automate decision-making during rollout. 

## Summary 

Canary deployments offer fine-grained control and safer releases. Argo Rollouts enhances this by allowing explicit steps. pauses, and metrics integration - far beyond what native Kubernetes provides.
