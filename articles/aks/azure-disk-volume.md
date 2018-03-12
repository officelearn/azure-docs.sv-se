---
title: "Använda Azure-diskarna med AKS"
description: "Använda Azure-diskarna med AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e6b2d6e31a843259001a36ffb8c588c879a3f2b9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="volumes-with-azure-disks"></a>Volymer med Azure-diskar

Behållaren-baserade program behöver ofta åtkomst och spara data i en volym på externa data. Azure-diskar som kan användas som den här externa datalager. Den här artikeln information med hjälp av en Azure-disken som Kubernetes volymer i ett kluster med Azure Container Service (AKS).

Mer information om Kubernetes volymer finns [Kubernetes volymer][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Skapa en Azure-disken

Innan du monterar en Azure disk som hanteras som en Kubernetes volym på disken måste finnas i samma resursgrupp som klusterresurser AKS. Du hittar den här resursgruppen genom att använda den [az grupplistan] [ az-group-list] kommando.

```azurecli-interactive
az group list --output table
```

Du söker efter en resursgrupp med ett namn som liknar `MC_clustername_clustername_locaton`, där klusternamn är namnet på klustret AKS och platsen är Azure-regionen där klustret har distribuerats.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Använd den [az disk skapa] [ az-disk-create] kommando för att skapa Azure-disken. 

Med det här exemplet kan uppdatera `--resource-group` med namnet på resursgruppen och `--name` till ett valfritt namn.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Du bör se utdata som liknar följande när disken har skapats. Det här värdet är disk-ID som används när du monterar disken till en Kubernetes baljor.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-file-share-as-volume"></a>Montera filresursen som volym

Montera Azure-disken till din baljor genom att konfigurera volymen i behållaren-specifikationen. 

Skapa en ny fil med namnet `azure-disk-pod.yaml` med följande innehåll. Uppdatera `diskName` med namnet på den nya disken och `diskURI` med disk-ID. Dessutom anteckna den `mountPath`, det här är den sökväg som Azure-disken har monterats i baljor.

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
