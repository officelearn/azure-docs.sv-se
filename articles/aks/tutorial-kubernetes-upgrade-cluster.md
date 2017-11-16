---
title: "Kubernetes på Azure tutorial – uppdatera kluster | Microsoft Docs"
description: "Kubernetes på Azure tutorial – uppdatera kluster"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d0193e891c6c41687f1aaa3a8033bc71e85b10c3
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>Uppgradera Kubernetes i Azure Container Service (AKS)

Ett kluster med Azure Container Service (AKS) kan uppgraderas med hjälp av Azure CLI. Under uppgraderingen, Kubernetes noder är noggrant [cordoned och tar slut](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) att minimera störningar för program som körs.

I den här självstudiekursen del åtta åtta, ett Kubernetes kluster har uppgraderats. Uppgifterna som du har slutfört är:

> [!div class="checklist"]
> * Identifiera aktuella och tillgängliga Kubernetes versioner
> * Uppgradera Kubernetes noder
> * Verifiera uppgraderingen

## <a name="before-you-begin"></a>Innan du börjar

I föregående självstudier, ett program som har paketerats i en behållare avbildning, avbildningen har överförts till registret för Azure-behållaren och ett Kubernetes kluster skapas. Programmet körs sedan Kubernetes klustret.

Om du inte har gjort dessa steg och vill följa med, gå tillbaka till den [kursen 1 – skapa behållaren bilder](./tutorial-kubernetes-prepare-app.md).


## <a name="get-cluster-versions"></a>Hämta kluster-versioner

Innan du uppgraderar ett kluster, använda den `az aks get-versions` kommando för att kontrollera vilka Kubernetes versioner är tillgängliga för uppgradering.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Här kan du se att den aktuella nod-versionen är `1.7.7` och den versionen `1.7.9`, `1.8.1`, och `1.8.2` är tillgängliga.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>Uppgradera kluster

Använd den `az aks upgrade` kommando för att uppgradera klusternoder. I följande exempel uppdateras klustret till version `1.8.2`.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
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

## <a name="validate-upgrade"></a>Verifiera uppgraderingen

Du kan nu bekräfta uppgraderingen lyckades med den `az aks show` kommando.

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Resultat:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Nästa steg

I kursen får uppgraderat du Kubernetes i ett AKS-kluster. Följande uppgifter har slutförts:

> [!div class="checklist"]
> * Identifiera aktuella och tillgängliga Kubernetes versioner
> * Uppgradera Kubernetes noder
> * Verifiera uppgraderingen

Den här länken om du vill veta mer om AKS.

> [!div class="nextstepaction"]
> [Översikt över AKS](./intro-kubernetes.md)