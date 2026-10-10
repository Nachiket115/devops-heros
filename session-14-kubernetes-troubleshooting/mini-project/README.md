# Kubernetes Troubleshooting Challenge

Your job is to:

```text
Deploy
  │
  ▼
Observe
  │
  ▼
Break
  │
  ▼
Investigate
  │
  ▼
Find root cause
  │
  ▼
Fix
  │
  ▼
Verify
```

---

## Project Scenario

You have a simple Nginx application running inside Kubernetes.

You have:
* Deployment
* Service
* Pods

Your application should be accessible through the Service. But your team has reported that something is wrong.

Your job is to find and fix the problems.

---

## 1. Deploy The Application

Run:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Check:

```bash
kubectl get pods
kubectl get service
```

---

## 2. Check The Application

Run:

```bash
kubectl get pods -o wide
```

Then:

```bash
kubectl describe pod <pod-name>
```

Then:

```bash
kubectl logs <pod-name>
```

Then:

```bash
kubectl exec -it <pod-name> -- bash
```

Inside the container:

```bash
curl localhost
```

You should get the Nginx response.

---

## 3. Check The Service

Run:

```bash
kubectl get service
```

Then:

```bash
kubectl describe service troubleshooting-service
```

Check:
* **Selector**
* **TargetPort**
* **Endpoints**

---

## 4. Check Endpoints

Run:

```bash
kubectl get endpoints troubleshooting-service
```

You should see Pod IP addresses.

---

## 5. Create A Broken Pod

Run:

```bash
kubectl apply -f broken-pod.yaml
```

Check:

```bash
kubectl get pod project-broken-pod
```

You should see an image-related problem.

---

## 6. Troubleshoot It

You are **NOT** allowed to immediately change the YAML.

First run:

```bash
kubectl get pod project-broken-pod
```

Then:

```bash
kubectl describe pod project-broken-pod
```

Then look at **Events**. Find the root cause.

---

## 7. Your Task

For the broken Pod, answer:

**Question 1:** What is the Pod status?  
*Answer:* `ImagePullBackOff` (transitions from `ErrImagePull`).

**Question 2:** What is the actual error?  
*Answer:* `Failed to pull image "nginx:this-tag-does-not-exist": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/nginx:this-tag-does-not-exist": not found`.

**Question 3:** Which command helped you find the reason?  
*Answer:* `kubectl describe pod project-broken-pod` in the **Events** section.

**Question 4:** What is wrong with the image?  
*Answer:* The tag `this-tag-does-not-exist` is invalid and does not exist in Docker Hub.

**Question 5:** How would you fix it?  
*Answer:* Update `broken-pod.yaml` container image to a valid tag (such as `nginx:1.27` or `nginx:alpine`) and apply the updated manifest.

---

## 8. Service Troubleshooting Challenge

Now intentionally create a Service selector problem.

Change the Service selector from:

```yaml
selector:
  app: troubleshooting-app
```

to:

```yaml
selector:
  app: wrong-app
```

Apply it. Then run:

```bash
kubectl get service
```

Then:

```bash
kubectl get endpoints troubleshooting-service
```

You should find: `<none>`.

---

## 9. Find The Root Cause

Run:

```bash
kubectl get pods --show-labels
```

Check the Pod label.

Then:

```bash
kubectl describe service troubleshooting-service
```

Compare **Pod label** with **Service selector**. Find the mismatch and fix it.

---

## 10. Final Troubleshooting Checklist

Before saying: *"It is not working."*

