---
title: Uppgradera ett kluster i Azure Kubernetes Service (AKS)
description: Lär dig att uppgradera ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 2cadd4b33cb52307599ce1e83eee8370ef9850fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66692774"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uppgradera ett kluster i Azure Kubernetes Service (AKS)

Som en del av livscykeln för ett AKS-kluster behöver du ofta uppgradera till den senaste versionen av Kubernetes. Det är viktigt att du använder de senaste Kubernetes security versionerna eller uppgradera för att få de senaste funktionerna. Den här artikeln visar hur du uppgraderar huvudkomponenter eller en enskild nod standardadresspool i ett AKS-kluster.

AKS-kluster som använder flera nodpooler eller Windows Server-noder (både för närvarande i förhandsversion i AKS) finns i [uppgradera en nodpool i AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.0.65 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Sök efter tillgängliga uppgraderingar för AKS-kluster

Du kan kontrollera vilka Kubernetes-versioner är tillgängliga för ditt kluster genom att använda den [az aks get-uppgraderingar] [ az-aks-get-upgrades] kommando. I följande exempel söker efter tillgängliga uppgraderingar till klustret med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> När du uppgraderar ett AKS-kluster kan inte Kubernetes delversioner hoppas över. Till exempel uppgraderar mellan *1.11.x* -> *1.12.x* eller *1.12.x* -> *1.13.x* tillåts, men *1.11.x* -> *1.13.x* är inte.
>
> Så här uppgraderar du, från *1.11.x* -> *1.13.x*, första uppgradera från *1.11.x* -> *1.12.x*, sedan uppgradera från *1.12.x* -> *1.13.x*.

Följande Exempelutdata visar att klustret kan uppgraderas till version *1.12.7* eller *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Uppgradera ett AKS-kluster

Med en lista över tillgängliga versioner för AKS-kluster, använda den [az aks uppgradera] [ az-aks-upgrade] kommando för att uppgradera. Under uppgraderingsprocessen AKS lägger till en ny nod i klustret som kör den angivna Kubernetes-versionen sedan noggrant [cordon och krymper] [ kubernetes-drain] en av de gamla noderna för att minimera störningar i körs program. När den nya noden har bekräftats som kör programmet poddar tas gamla noden bort. Den här processen upprepas tills alla noder i klustret har uppgraderats.

I följande exempel uppgraderar ett kluster till version *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Det tar några minuter att uppgradera klustret, beroende på hur många noder som du har.

Kontrollera att uppgraderingen lyckades genom att använda den [az aks show] [ az-aks-show] kommando:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Följande Exempelutdata visar att klustret körs *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du uppgraderar ett befintligt AKS-kluster. Mer information om att distribuera och hantera AKS-kluster finns uppsättning självstudier.

> [!div class="nextstepaction"]
> [Självstudier om AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
