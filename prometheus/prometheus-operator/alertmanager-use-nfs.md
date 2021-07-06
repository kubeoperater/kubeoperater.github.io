<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [遗留的问题，  accessModes: ReadWriteOnce 造成 replica只能为1，缺乏高可用和集群功能。](#%E9%81%97%E7%95%99%E7%9A%84%E9%97%AE%E9%A2%98--accessmodes-readwriteonce-%E9%80%A0%E6%88%90-replica%E5%8F%AA%E8%83%BD%E4%B8%BA1%E7%BC%BA%E4%B9%8F%E9%AB%98%E5%8F%AF%E7%94%A8%E5%92%8C%E9%9B%86%E7%BE%A4%E5%8A%9F%E8%83%BD)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

### 遗留的问题，  accessModes: ReadWriteOnce 造成 replica只能为1，缺乏高可用和集群功能。

**1.配置alertmanager的pv**

``` yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: alertmanager-pv
  labels:
    app: alertmanager-pv
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: alertmanager-slow
  nfs:
    server: 10.255.72.206
    path: "/home/kubernetes_data/alertmanager_data"
```

**2.配置alertmanager的部署配置文件**

``` yaml
apiVersion: monitoring.coreos.com/v1
kind: Alertmanager
metadata:
  labels:
    alertmanager: main
  name: main
  namespace: monitoring
spec:
  baseImage: hub-dev.example.com/prometheus/alertmanager
  nodeSelector:
    beta.kubernetes.io/os: linux
  replicas: 1
  storage:
    volumeClaimTemplate:
      spec:
        volumeMode: Filesystem
        storageClassName: alertmanager-slow
        selector:
          matchLabels:
            app: alertmanager-pv
        resources:
          requests:
            storage: 10Gi
  serviceAccountName: alertmanager-main
  version: v0.14.0
```
