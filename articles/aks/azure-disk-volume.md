---
title: Använda Azure-diskar med AKS
description: Använda Azure-diskar med AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8aea56017d38b57d36f5f1d42e2d4e9ed1d809e5
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346101"
---
# <a name="volumes-with-azure-disks"></a>Volymer med Azure-diskar

Behållarbaserade program behöver ofta åtkomst till och bevara data i en extern datavolym. Azure-diskar som kan användas som den här externa datalager. Den här artikeln beskriver hur man använder en Azure-disk som en Kubernetes-volym i ett kluster i Azure Kubernetes Service (AKS).

Mer information om Kubernetes volymer finns i [Kubernetes volymer][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Skapa en Azure-disk

Innan du monterar en Azure-hanterade disk som en Kubernetes-volym, disken måste finnas i AKS **noden** resursgrupp. Hämta resursgruppens namn med den [az resource show] [ az-resource-show] kommando.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Använd den [az disk skapa] [ az-disk-create] kommando för att skapa Azure-disken.

Uppdatera `--resource-group` med namnet på resursgruppen som samlats in i det sista steget och `--name` till ett valfritt namn.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Du bör se utdata som liknar följande när du har skapat disken. Det här värdet är disk-ID som används vid montera disken.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```
> [!NOTE]
> Azure-hanterade diskar faktureras av SKU: N för en viss storlek. Dessa SKU: er mellan 32GiB för S4 eller P4 diskar och 4TiB för S50 eller P50 diskar. Dessutom är det dataflöde och IOPS-prestanda för en Premium managed disk beror på både SKU: N och instansstorleken för noderna i AKS-klustret. Se [priser och prestanda för hanterade diskar][managed-disk-pricing-performance].

## <a name="mount-disk-as-volume"></a>Montera disken som volym

Montera Azure-disken till din pod genom att konfigurera volymen i container-specifikationen.

Skapa en ny fil med namnet `azure-disk-pod.yaml` med följande innehåll. Uppdatera `diskName` med namnet på den nya disken och `diskURI` med disk-ID. Dessutom ta del av den `mountPath`, vilket är den sökväg där Azure-disken är monterad i en pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Använda kubectl för att skapa en pod.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Nu har du en aktiv pod med en Azure-disk monterad på den `/mnt/azure`.

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes volymer med Azure-diskar.

> [!div class="nextstepaction"]
> [Kubernetes-plugin för Azure-diskar][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
