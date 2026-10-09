# Kubernetes Volumes

This note documents the storage concepts practiced in Session 13. Kubernetes Pods are temporary by design, so volumes are used when a container needs shared files, scratch space, node files, or persistent data that survives Pod replacement.

## emptyDir

`emptyDir` creates an empty directory when a Pod starts. The directory is shared by all containers in the same Pod and remains available while that Pod exists.

Important behavior:

- Data survives container restarts inside the same Pod.
- Data is deleted when the Pod is deleted or rescheduled.
- Good for temporary files, cache data, and sharing files between containers in one Pod.

Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-demo
spec:
  containers:
    - name: app
      image: busybox:1.36
      command: ["sh", "-c", "while true; do date >> /data/app.log; sleep 5; done"]
      volumeMounts:
        - name: cache
          mountPath: /data
  volumes:
    - name: cache
      emptyDir: {}
```

Useful commands:

```bash
kubectl apply -f emptydir-pod.yaml
kubectl exec emptydir-demo -- cat /data/app.log
kubectl delete pod emptydir-demo
```

## hostPath

`hostPath` mounts a file or directory from the Kubernetes node into a Pod.

Important behavior:

- Data is stored on the node, not inside the container.
- If the Pod is scheduled to another node, it may not see the same data.
- Useful for learning, node-level tools, log collectors, and local single-node clusters.
- Use carefully in production because it exposes node filesystem paths to Pods.

Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostpath-demo
spec:
  containers:
    - name: app
      image: busybox:1.36
      command: ["sh", "-c", "echo hostPath demo > /node-data/message.txt && sleep 3600"]
      volumeMounts:
        - name: node-storage
          mountPath: /node-data
  volumes:
    - name: node-storage
      hostPath:
        path: /tmp/student-data
        type: DirectoryOrCreate
```

Useful commands:

```bash
kubectl apply -f hostpath-pod.yaml
kubectl exec hostpath-demo -- cat /node-data/message.txt
kubectl describe pod hostpath-demo
```

## PersistentVolume

A `PersistentVolume` is cluster storage created or made available by an administrator. It represents real storage such as a disk, NFS share, cloud volume, or local path.

Important behavior:

- It is a cluster-level resource.
- It exists independently of Pods.
- It can be retained, recycled, or deleted depending on its reclaim policy.

Example:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: manual-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /tmp/manual-pv
```

## PersistentVolumeClaim

A `PersistentVolumeClaim` is a request for storage by a user or application. Pods mount PVCs, and Kubernetes binds the PVC to a matching PV.

Important behavior:

- The app asks for storage using size, access mode, and optionally a StorageClass.
- The Pod does not need to know the storage implementation.
- The claim can remain even if the Pod is deleted.

Example:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Pod mount example:

```yaml
volumeMounts:
  - name: app-storage
    mountPath: /data
volumes:
  - name: app-storage
    persistentVolumeClaim:
      claimName: app-data
```

Useful commands:

```bash
kubectl get pv
kubectl get pvc
kubectl describe pvc app-data
```

## StorageClass

A `StorageClass` defines how Kubernetes should create storage dynamically. It describes the provisioner, reclaim policy, binding mode, and provider-specific parameters.

Important behavior:

- It removes the need to manually create PVs for every application.
- Different StorageClasses can represent different performance or cost levels.
- A cluster can have a default StorageClass.

Example:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
provisioner: k8s.io/minikube-hostpath
reclaimPolicy: Delete
volumeBindingMode: Immediate
```

Useful commands:

```bash
kubectl get storageclass
kubectl describe storageclass standard
```

## Dynamic Provisioning

Dynamic provisioning automatically creates a PersistentVolume when a PVC is created. The PVC references a StorageClass, and the StorageClass provisioner creates the real backing storage.

Flow:

```text
Pod -> PVC -> StorageClass -> Dynamic PV -> Real storage
```

Example PVC using a StorageClass:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: dynamic-data
spec:
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

Verification commands:

```bash
kubectl apply -f pvc.yaml
kubectl get pvc
kubectl get pv
kubectl describe pvc dynamic-data
```

Expected result:

```text
NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS
dynamic-data   Bound    pvc-0f62c25e-7f27-4b96-a03f-c63e5d88c542   500Mi      RWO            standard
```

## Summary

| Storage type | Lifetime | Main use |
| --- | --- | --- |
| `emptyDir` | Same as Pod | Temporary scratch space |
| `hostPath` | Node filesystem | Local learning and node-level access |
| `PersistentVolume` | Independent cluster resource | Real persistent storage |
| `PersistentVolumeClaim` | Until claim deletion | Application storage request |
| `StorageClass` | Cluster configuration | Dynamic storage creation |
