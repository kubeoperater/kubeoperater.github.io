<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [这里说明一下：](#%E8%BF%99%E9%87%8C%E8%AF%B4%E6%98%8E%E4%B8%80%E4%B8%8B)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

- 1. 克隆kube-state-metrics 代码仓库

git clone https://github.com/kubernetes/kube-state-metrics.git

- 2. 修改yaml文件的image地址

- 3. apply 相应的yaml文件，创建rbac角色，账户，以及相应的部署和服务

	kubectl apply -f kube-state-metrics/kubernetes/ -n kube-system


# 这里说明一下：
因为 prometheus里的configmap里有kubernetes-stats-endport的设置，所以这里只需要部署一下 kube-state-metrics 服务就可以被动态发现和监控到。

参考资料


* [kube-state-metrics-yaml](https://github.com/kubernetes/kube-state-metrics/tree/master/kubernetes)
* [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics)
