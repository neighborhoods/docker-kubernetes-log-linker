# Kubernetes Log Linker
Workaround for [kubernetes/kubernetes#52172](https://github.com/kubernetes/kubernetes/issues/52172).

Based on code from [@joelittlejohn](https://github.com/kubernetes/kubernetes/issues/52172#issuecomment-346075080) and [@cjyar](https://github.com/kubernetes/kubernetes/issues/52172#issuecomment-356085479)

## Deploying
```yaml
---
kind: DaemonSet
apiVersion: apps/v1
metadata:
  name: log-linker
  namespace: kube-system
  labels:
    app: log-linker
spec:
  selector:
    matchLabels:
      app: log-linker
  template:
    metadata:
      labels:
        app: log-linker
    spec:
      tolerations:
        - key: node-role.kubernetes.io/master
          operator: Exists
          effect: NoSchedule
      containers:
      - name: log-linker
        image: neighborhoods/kubernetes-log-linker:1.0.0
        volumeMounts:
        - name: var-lib-docker
          mountPath: /var/lib/docker
        - name: var-log
          mountPath: /var/log
      volumes:
      - name: var-log
        hostPath:
          path: /var/log
      - name: var-lib-docker
        hostPath:
          path: /var/lib/docker
```
