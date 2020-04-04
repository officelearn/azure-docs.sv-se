---
title: Uppgradera ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du uppgraderar ett AKS-kluster (Azure Kubernetes Service) för att få de senaste funktionerna och säkerhetsuppdateringarna.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 183e0a85f65d24dc7133307391931bea754a456d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632610"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uppgradera ett AKS-kluster (Azure Kubernetes Service)

Som en del av livscykeln för ett AKS-kluster måste du ofta uppgradera till den senaste Kubernetes-versionen. Det är viktigt att du använder de senaste kubernetes-säkerhetsversionerna eller uppgraderar för att få de senaste funktionerna. Den här artikeln visar hur du uppgraderar huvudkomponenterna eller en enda standardnodpool i ett AKS-kluster.

För AKS-kluster som använder flera nodpooler eller Windows Server-noder (för närvarande i förhandsversionen i AKS) finns [uppgradera en nodpool i AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.0.65 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

> [!WARNING]
> En AKS-klusteruppgradering utlöser en avspärrning och tömning av dina noder. Om du har en låg beräkningskvot tillgänglig kan uppgraderingen misslyckas. Se [öka kvoterna](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för mer information.
> Om du kör din egen kluster automatisk skalning distribution inaktivera den (du kan skala den till noll repliker) under uppgraderingen eftersom det finns en chans att det kommer att störa uppgraderingsprocessen. Hanterad automatisk skalning automatiskt hanterar detta. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Sök efter tillgängliga AKS-klusteruppgraderingar

Om du vill kontrollera vilka Kubernetes-versioner som är tillgängliga för klustret använder du kommandot [az aks get-upgrades.][az-aks-get-upgrades] I följande exempel söker efter tillgängliga uppgraderingar till klustret som heter *myAKSCluster* i resursgruppen *myResourceGroup:*

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> När du uppgraderar ett AKS-kluster kan kubernetes delversioner inte hoppas över. Till exempel tillåts uppgraderingar mellan *1.12.x* -> *1.13.x* eller *1.13.x* -> *1.14.x,* men *1.12.x* -> *1.14.x* är det inte.
>
> För att uppgradera, från *1.12.x* -> *1.14.x*, först uppgradera från *1.12.x* -> *1.13.x*och uppgradera sedan från *1.13.x* -> *1.14.x*.

Följande exempelutdata visar att klustret kan uppgraderas till *versionerna 1.13.9* och *1.13.10:*

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Om ingen uppgradering är tillgänglig får du:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Uppgradera ett AKS-kluster

Med en lista över tillgängliga versioner för AKS-klustret använder du kommandot [az aks upgrade][az-aks-upgrade] för att uppgradera. Under uppgraderingsprocessen lägger AKS till en ny nod i klustret som kör den angivna Kubernetes-versionen och sedan försiktigt [avspärra och tömmer][kubernetes-drain] en av de gamla noderna för att minimera störningar i program som körs. När den nya noden bekräftas som programenheter som körs tas den gamla noden bort. Den här processen upprepas tills alla noder i klustret har uppgraderats.

I följande exempel uppgraderas ett kluster till version *1.13.10:*

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Det tar några minuter att uppgradera klustret, beroende på hur många noder du har. 

> [!NOTE]
> Det finns en total tillåten tid för en klusteruppgradering att slutföras. Denna tid beräknas genom att `10 minutes * total number of nodes in the cluster`ta produkten av . I ett kluster med 20 noder måste till exempel uppgraderingsåtgärderna lyckas på 200 minuter eller så misslyckas AKS åtgärden för att undvika ett klustertillstånd som inte kan återställas. Om du vill återställa vid uppgraderingsfel försöker du återuppta uppgraderingsåtgärden efter att tidsgränsen har nåtts.

För att bekräfta att uppgraderingen lyckades använder du kommandot [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Följande exempelutdata visar att klustret nu körs *1.13.10:*

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du uppgraderar ett befintligt AKS-kluster. Mer information om hur du distribuerar och hanterar AKS-kluster finns i uppsättningen självstudier.

> [!div class="nextstepaction"]
> [AKS-självstudier][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
