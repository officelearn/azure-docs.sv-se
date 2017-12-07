---
title: Uppgradera ett Azure Container Service-kluster (AKS)
description: Uppgradera ett Azure Container Service-kluster (AKS)
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 40b55309ee4c52743b30682d8751e6e432f9bb4a
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Uppgradera ett Azure Container Service-kluster (AKS)

Azure Container Service (AKS) gör det enkelt att utföra vanliga hanteringsuppgifter, bland annat att uppgradera Kubernetes-kluster.

## <a name="upgrade-an-aks-cluster"></a>Uppgradera ett AKS-kluster

Innan du uppgraderar ett kluster ska du använda kommandot `az aks get-versions` och kontrollera vilka Kubernetes-versioner som är tillgängliga för uppgradering.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Resultat:

```console
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

Vi har tre versioner som är tillgängliga för uppgradering: 1.7.9, 1.8.1 och 1.8.2. Vi kan använda kommandot `az aks upgrade` för att uppgradera till den senaste tillgängliga versionen.  Under uppgraderingen blir noderna noggrant [avspärrade och tömda](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) för att minimera störningar i program som körs.  Innan du påbörjar en uppgradering av klustret bör du se till att du har tillräckligt med beräkningskapacitet för att hantera arbetsbelastningen när klusternoder läggs till och tas bort.

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

Du kan nu bekräfta att uppgraderingen lyckades med kommandot `az aks show`.

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

Läs mer om att distribuera och hantera AKS med självstudier om AKS.

> [!div class="nextstepaction"]
> [Självstudier om AKS](./tutorial-kubernetes-prepare-app.md)