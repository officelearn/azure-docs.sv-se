---
title: Skala ett kluster i Azure Container Service (AKS) | Microsoft Docs
description: Skala ett Azure Container Service (AKS)-kluster.
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Kubernetes, Docker, Containers, Microservices, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 3c05ccc6d1e304b7c511015b916425347bc4d995
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="scale-an-azure-container-service-aks-cluster"></a>Skala ett Azure Container Service (AKS)-kluster

Det är enkelt att skala ett AKS kluster till ett annat antal noder.  Välj önskat antal noder och kör den `az aks scale` kommando.  Vid skalning noderna kommer att noggrant [cordoned och tar slut](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) att minimera störningar för program som körs.  När du ökar, den `az` kommandot ska vänta tills noder markeras `Ready` av Kubernetes-klustret.

## <a name="scale-the-cluster-nodes"></a>Skala klusternoderna

Använd den `az aks scale` kommando för att skala klusternoderna. I följande exempel skalar ett kluster med namnet *myK8SCluster* till en enda nod.

```azurecli-interactive
az aks scale --name myK8sCluster --resource-group myResourceGroup --agent-count 1
```

Resultat:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
  "name": "myK8sCluster",
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
        "name": "myK8sCluster",
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
    "kubernetesVersion": "1.7.7",
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

## <a name="next-steps"></a>Nästa steg

Mer information om att distribuera och hantera AKS med AKS självstudier.

> [!div class="nextstepaction"]
> [AKS självstudiekursen](./tutorial-kubernetes-prepare-app.md)
