# Session 13 Homework: Kubernetes Storage, HPA and Probes

This folder contains the completed homework deliverables for Session 13.

## Deliverables

| Requirement | Location |
| --- | --- |
| Volume documentation | `01-kubernetes-volumes/README.md` |
| Existing volume examples | `01-volumes/`, `02-persistent-storage/`, `03-storageclass/` |
| HPA YAML | `04-hpa/hpa.yml` and `04-hpa/hpa.yaml` |
| Load generator | `04-hpa/load-generator.yaml` |
| HPA output and screenshots | `04-hpa/readme1.md`, `04-hpa/screenshots/` |
| Probe examples | `05-probes/` |
| Mini-project implementation | `mini-project/` |
| Mini-project screenshots | `mini-project/screenshots/` |

## Quick Run: HPA Hands-on

```bash
cd session-13-storage-hpa-probes/04-hpa
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f hpa.yml
kubectl apply -f load-generator.yaml
kubectl get hpa
kubectl get pods
kubectl top pods
kubectl describe hpa hpa-demo
```

## Quick Run: Mini Project

```bash
cd session-13-storage-hpa-probes/mini-project
kubectl apply -f namespace.yaml
kubectl apply -f pvc.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f hpa.yaml
kubectl apply -f load-generator.yaml
kubectl get all,pvc -n production-webapp
```

## Notes

- Metrics Server must be enabled for CPU-based HPA output.
- Minikube users can run `minikube addons enable metrics-server`.
- The included screenshots document representative command output for the homework submission.
