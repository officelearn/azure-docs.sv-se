---
title: Använda systemnodpooler i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och hanterar systemnodpooler i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: ef5400f19f68fd2da45776d220e17777f58e46e6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986322"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Hantera systemnodpooler i Azure Kubernetes Service (AKS)

I Azure Kubernetes Service (AKS) grupperas noder med samma konfiguration i *nodpooler*. Nodpooler innehåller underliggande virtuella datorer som kör dina program. Systemnodpooler och användarnodpooler är två olika nodpoollägen för AKS-kluster. Systemnodspooler är värd för viktiga systemtjänster som CoreDNS. Användarnodpooler är där du placerar dina programspecifika poddar. Varje AKS-kluster måste innehålla minst en systemnodpool med minst en nod. Om du kör en enda systemnodpool för AKS-klustret rekommenderar vi att du använder minst tre noder för nodpoolen. 

## <a name="before-you-begin"></a>Innan du börjar

* Du behöver Azure CLI version 2.3.1 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder systemnodpooler.

* Se [Kvoter, storleksbegränsningar för virtuella datorer och regiontillgänglighet i Azure Kubernetes Service (AKS)][quotas-skus-regions].
* AKS-klustret måste byggas med skaluppsättningar för virtuella datorer som vm-typ.
* Namnet på en nodpool får bara innehålla gemener alfanumeriska tecken och måste börja med en gemen bokstav. För Linux-nodpooler måste längden vara mellan 1 och 12 tecken. För Windows-nodpooler måste längden vara mellan 1 och 6 tecken.

## <a name="system-and-user-node-pools"></a>System- och användarnodpooler

Systemnodspoolnoder har var sin etikett **kubernetes.azure.com/mode: system**. Varje AKS-kluster innehåller minst en systemnodpool. Systemnodspooler har följande begränsningar:

* Systempooler osType måste vara Linux.
* Användarnodpooler osType kan vara Linux eller Windows.
* Systempooler måste innehålla minst en nod och användarnodpooler kan innehålla noll eller fler noder.
* Systemnodpooler kräver en VM-SKU med minst 2 virtuella processorer och 4 GB minne.
* Systemnodspooler måste ha stöd för minst 30 poddar enligt beskrivningen av [minsta och högsta värdeformeln för poddar][maximum-pods].
* Spotnodpooler kräver användarnodpooler.

Du kan utföra följande åtgärder med nodpooler:

* Ändra en systemnodpool till en användarnodpool, förutsatt att du har en annan systemnodpool som ska äga rum i AKS-klustret.
* Ändra en användarnodpool till en systemnodpool.
* Ta bort användarnodpooler.
* Du kan ta bort systemnodpooler, förutsatt att du har en annan systemnodpool som ska äga rum i AKS-klustret.
* Ett AKS-kluster kan ha flera systemnodpooler och kräver minst en systemnodpool.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Skapa ett nytt AKS-kluster med en systemnodpool

När du skapar ett nytt AKS-kluster skapar du automatiskt en systemnodpool med en enda nod. Den första nodpoolen är standard ett typlägessystem. När du skapar nya nodpooler med az aks nodepool add, är dessa nodpooler användarnodpooler om du inte uttryckligen anger lägesparametern.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i *regionen eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* med en systempool som innehåller en nod. För dina produktionsarbetsbelastningar, se till att du använder systemnodpooler med minst tre noder. Den här åtgärden kan ta flera minuter att slutföra.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Lägga till en systemnodpool i ett befintligt AKS-kluster

Du kan lägga till en eller flera systemnodpooler i befintliga AKS-kluster. Följande kommando lägger till en nodpool av lägestypsystem med ett standardantal på tre noder.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Visa information för nodpoolen

Du kan kontrollera information om nodpoolen med följande kommando.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Ett läge av typen **System** definieras för systemnodpooler och ett läge av typen **Användare** definieras för användarnodpooler.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>Uppdatera system- och användarnodpooler

Du kan ändra lägen för både system- och användarnodpooler. Du kan bara ändra en systemnodpool till en användarpool om det redan finns en annan systemnodpool i AKS-klustret.

Det här kommandot ändrar en systemnodpool till en användarnodpool.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Det här kommandot ändrar en användarnodpool till en systemnodpool.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Ta bort en systemnodpool

> [!Note]
> Om du vill använda systemnodpooler i AKS-kluster före API-version 2020-03-02 lägger du till en ny systemnodpool och tar sedan bort den ursprungliga standardnodpoolen.

Tidigare kunde du inte ta bort systemnodpoolen, som var den första standardnodpoolen i ett AKS-kluster. Du har nu flexibiliteten att ta bort en nodpool från klustren. Eftersom AKS-kluster kräver minst en systemnodpool måste du ha minst två systemnodpooler i AKS-klustret innan du kan ta bort en av dem.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du skapar och hanterar systemnodpooler i ett AKS-kluster. Mer information om hur du använder flera nodpooler finns i [använda flera nodpooler][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30