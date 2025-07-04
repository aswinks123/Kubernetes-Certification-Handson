## What is Kubernetes Vertical Pod Autoscaler (VPA)?

The VPA automatically adjusts the CPU and memory requests/limits for your Pods based on actual usage.

## Why use VPA?

Prevents over-provisioning (too high requests wasting resources)

Prevents under-provisioning (too low requests causing OOMKilled or throttling)

Useful for workloads that cannot be horizontally scaled (like single-instance apps, stateful apps).

## How does VPA work?

VPA monitors resource usage and:

Recommends optimal CPU and memory.

Optionally updates the Pod spec (if configured to do so).

Evicts Pods so they restart with the new resource settings.

VPA requires restarting Pods to apply new resources because Kubernetes doesn’t support live resizing.

## VPA Modes


| Mode        | Behavior                                                 |
| ----------- | -------------------------------------------------------- |
| **Off**     | Only gives recommendations (no updates).                 |
| **Auto**    | Automatically updates resource requests and evicts Pods. |
| **Initial** | Applies recommendations only when Pod is created.        |


## Components of VPA


Recommender – Watches usage and calculates resource recommendations.

Updater – Decides when to evict Pods to apply new resource settings.

Admission Controller – Applies recommendations when Pods are (re)created.

```go
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind:       Deployment
    name:       my-app
  updatePolicy:
    updateMode: "Auto"  # Off, Auto, or Initial
```

## Limitations


Does NOT scale replica count (use HPA for that).


Pod eviction may cause downtime (careful for stateful apps).


VPA doesn’t work well with HPA on CPU/Memory (but can work with HPA on custom metrics).