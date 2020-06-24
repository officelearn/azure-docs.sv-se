---
title: Använda pooler för system-noder i Azure Kubernetes service (AKS)
description: Lär dig hur du skapar och hanterar system-nodkonfigurationer i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.openlocfilehash: 9b6270f81e7af8bd508d29510698e6cf9a5a2010
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052660"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Hantera system-nodkonfigurationer i Azure Kubernetes service (AKS)

I Azure Kubernetes service (AKS) grupperas noderna i samma konfiguration tillsammans i *noder i pooler*. Node-pooler innehåller de underliggande virtuella datorer som kör dina program. System-nodkonfigurationer och användar-Node-pooler är två olika lägen för resurspooler för dina AKS-kluster. System Node-pooler fungerar som ett primärt syfte att vara värd för kritiska system poddar, till exempel CoreDNS och tunnelfront. Pooler för användar-noder fungerar som ett primärt syfte att vara värd för din applikations poddar. Programpoddar kan dock schemaläggas på system-nodkonfigurationer om du bara vill ha en pool i ditt AKS-kluster. Varje AKS-kluster måste innehålla minst en adresspool för system med minst en nod.

> [!Important]
> Om du kör en pool med en enda pool för AKS-klustret i en produktions miljö rekommenderar vi att du använder minst tre noder för Node-poolen.

## <a name="before-you-begin"></a>Innan du börjar

* Du behöver Azure CLI version 2.3.1 eller senare installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder system-nodkonfigurationer.

* Se [kvoter, storleks begränsningar för virtuella datorer och regions tillgänglighet i Azure Kubernetes service (AKS)][quotas-skus-regions].
* AKS-klustret måste ha skapats med skalnings uppsättningar för virtuella datorer som VM-typ.
* Namnet på en Node-pool får bara innehålla gemena alfanumeriska tecken och måste börja med en gemen bokstav. För Linux-Node-pooler måste längden vara mellan 1 och 12 tecken. För Windows Node-pooler måste längden vara mellan 1 och 6 tecken.
* En API-version på 2020-03-01 eller större måste användas för att ange ett läge för Node-poolen. Kluster som skapats på API-versioner som är äldre än 2020-03-01 innehåller bara pooler för användar-noder, men kan migreras så att de innehåller system-nodkonfigurationer genom att följa [stegen i uppdaterings läget](#update-existing-cluster-system-and-user-node-pools).
* Läget för en Node-pool är en obligatorisk egenskap och måste anges explicit när du använder ARM-mallar eller direkta API-anrop.

## <a name="system-and-user-node-pools"></a>Pooler för system-och användar-noder

Noder i noden system har etiketten **Kubernetes.Azure.com/mode: system**. Varje AKS-kluster innehåller minst en adresspool för systemet. Systemnode-pooler har följande begränsningar:

* OsType för system pooler måste vara Linux.
* OsType för användar-noder kan vara Linux eller Windows.
* Systempooler måste innehålla minst en nod och användar-noder kan innehålla noll eller flera noder.
* Pooler för system-noder kräver en VM-SKU på minst 2 virtuella processorer och 4 GB minne.
* Systemnodens pooler måste ha stöd för minst 30 poddar enligt beskrivningen i den [minsta och högsta värde formeln för poddar][maximum-pods].
* Resurspooler för plats kräver användarens noder.

Du kan utföra följande åtgärder med Node-pooler:

* Ändra en adresspool till en adresspool, förutsatt att du har en annan adresspool som tar sitt ställe i AKS-klustret.
* Ändra en pool för användar-Node så att den är en system Node-pool.
* Ta bort pooler för användar-noder.
* Du kan ta bort systemnoder, förutsatt att du har en annan adresspool som tar sitt ställe i AKS-klustret.
* Ett AKS-kluster kan ha flera pooler för system och kräver minst en pool för system-Node.
* Om du vill ändra olika inställningar i befintliga resurspooler kan du skapa nya resurspooler för att ersätta dem. Ett exempel är att lägga till en ny Node-pool med en ny maxPods-inställning och ta bort den gamla Node-poolen.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Skapa ett nytt AKS-kluster med en pool för system-Node

När du skapar ett nytt AKS-kluster skapar du automatiskt en adresspool med en enda nod. Den inledande nodens standardpool är som standard ett läge av typen system. När du skapar nya Node-pooler med AZ AKS nodepool Add, är de noderna för användar-noden, såvida du inte uttryckligen anger läges parametern.

I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i regionen *östra* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* med en mediepool som innehåller en nod. Se till att du använder system-nodkonfigurationer med minst tre noder för dina produktions arbets belastningar. Den här åtgärden kan ta flera minuter att slutföra.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Lägga till en pool för system-Node i ett befintligt AKS-kluster

Du kan lägga till en eller flera språknode-pooler i befintliga AKS-kluster. Följande kommando lägger till en Node-pool av läges typ system med ett standard antal tre noder.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Visa information om Node-poolen

Du kan kontrol lera informationen om Node-poolen med följande kommando.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Ett läge av typen **system** har definierats för system-nodkonfigurationer och ett läge av typen **användare** definieras för användar-noder i pooler.

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

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Uppdatera befintliga kluster system och pooler för användar-noder

> [!NOTE]
> En API-version på 2020-03-01 eller större måste användas för att ange ett system Node pool-läge. Kluster som skapats på API-versioner som är äldre än 2020-03-01 innehåller bara pooler för användar-noder som ett resultat. Om du vill ta emot funktionaliteten för systemnode-pooler och fördelar med äldre kluster uppdaterar du läget för befintliga nodkonfigurationer med följande kommandon i den senaste versionen av Azure CLI.

Du kan ändra lägen för både system-och användar-Node-pooler. Du kan bara ändra en adresspool för systemet till en adresspool om det redan finns en adresspool i AKS-klustret.

Det här kommandot ändrar en adresspool för systemet till en adresspool.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Det här kommandot ändrar en pool för användar-Node till en adresspool.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Ta bort en pool för system-Node

> [!Note]
> Om du vill använda AKS-kluster före API-version 2020-03-02 lägger du till en ny system-adresspool och tar sedan bort den ursprungliga standardnoden.

Tidigare kunde du inte ta bort noden system Node, som var den första standardnoden i ett AKS-kluster. Nu har du möjlighet att ta bort alla noder från klustren. Eftersom AKS-kluster kräver minst en adresspool måste du ha minst två resurspooler för system på ditt AKS-kluster innan du kan ta bort ett av dem.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar och hanterar system-nodkonfigurationer i ett AKS-kluster. Mer information om hur du använder flera Node-pooler finns i [använda flera noder][use-multiple-node-pools].

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
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node