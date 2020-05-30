---
title: Azure Kubernetes service (AKS) med SLA för drift tid
description: Lär dig mer om SLA-erbjudandet (tillval) för API-servern för Azure Kubernetes service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: references_regions
ms.openlocfilehash: 986bb835f319cebf3006d7e1bd31bd7f04a1ae82
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193998"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>SLA för Azure Kubernetes service (AKS) drift tid

SLA för drift tid är en valfri funktion för att aktivera ett finansiellt, högre service avtal för ett kluster. SLA för drift tid garanterar 99,95% tillgänglighet för Kubernetes API Server-slutpunkten för kluster som använder [Tillgänglighetszoner][availability-zones] och 99,9% av tillgänglighet för kluster som inte använder Tillgänglighetszoner. AKS använder huvudnoders repliker över uppdaterings-och fel domäner för att säkerställa att SLA-kraven är uppfyllda.

Kunder som behöver ett service avtal för att uppfylla kraven på efterlevnad eller som kräver utökning av ett service avtal för slutanvändarna bör aktivera den här funktionen. Kunder med kritiska arbets belastningar som kommer att ha nytta av ett service avtal med högre drift tid kan också dra nytta av. Med hjälp av SLA-funktionen för drift tid med Tillgänglighetszoner möjliggör en högre tillgänglighet för drift tiden för Kubernetes API-servern.  

Kunder kan ändå skapa obegränsat antal kostnads fria kluster med ett service nivå mål på 99,5% och välja önskat service nivå mål eller drift tid för service nivå avtal vid behov.

> [!Important]
> För kluster med utgående låsning, se [begränsa utgående trafik](limit-egress-traffic.md) för att öppna lämpliga portar.

## <a name="sla-terms-and-conditions"></a>Villkor för SLA

SLA för drift tid är en betald funktion och aktive rad per kluster. SLA-priser för drift tid bestäms av antalet diskreta kluster och inte av storleken på de enskilda klustren. Du kan visa [SLA pris information för drift tid](https://azure.microsoft.com/pricing/details/kubernetes-service/) för mer information.

## <a name="region-availability"></a>Tillgänglighet för regioner

SLA för drift tid är tillgängligt i följande regioner:

* Australien, östra
* Kanada, centrala
* USA, östra
* USA, östra 2
* USA, södra centrala
* Sydostasien
* USA, västra 2

## <a name="before-you-begin"></a>Innan du börjar

* Azure CLI-version 2.7.0 eller senare

## <a name="creating-a-cluster-with-uptime-sla"></a>Skapa ett kluster med SLA för drift tid

Om du vill skapa ett nytt kluster med SLA för drift tid använder du Azure CLI.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Azure Monitor för containrar aktiveras också med hjälp av parametern *--enable-addons monitoring*.  Den här åtgärden tar flera minuter att slutföra.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret. Följande JSON-kodfragment visar den betalda nivån för SKU: n, vilket anger att ditt kluster har Aktiver ATS med SLA för drift tid.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>Begränsningar

* För närvarande går det inte att konvertera som befintligt kluster för att aktivera SLA för drift tid.
* Det finns för närvarande inget sätt att ta bort service avtal för drift tid från ett AKS-kluster när du har skapat det med aktiverat.  
* Privata kluster stöds inte för närvarande.

## <a name="next-steps"></a>Nästa steg

Använd [Tillgänglighetszoner][availability-zones] för att öka hög tillgänglighet med AKS-klustrets arbets belastningar.
Konfigurera klustret för att [begränsa utgående trafik](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
