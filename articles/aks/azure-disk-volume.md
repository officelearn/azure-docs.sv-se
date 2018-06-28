---
title: Använda Azure-diskarna med AKS
description: Använda Azure-diskarna med AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4af4620ff7a17cae76c4d5f2cf1a30ce4a3dccd8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "34597075"
---
# <a name="volumes-with-azure-disks"></a>Volymer med Azure-diskar

Behållaren-baserade program behöver ofta åtkomst och spara data i en volym på externa data. Azure-diskar som kan användas som den här externa datalager. Den här artikeln information med hjälp av en Azure-disken som Kubernetes volymer i ett kluster med Azure Kubernetes Service (AKS).

Mer information om Kubernetes volymer finns [Kubernetes volymer][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Skapa en Azure-disken

Innan du monterar en Azure-hanterade disk som en Kubernetes volym på disken måste finnas i AKS **nod** resursgruppen. Hämta resursgruppens namn med den [az resurs visa] [ az-resource-show] kommando.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Använd den [az disk skapa] [ az-disk-create] kommando för att skapa Azure-disken.

Uppdatera `--resource-group` med namnet på resursgruppen som samlats in i det sista steget, och `--name` till ett valfritt namn.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Du bör se utdata som liknar följande när disken har skapats. Det här värdet är disk-ID som används när du monterar disken.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montera disk som volym

Montera Azure-disken till din baljor genom att konfigurera volymen i behållaren-specifikationen.

Skapa en ny fil med namnet `azure-disk-pod.yaml` med följande innehåll. Uppdatera `diskName` med namnet på den nya disken och `diskURI` med disk-ID. Dessutom anteckna den `mountPath`, vilket är sökvägen där Azure-disken har monterats i baljor.

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

Använd kubectl för att skapa baljor.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Nu har du en körs baljor med en Azure-disken monterats på den `/mnt/azure`.

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes volymer med Azure-diskarna.

> [!div class="nextstepaction"]
> [Kubernetes plugin-program för Azure-diskar][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
