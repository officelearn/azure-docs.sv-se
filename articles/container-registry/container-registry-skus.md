---
title: Servicenivåer och SKU:er
description: 'Lär dig mer om funktionerna och begränsningarna på de grundläggande standard-, standard- och premiumtjänstnivåerna (SKU: er) i Azure Container Registry.'
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456281"
---
# <a name="azure-container-registry-skus"></a>SKU:er för Azure-behållarregister

Azure Container Registry (ACR) är tillgängligt på flera tjänstnivåer, så kallade SKU:er. Dessa SKU:er ger förutsägbar prissättning och flera alternativ för att anpassa till kapacitets- och användningsmönstren för ditt privata Docker-register i Azure.

| SKU | Beskrivning |
| --- | ----------- |
| **Basic** | En kostnadsoptimerad startpunkt för utvecklare som lär sig Azure Container Registry. Grundläggande register har samma programmatiska funktioner som Standard och Premium (till exempel Integrering av Azure Active [Directory-autentisering,](container-registry-authentication.md#individual-login-with-azure-ad)borttagning av [avbildningar][container-registry-delete]och [webhooks][container-registry-webhook]). Det inkluderade lagrings- och bilddataflödet är dock lämpligast för scenarier med lägre användning. |
| **Standard** | Standardregister erbjuder samma funktioner som Basic, med ökat inkluderat lagrings- och avbildningsdataflöde. Standard-register bör uppfylla behoven för de flesta produktionsscenarier. |
| **Premium** | Premium-register ger den högsta mängden inkluderad lagring och samtidiga åtgärder, vilket möjliggör scenarier med stora volymer. Förutom högre bildflöde lägger Premium till funktioner som [geo-replikering][container-registry-geo-replication] för hantering av ett enda register i flera regioner, [innehållsförtroende](container-registry-content-trust.md) för signering av avbildningstaggar, [brandväggar och virtuella nätverk (förhandsgranskning)](container-registry-vnet.md) för att begränsa åtkomsten till registret. |

De grundläggande SKU:erna basic, standard och Premium har samma programmatiska funktioner. De drar också alla nytta av [avbildningslagring][container-registry-storage] som hanteras helt av Azure. Att välja en SKU på högre nivå ger mer prestanda och skala. Med flera tjänstnivåer kan du komma igång med Basic och sedan konvertera till Standard och Premium när registeranvändningen ökar.

## <a name="sku-features-and-limits"></a>SKU-funktioner och begränsningar

I följande tabell beskrivs funktionerna och gränserna för tjänstnivåerna Basic, Standard och Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Ändra SKU:er

Du kan ändra ett registers SKU med Azure CLI eller i Azure-portalen. Du kan röra dig fritt mellan SKU:er så länge SKU du byter till har den maximala lagringskapacitet som krävs. 

### <a name="azure-cli"></a>Azure CLI

Om du vill flytta mellan SKU:er i Azure CLI använder du kommandot [az acr update.][az-acr-update] Om du till exempel vill växla till Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

Välj **Uppdatera**i **listrutan** SKU i behållarregistret Översikt i Azure-portalen och välj sedan en ny **SKU** i listrutan SKU.

![Uppdatera behållarregistret SKU i Azure-portalen][update-registry-sku]

## <a name="pricing"></a>Prissättning

Prisinformation om var och en av Azure Container Registry SKU:er finns i [prissättningen för behållarregister][container-registry-pricing].

Mer information om prissättning för dataöverföringar finns i [Information om bandbreddsprissättning](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Nästa steg

**Översikt över Azure-behållarregister**

Besök [ACR-översikten][acr-roadmap] på GitHub för att hitta information om kommande funktioner i tjänsten.

**Azure-behållare register UserVoice**

Skicka in och rösta om nya funktionsförslag i [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
