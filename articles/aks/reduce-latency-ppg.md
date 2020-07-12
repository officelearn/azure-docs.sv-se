---
title: Använd närhets placerings grupper för att minska svars tiden för Azure Kubernetes service-kluster (AKS)
description: Lär dig hur du använder närhets placerings grupper för att minska svars tiden för AKS-klustrets arbets belastningar.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/22/2020
ms.openlocfilehash: 1bcdfb4bb3c910feeac0521308e1e7d733fbd959
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244080"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>Minska latens med närhets placerings grupper (förhands granskning)

> [!Note]
> När du använder närhets placerings grupper med AKS gäller samplaceringen endast för agent-noderna. Noden till noden och motsvarande värdbaserade Pod till Pod-fördröjningen har förbättrats. Samplaceringen påverkar inte placeringen av ett klusters kontroll plan.

När du distribuerar ditt program i Azure skapar spridning av virtuella datorer i regioner och tillgänglighets zoner nätverks svars tid, vilket kan påverka programmets övergripande prestanda. En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Vissa program som spel, teknik simuleringar och handel med hög frekvens (HFT) kräver låg latens och uppgifter som slutförs snabbt. För högpresterande data behandlings scenarier (HPC), till exempel dessa, bör du överväga att använda [närhets placerings grupper](../virtual-machines/linux/co-location.md#proximity-placement-groups) för ditt klusters Node-pooler.

## <a name="limitations"></a>Begränsningar

* Placerings gruppen närhet sträcker sig över en enda tillgänglighets zon.
* Det finns inget aktuellt stöd för AKS-kluster som använder tillgänglighets uppsättningar för virtuella datorer.
* Du kan inte ändra befintliga noder för att använda en närhets placerings grupp.

> [!IMPORTANT]
> AKS för hands versions funktioner är tillgängliga på en självbetjänings-och deltagande nivå. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och omfattas inte av service nivå avtal och begränsad garanti. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Mer information finns i följande support artiklar:
>
> - [Support principer för AKS](support-policies.md)
> - [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurser installerade:

- Tillägget AKS-Preview 0.4.53

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>Konfigurera för hands versions funktionen för närhet av placerings grupper

> [!IMPORTANT]
> När du använder närhets placerings grupper med AKS gäller samplaceringen endast för agent-noderna. Noden till noden och motsvarande värdbaserade Pod till Pod-fördröjningen har förbättrats. Samplaceringen påverkar inte placeringen av ett klusters kontroll plan.

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

Det kan ta flera minuter för registreringen. Använd kommandot nedan för att kontrol lera att funktionen är registrerad:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

Under för hands versionen behöver du *AKS-Preview CLI-* tillägget för att använda närhets placerings grupper. Använd kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
## <a name="node-pools-and-proximity-placement-groups"></a>Placerings grupper för resurspooler och närhet

Den första resurs som du distribuerar med en närhets placerings grupp kopplas till ett Data Center. Ytterligare resurser som distribueras med samma närhets placerings grupp finns i samma data Center. När alla resurser som använder närhets placerings gruppen har stoppats (frigjorts) eller tagits bort är den inte längre ansluten.

* Många noder i pooler kan associeras med en enda närhets placerings grupp.
* En Node-pool kan bara associeras med en enda närhets placerings grupp.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Skapa ett nytt AKS-kluster med en närhets placerings grupp

I följande exempel används kommandot [AZ Group Create][az-group-create] för att skapa en resurs grupp med namnet *myResourceGroup* i den *centrala* regionen. Ett AKS-kluster med namnet *myAKSCluster* skapas sedan med kommandot [AZ AKS Create][az-aks-create] . 

Accelererat nätverk förbättrar nätverks prestandan för virtuella datorer avsevärt. Vi rekommenderar att du använder närhets placerings grupper tillsammans med accelererat nätverk. Som standard använder AKS accelererat nätverk på [virtuella dator instanser som stöds](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), som inkluderar de flesta virtuella Azure-datorer med två eller flera virtuella processorer.

Skapa ett nytt AKS-kluster med en närhets placerings grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Kör följande kommando och lagra det ID som returneras:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Kommandot ger utdata som innehåller det *ID-* värde du behöver för kommande CLI-kommandon:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Använd resurs-ID för närhets placerings grupp för värdet *myPPGResourceID* i nedanstående kommando:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial node pool
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Lägg till en närhets placerings grupp i ett befintligt kluster

Du kan lägga till en närhets placerings grupp i ett befintligt kluster genom att skapa en ny Node-pool. Du kan sedan välja att migrera befintliga arbets belastningar till den nya noden och sedan ta bort den ursprungliga Node-poolen.

Använd samma närhets placerings grupp som du skapade tidigare och detta säkerställer att agent-noderna i båda noderna i AKS-klustret är fysiskt placerade i samma data Center.

Använd resurs-ID: t från den närhets placering som du skapade tidigare och Lägg till en ny Node-pool med [`az aks nodepool add`][az-aks-nodepool-add] kommandot:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Rensa

Ta bort klustret genom att använda [`az group delete`][az-group-delete] kommandot för att ta bort resurs gruppen AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [närhets placerings grupper][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
