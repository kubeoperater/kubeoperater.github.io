<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [其实flume有官方的docker镜像，我们是因为flume自定义过，所以需要这种方式。](#%E5%85%B6%E5%AE%9Eflume%E6%9C%89%E5%AE%98%E6%96%B9%E7%9A%84docker%E9%95%9C%E5%83%8F%E6%88%91%E4%BB%AC%E6%98%AF%E5%9B%A0%E4%B8%BAflume%E8%87%AA%E5%AE%9A%E4%B9%89%E8%BF%87%E6%89%80%E4%BB%A5%E9%9C%80%E8%A6%81%E8%BF%99%E7%A7%8D%E6%96%B9%E5%BC%8F)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

1. 实现需求：每个pod的日志都会被flume收集到elk里
2. flume dockerfile：

``` yaml
from hub-dev.example.com/base/ubuntu:16.04
ADD jdk1.8 /usr/local/jdk1.8/
ADD flume-agent /opt/flume-agent/
RUN mkdir -p /export/log/test/; apt-get update -q && apt-get install locales -y &&  locale-gen zh_CN.UTF-8
ENV JAVA_HOME /usr/local/jdk1.8
CMD ["/opt/flume-agent/bin/start.sh"]
```
#这一步是规定死了 目录就是这个/export/log/test下  在应用的过程中 可以通过emptyDir方式共享，然后再动态挂在到flume的相应位置就可以了

# 其实flume有官方的docker镜像，我们是因为flume自定义过，所以需要这种方式。


3. 在k8s里的部署

``` yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-access-log-flume
spec:
  replicas: 1
  template:
    metadata:
      labels:
        k8s-app: nginx-access-log-flume
    spec:
      containers:
      - name: nginx-access
        resources:
          limits:
            cpu: 2000m
            memory: 4096Mi
          requests:
            cpu: 200m
            memory: 2048Mi
        image: hub-dev.example.com/base/nginx-kafka.v1.0:latest
        imagePullPolicy: Always
        volumeMounts:
        - mountPath: /export/log/test/
          name: cache-volume
      - name: flume-sender
        resources:
          limits:
            cpu: 2000m
            memory: 4096Mi
          requests:
            cpu: 200m
            memory: 2048Mi
        image: hub-dev.example.com/base/flume-beta:v1.0
        imagePullPolicy: Always
        volumeMounts:
        - mountPath: /export/log/test/
          name: cache-volume
      volumes:
        - name: cache-volume
          emptyDir: {}
```
