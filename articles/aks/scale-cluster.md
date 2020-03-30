---
title: Skala ut ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du skalar antalet noder i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368425"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skala ut nodantalet i ett Azure Kubernetes Service-kluster

Om resursbehoven för dina program ändras kan du manuellt skala ett AKS-kluster för att köra ett annat antal noder. När du skalar ned är noder noggrant [avspärrade och tömda][kubernetes-drain] för att minimera störningar i program som körs. När du skalar upp väntar AKS `Ready` tills noder markeras av Kubernetes-klustret innan poddar schemaläggs på dem.

## <a name="scale-the-cluster-nodes"></a>Skala klusternoderna

Först får du *namnet* på din nodpool med kommandot [az aks show.][az-aks-show] I följande exempel får nodpoolnamnet för klustret *myAKSCluster* i resursgruppen *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Följande exempelutdata visar att *namnet* är *nodepool1:*

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Använd kommandot [az aks scale][az-aks-scale] för att skala klusternoderna. I följande exempel skalas ett kluster med namnet *myAKSCluster* till en enda nod. Ange ditt eget *--nodepool-namn* från föregående kommando, till exempel *nodepool1:*

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

I följande exempelutdata visas att klustret har skalats till en nod, vilket visas i avsnittet *agentPoolProfiles:*

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln skalade du manuellt ett AKS-kluster för att öka eller minska antalet noder. Du kan också använda [kluster automatisk skalning][cluster-autoscaler] för att automatiskt skala klustret.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
