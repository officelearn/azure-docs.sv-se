---
title: Uppgradera ett kluster i Azure Kubernetes Service (AKS)
description: Uppgradera ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 4ff2b56afc4496b6344735b4e3c813b06cee17e3
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055615"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uppgradera ett kluster i Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) gör det enkelt att utföra vanliga hanteringsuppgifter, bland annat uppgradera Kubernetes-kluster.

## <a name="upgrade-an-aks-cluster"></a>Uppgradera ett AKS-kluster

Innan du uppgraderar ett kluster ska du använda kommandot `az aks get-upgrades` och kontrollera vilka Kubernetes-versioner som är tillgängliga för uppgradering.

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Resultat:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  -------------------
default  mytestaks007     1.8.10           1.8.10             1.9.1, 1.9.2, 1.9.6
```

Vi har tre versioner som är tillgängliga för uppgradering: 1.9.1, 1.9.2 och 1.9.6. Vi kan använda kommandot `az aks upgrade` för att uppgradera till den senaste tillgängliga versionen.  Under uppgraderingsprocessen AKS lägger till en ny nod i klustret, sedan noggrant [här] [ kubernetes-drain] en nod åt gången för att minimera störningar i program som körs.

> [!NOTE]
> När du uppgraderar ett AKS-kluster kan inte Kubernetes delversioner hoppas över. Till exempel uppgraderingar mellan 1.8.x -> 1.9.x eller 1.9.x -> 1.10.x tillåts, men 1.8 -> 1.10 är inte. Om du vill uppgradera, från 1.8 1.10 -> måste du först uppgradera från 1.8 -> 1.9 och en annan gör sedan en annan uppgradering från 1.9 1.10 ->

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.9.6
```

Resultat:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.9.6",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

Kontrollera att uppgraderingen lyckades med kommandot `az aks show`.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Resultat:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.9.6                 Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Nästa steg

Läs mer om att distribuera och hantera AKS med självstudierna om AKS.

> [!div class="nextstepaction"]
> [Självstudier om AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
