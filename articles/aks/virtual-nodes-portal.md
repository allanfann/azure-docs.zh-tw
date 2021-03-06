---
title: 在 Azure Kubernetes Service (AKS) 中使用入口網站建立虛擬節點
description: 了解如何使用 Azure 入口網站建立使用虛擬節點執行 Pod 的 Azure Kubernetes Service (AKS) 叢集。
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 4376db8cdfa90b8d29ecd9b210e683848b4c94b4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072595"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>在 Azure 入口網站中建立和設定 Azure Kubernetes Service (AKS) 叢集以使用虛擬節點

若要在 Azure Kubernetes Service (AKS) 叢集中快速地部署工作負載，您可以使用虛擬節點。 透過虛擬節點，您可以快速佈建 Pod，而且只需在節點執行時付費 (以秒計算)。 在調整案例中，您不需要等候 Kubernetes 叢集自動調整程式來部署 VM 計算節點以執行其他 Pod。 虛擬節點，只支援 Linux pod 和節點。

本文會示範如何建立和設定啟用虛擬節點的虛擬網路資源與 AKS 叢集。

## <a name="before-you-begin"></a>開始之前

虛擬節點能夠進行在 ACI 與 AKS 叢集中執行的 pod 之間的網路通訊。 為了提供此通訊功能，需要建立虛擬網路子網路並指派委派權限。 虛擬節點只能與使用「進階」網路所建立的 AKS 叢集搭配運作。 但根據預設，系統會使用「基本」網路來建立 AKS 叢集。 本文說明如何建立虛擬網路和子網路，然後部署使用進階網路的 AKS 叢集。

如果您先前未使用 ACI，請向您的訂用帳戶註冊服務提供者。 您可以使用 [az provider list][az-provider-list] 命令來檢查 ACI 提供者註冊狀態，如以下範例所示：

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* 提供者應該回報為 *Registered*，如以下範例輸出所示：

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

如果提供者會顯示成*NotRegistered*，註冊的提供者使用 [az 提供者註冊] [az 提供者-暫存器]，如下列範例所示：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>區域可用性

針對虛擬節點部署支援下列區域：

* 澳大利亞東部 (australiaeast)
* 美國中部 (centralus)
* 美國東部 (eastus)
* 美國東部 2 (eastus2)
* 日本東部 (japaneast)
* 北歐 (northeurope)
* 東南亞 (southeastasia)
* 美國中西部 (westcentralus)
* 西歐 (westeurope)
* 美國西部 (westus)
* 美國西部 2 (westus2)

## <a name="known-limitations"></a>已知限制
虛擬節點功能是高度依賴 ACI 的功能集。 下列案例尚不支援具有虛擬節點

