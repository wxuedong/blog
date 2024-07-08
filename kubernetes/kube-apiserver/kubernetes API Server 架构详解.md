# kubernetes API Server 架构详解

## 概述

K8s API Server提供了k8s各类资源对象（Pod,Deployment,Service等）的增删改查及watch等HTTP Rest接口，是整个系统的数据总线和数据中心。


kubernetes API Server的功能：

* 提供了集群管理的REST API接口(包括认证授权、数据校验以及集群状态变更)
* 提供其他模块之间的数据交互和通信的枢纽（其他模块通过API Server查询或修改数据，只有API Server才直接操作etcd）;
* 是资源配额控制的入口
* 拥有完备的集群安全机制

<center><img src="./images/apiserver.png"></center><br />

## API 扩展机制
kube-apiserver 内部实现了下面这样一个 workflow

<center><img src="./images/kube-apiserver-internal.png"></center><br/>


* kube-aggregator：处理本节这种反向代理需求，将请求转发给 API 对应的用户服务；如果没有命中，转 2；
* kube resources：处理内置的 pods, services 等内置资源；如果没有命中，转 3；
* apiextensions-apiserver：处理 CRD 资源的请求；如果没有命中，转 4；
* 返回 404。