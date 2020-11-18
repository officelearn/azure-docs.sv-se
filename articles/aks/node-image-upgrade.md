---
title: Uppgradera Node-avbildningar för Azure Kubernetes service (AKS)
description: Lär dig hur du uppgraderar avbildningarna på AKS-klusternoder och Node-pooler.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 211190228c1ea9c98004b55da96ad38808821d67
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682391"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Uppgradering av noden Azure Kubernetes service (AKS)

AKS stöder uppgradering av avbildningar på en nod så att du är uppdaterad med de senaste uppdateringarna för operativ systemet och körning. AKS tillhandahåller en ny avbildning per vecka med de senaste uppdateringarna, så det är bra att uppgradera nodens avbildningar regelbundet för de senaste funktionerna, inklusive Linux-eller Windows-korrigeringsfiler. I den här artikeln lär du dig hur du uppgraderar AKS-klusternoder samt hur du uppdaterar avbildningar av noder utan att uppgradera versionen av Kubernetes.

Om du är intresse rad av att lära dig om de senaste avbildningarna som tillhandahålls av AKS kan du läsa mer i [AKS-versions informationen](https://github.com/Azure/AKS/releases) .

Information om hur du uppgraderar Kubernetes-versionen för klustret finns i [uppgradera ett AKS-kluster][upgrade-cluster].

## <a name="limitations"></a>Begränsningar

* AKS-klustret måste använda skalnings uppsättningar för virtuella datorer för noderna.

## <a name="install-the-aks-cli-extension"></a>Installera AKS CLI-tillägget

Innan nästa kärn CLI-version släpps behöver du *AKS-Preview CLI-* tillägget för att kunna använda noden avbildnings uppgradering. Använd kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Uppgradera alla noder i alla noder i pooler

Uppgradering av Node-avbildningen görs med `az aks upgrade` . Använd följande kommando för att uppgradera Node-avbildningen:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Under uppgraderingen kontrollerar du status för Node-avbildningarna med följande `kubectl` kommando för att hämta etiketterna och filtrera ut den aktuella nodens avbildnings information:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

När uppgraderingen är klar använder `az aks show` du för att hämta den uppdaterade nodens resurspool. Den aktuella nodens avbildning visas i `nodeImageVersion` egenskapen.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Uppgradera en angiven Node-pool

Att uppgradera avbildningen på en Node-pool liknar att uppgradera avbildningen på ett kluster.

Om du vill uppdatera OS-avbildningen för Node-poolen utan att utföra en Kubernetes kluster uppgradering använder du `--node-image-only` alternativet i följande exempel:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Under uppgraderingen kontrollerar du status för Node-avbildningarna med följande `kubectl` kommando för att hämta etiketterna och filtrera ut den aktuella nodens avbildnings information:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

När uppgraderingen är klar använder `az aks nodepool show` du för att hämta den uppdaterade nodens resurspool. Den aktuella nodens avbildning visas i `nodeImageVersion` egenskapen.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Uppgradera Node-avbildningar med överspänning för nod

Om du vill påskynda uppgraderings processen för Node-avbildningen kan du uppgradera dina Node-avbildningar med hjälp av ett anpassningsbart värde för en nod. Som standard använder AKS ytterligare en nod för att konfigurera uppgraderingar.

Om du vill öka hastigheten på uppgraderingar använder du `--max-surge` värdet för att konfigurera antalet noder som ska användas för uppgraderingar så att de blir snabbare. Mer information om kompromisser med olika `--max-surge` inställningar finns i [Anpassa uppgradering av nod överspänning][max-surge].

Följande kommando anger Max värdet för överspänning för att utföra en uppgradering av noden avbildning:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Under uppgraderingen kontrollerar du status för Node-avbildningarna med följande `kubectl` kommando för att hämta etiketterna och filtrera ut den aktuella nodens avbildnings information:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Använd `az aks nodepool show` för att hämta den uppdaterade informationen om Node-poolen. Den aktuella nodens avbildning visas i `nodeImageVersion` egenskapen.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Nästa steg

- Mer information om de senaste Node-avbildningarna finns i [AKS-versions anteckningarna](https://github.com/Azure/AKS/releases) .
- Lär dig hur du uppgraderar Kubernetes-versionen med [uppgradera ett AKS-kluster][upgrade-cluster].
- [Tillämpa säkerhets-och kernel-uppdateringar på Linux-noder i Azure Kubernetes service (AKS)][security-update]
- Lär dig mer om flera noder och hur du uppgraderar noder med [skapa och hantera flera noder][use-multiple-node-pools]i pooler.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