* 使用服務主體，以提取 ACR 映像。 [因應措施](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry)是使用[Kubernetes 祕密](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [虛擬網路限制](../container-instances/container-instances-vnet.md)包括 VNet 對等互連、 Kubernetes 網路原則，以及網路安全性群組與網際網路的輸出流量。
* 初始化容器
* [主機別名](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [引數](../container-instances/container-instances-exec.md#restrictions)在 ACI 中執行的
* [Daemonset](concepts-clusters-workloads.md#statefulsets-and-daemonsets)將無法部署到虛擬節點的 pod

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

在 Azure 入口網站的左上角，選取 [建立資源] > [Kubernetes 服務]。

在 [基本資料] 頁面上設定下列選項：

- 專案詳細資料：選取 Azure 訂用帳戶，然後選取或建立 Azure 資源群組，例如 *myResourceGroup*。 輸入 **Kubernetes 叢集名稱**，例如 myAKSCluster。
- 叢集詳細資料：選取 AKS 叢集的區域、Kubernetes 版本及 DNS 名稱前置詞。
- *主要節點的集區*:選取 AKS 節點的 VM 大小。 VM 大小**無法**在 AKS 叢集部署完畢後變更。
     - 選取要部署到叢集的節點數目。 本文將 [節點計數] 設為 1。 節點計數**可以**在叢集部署完畢後調整。

按一下 **下一步擴展**。

在 **擴展**頁面上，選取*已啟用*下**虛擬節點**。

![建立 AKS 叢集並啟用虛擬節點](media/virtual-nodes-portal/enable-virtual-nodes.png)

根據預設，系統會建立 Azure Active Directory 服務主體。 此服務主體會用於叢集通訊，以及與其他 Azure 服務整合。

叢集也會針對進階網路功能進行設定。 虛擬節點會設定為使用自己的 Azure 虛擬網路子網路。 此子網路具有委派的權限，可連線 AKS 叢集之間的 Azure 資源。 如果您還沒有委派的子網路，Azure 入口網站會建立並設定 Azure 虛擬網路和子網路，以搭配虛擬節點使用。

選取 [檢閱 + 建立]。 驗證完成之後，請選取 [建立]。

從建立 AKS 叢集到可供使用需要幾分鐘的時間。

## <a name="connect-to-the-cluster"></a>連接到叢集

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 `kubectl` 用戶端會預先安裝在 Azure Cloud Shell 中。

若要開啟 Cloud Shell，請選取程式碼區塊右上角的 [試試看]。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

若要設定 `kubectl` 來連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 下列範例會針對 myResourceGroup 資源群組中的叢集名稱 myAKSCluster 取得認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```azurecli-interactive
kubectl get nodes
```

下列輸出範例顯示單一 VM 節點已建立，然後是適用於 Linux 的虛擬節點 (virtual-node-aci-linux)：

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>部署範例應用程式

在 Azure Cloud Shell 中，建立名為 `virtual-node.yaml` 的檔案，然後將下列 YAML 複製進來。 若要在節點上排定容器，需定義 [nodeSelector][node-selector] 和 [toleration][toleration]。 這些設定會允許 Pod 在虛擬節點上進行排程，並確認該功能已成功啟用。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

使用 [kubectl apply][kubectl-apply] 命令來執行應用程式。

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

使用 [kubectl get pods][kubectl-get] 命令搭配 `-o wide` 引數來輸出 Pod 清單和排定的節點。 請注意，`virtual-node-helloworld` pod 已排定在 `virtual-node-linux` 節點上。

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod 會從 Azure 虛擬網路的子網路 (為搭配使用虛擬節點而委派) 獲派內部 IP 位址。

> [!NOTE]
> 如果您使用儲存在 Azure Container Registry 中的映像，請[設定並使用 Kubernetes 祕密][acr-aks-secrets]。 虛擬節點的目前限制是，您無法使用整合式的 Azure AD 服務主體驗證。 如果您未使用祕密，已在虛擬節點上排程的 Pod 就無法啟動並會回報錯誤 `HTTP response status code 400 error code "InaccessibleImage"`。

## <a name="test-the-virtual-node-pod"></a>測試虛擬節點 Pod

若要測試虛擬節點上執行的 Pod，請瀏覽至搭配 Web 用戶端的示範應用程式。 由於 Pod 會獲派內部 IP 位址，因此您可以快速地從 AKS 叢集上其他 Pod 測試此連線。 建立測試 Pod，並將終端機工作階段與它連結：

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

使用 `apt-get` 在 Pod 中安裝 `curl`：

```azurecli-interactive
apt-get update && apt-get install -y curl
```

現在您可以使用 `curl` (例如 http://10.241.0.4) 來存取 Pod 的位址。 提供前述 `kubectl get pods` 命令中您自己的 IP 位址：

```azurecli-interactive
curl -L http://10.241.0.4
```

示範應用程式會隨即顯示，如下列簡要範例輸出所示：

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

使用 `exit` 來關閉測試 Pod 的終端機工作階段。 當您的工作階段結束時，Pod 也會一併刪除。

## <a name="next-steps"></a>後續步驟

在本文中，Pod 已在虛擬節點上進行排程，並獲派私人的內部 IP 位址。 您可以改為建立服務部署，並透過負載平衡器或輸入控制器將流量路由到您的 Pod。 如需詳細資訊，請參閱[在 AKS 中建立基本的輸入控制器][aks-basic-ingress]。

虛擬節點是 AKS 中調整解決方案的一個元件。 如需有關調整解決方案的詳細資訊，請參閱下列文章：

- [使用 Kubernetes 水平 Pod 自動調整程式][aks-hpa]
- [使用 Kubernetes 叢集自動調整程式][aks-cluster-autoscaler]
- [針對虛擬節點檢查自動調整範例][virtual-node-autoscale]
- [深入的了解 Virtual Kubelet 開放原始碼程式庫][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
[az-provider-list]: /cli/azure/provider#az-provider-list