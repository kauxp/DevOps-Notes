# Understanding HPA

Short summary:

Horizontal Pod Autoscaler (HPA) concepts and usage.

What you'll learn:

- How HPA scales based on metrics
- Metrics servers and custom metrics
- Tuning and stability considerations

Resources:

- Add links or notes here

Exercises:

- Configure HPA for a sample Deployment

Notes:

- Add instructor notes or references here

---

## HPA — Big Picture

HPA = Horizontal Pod Autoscaler

Automatically:

- Increases pods when load increases
- Decreases pods when load drops

Works on:

- Deployment
- ReplicaSet
- StatefulSet

Most common metric: CPU

One-liner:

More load? Add more pods. Less load? Remove pods.

Horizontal vs Vertical (memory trick):

- Horizontal = add/remove PODS
- Vertical = increase/decrease CPU/RAM of SAME pod
- HPA = Horizontal only.

### How HPA works (flow)

User traffic → Service → Pods
Pods CPU ↑
↓
Metrics Server reports usage
↓
HPA Controller checks: CPU usage / CPU request
↓
If > threshold → increase replicas
If < threshold → decrease replicas

Very important formula:

HPA uses: CPU Usage / CPU Request

So: If `requests.cpu` is NOT set → HPA will NOT work

### Prerequisites (must have)

- Working cluster (kubeadm / any)
- Metrics Server (MANDATORY)

Check:

```bash
kubectl top nodes
kubectl top pods
```

If those commands work → metrics server is OK.

### Step-by-step setup (exam flow)

1. Install Metrics Server — without this HPA shows CPU = 0% and no scaling happens.

1. Create Deployment — important: set resource requests:

```yaml
resources:
  requests:
    cpu: 200m
```

No request = No HPA.

1. Create Service — needed to send traffic to pods and generate load.

1. Create HPA object. Example:

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
```

Meaning: if avg CPU > 50% → add pods.

### How to test HPA

Generate load using busybox:

```bash
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://my-service; done"
```

Watch scaling:

```bash
kubectl get hpa -w
kubectl get deploy
```

### What happens internally

- CPU crosses threshold
- Metrics Server reports it
- HPA controller calculates desired replicas
- Deployment scales
- New pods are created

### Scale down behavior

- Scale down is not immediate — cooldown period and gradual reduction.

### Common problems (quick table)

- CPU = 0% → Cause: No metrics server → Fix: install metrics-server
- No scaling → Cause: No cpu request → Fix: add requests.cpu
- HPA not working → Cause: label/selector mismatch → Fix: correct selector

### Important commands

- kubectl get hpa
- kubectl describe hpa
- kubectl top pods
- kubectl top nodes
- kubectl get deploy
- kubectl get events

### Interview answer (ready-made)

HPA monitors pod metrics via Metrics Server and automatically scales the number of replicas based on CPU or custom metrics thresholds defined in the HPA resource.

### Final mental model

- Metrics Server = thermometer
- HPA = decision maker
- Deployment = executor
- Pods = workers

One-line summary:

HPA automatically adds or removes pods based on CPU load using metrics from Metrics Server.
