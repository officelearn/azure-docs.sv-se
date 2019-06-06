---
title: Skala ett kluster i Azure Kubernetes Service (AKS)
description: Lär dig mer om att skala antalet noder i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfoulds
ms.openlocfilehash: de3f8613c93715aecf7e9e066a8ad1d82e4379e3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475126"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skala antalet noder i ett kluster i Azure Kubernetes Service (AKS)

Om resursbehov för ditt program ändras kan du skala manuellt ett AKS-kluster för att köra ett annat antal noder. När du skalar noder är noggrant [avspärrade och tömda] [ kubernetes-drain] att minimera störningar i program som körs. När du skalar upp AKS ska vänta tills noderna är markerade `Ready` av Kubernetes-klustret innan poddar schemaläggs på dem.

## <a name="scale-the-cluster-nodes"></a>Skala klusternoderna

Hämta först det *namn* av din nod poolen med hjälp av den [az aks show] [ az-aks-show] kommando. I följande exempel hämtas pool nodnamnet för klustret med namnet *myAKSCluster* i den *myResourceGroup* resursgrupp:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Följande Exempelutdata visar att den *namn* är *nodepool1*:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

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

Använd den [az aks skala] [ az-aks-scale] kommando för att skala klusternoderna. I följande exempel skalas ett kluster med namnet *myAKSCluster* till en enda nod. Ange din egen *--nodepool-name* från föregående kommando som *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Följande Exempelutdata visar klustret har har skalats till en nod, enligt den *agentPoolProfiles* avsnittet:

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

I den här artikeln får skala du manuellt ett AKS-kluster för att öka eller minska antalet noder. Du kan också använda den [kluster autoskalningen] [ cluster-autoscaler] (för närvarande i förhandsversion i AKS) du automatiskt skalar ditt kluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
