---
title: Uppgradera Node-avbildningar för Azure Kubernetes service (AKS)
description: Lär dig hur du uppgraderar avbildningarna på AKS-klusternoder och Node-pooler.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 20379f6a1e87c7553d6567be5b50f22bbadb8db7
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514689"
---
# <a name="preview---azure-kubernetes-service-aks-node-image-upgrades"></a>Förhands granskning – uppgraderingar för Azure Kubernetes service (AKS) Node-avbildningar

AKS stöder uppgradering av avbildningar på en nod så att du är uppdaterad med de senaste uppdateringarna för operativ systemet och körning. AKS tillhandahåller en ny avbildning per vecka med de senaste uppdateringarna, så det är bra att uppgradera nodens avbildningar regelbundet för de senaste funktionerna, inklusive Linux-eller Windows-korrigeringsfiler. I den här artikeln lär du dig hur du uppgraderar AKS-klusternoder samt hur du uppdaterar avbildningar av noder utan att uppgradera versionen av Kubernetes.

Om du är intresse rad av att lära dig om de senaste avbildningarna som tillhandahålls av AKS kan du läsa mer i [AKS-versions informationen](https://github.com/Azure/AKS/releases) .

Information om hur du uppgraderar Kubernetes-versionen för klustret finns i [uppgradera ett AKS-kluster][upgrade-cluster].

## <a name="register-the-node-image-upgrade-preview-feature"></a>Registrera funktionen för förhands granskning av Node-avbildning

Om du vill använda uppgraderings funktionen för Node-avbildningen under för hands versions perioden måste du registrera funktionen.

```azurecli
# Register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "NodeImageUpgradePreview"
```

Det tar flera minuter för registreringen att slutföras. Använd följande kommando för att kontrol lera att funktionen är registrerad:

```azurecli
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodeImageUpgradePreview')].{Name:name,State:properties.state}"
```

Under för hands versionen behöver du *AKS-Preview CLI-* tillägget för att kunna använda noden avbildnings uppgradering. Använd kommandot [AZ Extension Add] [AZ-Extension-Add] och Sök efter eventuella tillgängliga uppdateringar med hjälp av kommandot [AZ Extension Update] [AZ-Extension-Update]:

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
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