Always check:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- sh
kubectl get events
```

For Service problems:

```bash
kubectl describe service <service-name>
kubectl get endpoints <service-name>
nslookup <service-name>
```

---

## 11. Troubleshooting Table

Fill this table in your submission:

| Problem | What I Saw | Command I Used | Root Cause | Fix |
| :--- | :--- | :--- | :--- | :--- |
| **Broken Pod** | Pod in `ImagePullBackOff` / `ErrImagePull` | `kubectl get pod`, `kubectl describe pod` | Invalid non-existent image tag `this-tag-does-not-exist` | Change image tag to `nginx:1.27` |
| **Service Problem** | Endpoints returned `<none>`, requests timed out | `kubectl get endpoints`, `kubectl describe svc` | Service selector `app=wrong-app` mismatched pod label `app=troubleshooting-app` | Update Service selector to `app: troubleshooting-app` |
| **Image Problem** | Kubelet event `Failed to pull image: rpc error: code = NotFound` | `kubectl describe pod` (Events section) | Typo in image repository or tag name | Correct repository/tag name in container spec |

---

## 12. README Questions

Answer these in your own words:

1. **What does `kubectl get` tell us?**  
   It provides a high-level summary of resources in the cluster/namespace, showing name, ready replicas, current lifecycle status (`Running`, `CrashLoopBackOff`, `Pending`), restarts, and age. Adding `-o wide` shows assigned Node and Pod IP.

2. **What is the difference between `get` and `describe`?**  
   `kubectl get` gives a concise tabular list overview of resources. `kubectl describe` provides deep, detailed configuration data, exact container states, lifecycle termination reasons, volume mounts, resource limits, and real-time controller/kubelet events for a single resource.

3. **Why do we use `kubectl logs`?**  
   To read stdout/stderr streams from container processes to identify application-level crashes, unhandled stack traces, database connectivity timeouts, and syntax errors. The `--previous` flag retrieves crash logs from terminated container instances.

4. **When would you use `kubectl exec`?**  
   To execute live troubleshooting commands (`curl`, `nslookup`, `env`, `df -h`) or enter an interactive debugging shell directly inside a running container to verify network connectivity, DNS resolution, and local filesystem configurations.

5. **What does `CrashLoopBackOff` mean?**  
   The container process started, failed/exited with an error, and Kubernetes restarted it repeatedly. Because of the repeated failures, Kubernetes enters an exponential backoff delay before trying to start the container again.

6. **What does `ImagePullBackOff` mean?**  
   The kubelet failed to pull the specified container image from the registry (e.g., non-existent tag, private repo without credentials, or network issue) and is backing off before retrying.

7. **Why can a Pod remain `Pending`?**  
   The scheduler cannot place the pod on any node due to insufficient CPU/memory resources, unmatched `nodeSelector` or node affinity rules, untolerated node taints, or unattached persistent volume claims (PVCs).

8. **Why can a Service have no endpoints?**  
   The Service's `selector` does not match the `labels` of any running pods, or the matching pods are not in a `Ready` state (e.g. failing readiness probes), or the pods exist in a different namespace.

9. **What is the relationship between a Service selector and Pod labels?**  
   A Service uses label selectors to dynamically discover and route traffic to Pods whose labels match all specified key-value pairs. The Endpoint controller continuously updates the Service's endpoint list based on these matching labels.

10. **What is Kubernetes DNS?**  
    A built-in cluster service (CoreDNS) that automatically provides service discovery by resolving Kubernetes Service names (and FQDNs like `service.namespace.svc.cluster.local`) to their corresponding ClusterIP addresses.

---

## 13. Final Architecture

Your final application should look like:

```text
                    Kubernetes Cluster
                            │
                            ▼
                  ┌───────────────────┐
                  │      Service      │
                  └─────────┬─────────┘
                            │
                     Service Selector
                            │
              ┌─────────────┴─────────────┐
              │                           │
              ▼                           ▼
            Pod 1                       Pod 2
              │                           │
              └─────────────┬─────────────┘
                            │
                        Nginx App
```

---

## 14. What You Should Be Able To Do

After completing this project, you should be comfortable with:

```bash
kubectl get
kubectl describe
kubectl logs
kubectl exec
kubectl events
```

and troubleshooting:
* `CrashLoopBackOff`
* `ImagePullBackOff`
* `Pending`
* Service problems
* DNS problems

---

## Final Rule

When something breaks: **DON'T GUESS.**

```text
GET
 │
 ▼
DESCRIBE
 │
 ▼
EVENTS
 │
 ▼
LOGS
 │
 ▼
EXEC
 │
 ▼
TEST
 │
 ▼
FIX
 │
 ▼
VERIFY
```

That is the basic Kubernetes troubleshooting mindset.