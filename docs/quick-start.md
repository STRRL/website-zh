---
title: 快速试用（测试推荐）
sidebar_label: 快速试用（测试推荐）
---

import PickVersion from '@site/src/components/PickVersion'

本篇文档描述如何在测试环境或本机环境快速试用 Chaos Mesh。

:::note 注意

**本文档提供的 Chaos Mesh 安装方式为脚本安装，仅供快速试用。**

如果需要在生产环境或者是其他严格的非测试场景下安装，推荐使用 [Helm](https://helm.sh/)，详情请参考[使用 Helm 安装（生产推荐）](production-installation-using-helm.md)。

:::

## 环境准备

在试用之前，请先确保环境中已经部署 Kubernetes 集群。如果尚未部署 Kubernetes 集群，可以参考以下链接完成部署：

- [Kubernetes](https://kubernetes.io/docs/setup/)
- [minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kind](https://kind.sigs.k8s.io/docs/user/quick-start/)
- [K3s](https://rancher.com/docs/k3s/latest/en/quick-start/)
- [Microk8s](https://microk8s.io/)

## 快速安装

要在试用环境中安装 Chaos Mesh，请运行以下脚本：

<PickVersion className="language-bash">
curl -sSL https://mirrors.chaos-mesh.org/latest/install.sh | bash
</PickVersion>

:::note 注意

- 如果当前环境为 [kind](https://kind.sigs.k8s.io/)，请在脚本后添加 `--local kind` 参数。

  <PickVersion className="language-bash">
  curl -sSL https://mirrors.chaos-mesh.org/latest/install.sh | bash -s -- --local kind
  </PickVersion>

  若需要指定 kind 版本，请在脚本后添加 `--kind-version xxx` 参数，如：

  <PickVersion className="language-bash">
  curl -sSL https://mirrors.chaos-mesh.org/latest/install.sh | bash -s -- --local kind --kind-version v0.10.0
  </PickVersion>

- 如果当前环境为 [K3s](https://k3s.io/)，请在脚本后添加 `--k3s` 参数。

  <PickVersion className="language-bash">
  curl -sSL https://mirrors.chaos-mesh.org/latest/install.sh | bash -s -- --k3s
  </PickVersion>

- 如果当前环境为 [Microk8s](https://microk8s.io/)，请在脚本后添加 `--microk8s` 参数。

  <PickVersion className="language-bash">
  curl -sSL https://mirrors.chaos-mesh.org/latest/install.sh | bash -s -- --microk8s
  </PickVersion>

:::

:::tip 建议

中国大陆地区的用户可在脚本后添加 `--docker-mirror` 参数来加快拉取镜像的速度。添加该参数后，`install.sh` 安装脚本将从 `dockerhub.azk8s.cn` 和 `gcr.azk8s.cn` 拉取镜像。

:::

运行此安装脚本后，Chaos Mesh 将会自动安装与版本相符的 CustomResourceDefinition (CRD)、所有需要的组件、及相关的 Service Account 配置。

如果想了解更多的安装细节，请参考 [`install.sh` 的源代码](https://github.com/chaos-mesh/chaos-mesh/blob/master/install.sh)。

## 验证安装

要查看 Chaos Mesh 的运行情况，请执行以下命令：

```sh
kubectl get po -n chaos-testing
```

以下是预期输出：

```sh
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

如果你的实际输出与预期输出相符，表示 Chaos Mesh 已经成功安装。

:::note 注意

如果实际输出的 `STATUS` 状态不是 `Running`，则需要运行以下命令查看 Pod 的详细信息，然后依据错误提示排查并解决问题。

```sh
# 以 chaos-controller 为例
kubectl describe po -n chaos-testing chaos-controller-manager-69fd5c46c8-xlqpc
```

:::

## 运行 Chaos 实验

验证安装完成后，你可以运行一个 Chaos 实验来体验 Chaos Mesh 的功能。

运行实验的方法，推荐参考[模拟 Pod 故障](simulate-pod-chaos-on-kubernetes.md)。成功创建实验后，你可以通过 Chaos Dashboard 观察实验的运行状态。

## 卸载 Chaos Mesh

如要卸载 Chaos Mesh，请执行以下命令：

<PickVersion className="language-bash">
curl -sSL https://mirrors.chaos-mesh.org/latest/install.sh | bash -s -- --template | kubectl delete -f -
</PickVersion>

也可以通过删除 `chaos-testing` 命名空间直接卸载 Chaos Mesh：

```sh
kubectl delete ns chaos-testing
```

## 常见问题解答

### 为什么安装后根目录会出现 `local` 目录

如果当前环境并没有安装 kind 但你在安装命令中使用了 `--local kind` 参数，`install.sh` 安装脚本将会自动安装 kind 到根目录下的 `local` 目录中。
