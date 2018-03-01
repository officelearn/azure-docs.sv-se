---
title: Uppgradera ett Azure Container Service-kluster (AKS)
description: Uppgradera ett Azure Container Service-kluster (AKS)
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 9b94f858aa896eaa93430a12cd74e12d9bf02008
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Uppgradera ett Azure Container Service-kluster (AKS)

Azure Container Service (AKS) gör det enkelt att utföra vanliga hanteringsuppgifter, bland annat att uppgradera Kubernetes-kluster.

## <a name="upgrade-an-aks-cluster"></a>Uppgradera ett AKS-kluster

Innan du uppgraderar ett kluster ska du använda kommandot `az aks get-upgrades` och kontrollera vilka Kubernetes-versioner som är tillgängliga för uppgradering.

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Resultat:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

Vi har tre versioner som är tillgängliga för uppgradering: 1.7.9, 1.8.1 och 1.8.2. Vi kan använda kommandot `az aks upgrade` för att uppgradera till den senaste tillgängliga versionen.  Under uppgraderingen, noder är noggrant [cordoned och tar slut] [ kubernetes-drain] att minimera störningar för program som körs.  Innan du påbörjar en uppgradering av klustret bör du se till att du har tillräckligt med beräkningskapacitet för att hantera arbetsbelastningen när klusternoder läggs till och tas bort.

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
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
    "kubernetesVersion": "1.8.2",
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

Du kan nu bekräfta att uppgraderingen lyckades med kommandot `az aks show`.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Resultat:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Nästa steg

Läs mer om att distribuera och hantera AKS med självstudierna om AKS.

> [!div class="nextstepaction"]
> [Självstudier om AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md