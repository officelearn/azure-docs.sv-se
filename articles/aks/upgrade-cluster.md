---
title: Uppgradera ett kluster i Azure Kubernetes Service (AKS)
description: Lär dig att uppgradera ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028448"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uppgradera ett kluster i Azure Kubernetes Service (AKS)

Som en del av livscykeln för ett AKS-kluster behöver du ofta uppgradera till den senaste versionen av Kubernetes. Det är viktigt att du använder de senaste Kubernetes security versionerna eller uppgradera för att få de senaste funktionerna. Den här artikeln visar hur du uppgraderar ett befintligt AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.0.56 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Sök efter tillgängliga uppgraderingar för AKS-kluster

Du kan kontrollera vilka Kubernetes-versioner är tillgängliga för ditt kluster genom att använda den [az aks get-uppgraderingar] [ az-aks-get-upgrades] kommando. I följande exempel söker efter tillgängliga uppgraderingar till klustret med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> När du uppgraderar ett AKS-kluster kan inte Kubernetes delversioner hoppas över. Till exempel uppgraderar mellan *1.10.x* -> *1.11.x* eller *1.11.x* -> *1.12.x* tillåts, men *1.10.x* -> *1.12.x* är inte.
>
> Så här uppgraderar du, från *1.10.x* -> *1.12.x*, första uppgradera från *1.10.x* -> *1.11.x*, sedan uppgradera från *1.11.x* -> *1.12.x*.

Följande Exempelutdata visar att klustret kan uppgraderas till version *1.11.5* eller *1.11.6*:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>Uppgradera ett AKS-kluster

Med en lista över tillgängliga versioner för AKS-kluster, använda den [az aks uppgradera] [ az-aks-upgrade] kommando för att uppgradera. Under uppgraderingsprocessen AKS lägger till en ny nod i klustret, sedan noggrant [cordon och krymper] [ kubernetes-drain] en nod åt gången för att minimera störningar i program som körs. I följande exempel uppgraderar ett kluster till version *1.11.6*:

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

Det tar några minuter att uppgradera klustret, beroende på hur många noder som du har.

Kontrollera att uppgraderingen lyckades genom att använda den [az aks show] [ az-aks-show] kommando:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Följande Exempelutdata visar att klustret körs *1.11.6*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.azmk8s.io
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du uppgraderar ett befintligt AKS-kluster. Mer information om att distribuera och hantera AKS-kluster finns uppsättning självstudier.

> [!div class="nextstepaction"]
> [Självstudier om AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-upgrades]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-upgrade]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-upgrade
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show