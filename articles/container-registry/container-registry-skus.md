---
title: 'Azure Container Registry SKU: er'
description: 'Lär dig mer om funktionerna och begränsningarna i Azure Container Registry för Basic-, standard-och Premium-tjänster (SKU: er).'
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/05/2019
ms.author: danlep
ms.openlocfilehash: 19b1fb78413f82d422779b12227b4a5e2361d813
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681816"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU: er

Azure Container Registry (ACR) finns på flera tjänst nivåer, som kallas SKU: er. Dessa SKU: er tillhandahåller förutsägbar prissättning och flera alternativ för anpassning av kapacitets-och användnings mönstren för ditt privata Docker-register i Azure.

| SKU | Beskrivning |
| --- | ----------- |
| **Basic** | En kostnadsoptimerad startpunkt för utvecklare som lär sig Azure Container Registry. Grundläggande register har samma programmerings funktioner som standard och Premium (till exempel Azure Active Directory [autentisering](container-registry-authentication.md#individual-login-with-azure-ad), [avbildnings borttagning][container-registry-delete]och [Webhooks][container-registry-webhook]). Den inkluderade lagrings-och avbildnings data flödet är dock lämpligast för lägre användnings scenarier. |
| **Standard** | Standard register ger samma funktioner som Basic, med ökat lagrings utrymme och avbildnings data flöde. Standard-register bör uppfylla behoven för de flesta produktionsscenarier. |
| **Premium** | Premium register ger högsta mängd av inkluderad lagring och samtidiga åtgärder, vilket möjliggör stora volymer. Förutom högre bild data flöde lägger Premium till funktioner som [geo-replikering][container-registry-geo-replication] för att hantera ett enda register över flera regioner, [innehålls förtroende](container-registry-content-trust.md) för signering av bildtagg, [brand väggar och virtuella nätverk (för hands version)](container-registry-vnet.md) till begränsa åtkomsten till registret. |

Alla SKU: er för Basic, standard och Premium tillhandahåller samma programmerings funktioner. De har också all nytta av [image Storage][container-registry-storage] som hanteras helt av Azure. Att välja ett SKU på en högre nivå ger bättre prestanda och skalning. Med flera tjänst nivåer kan du komma igång med Basic och sedan konvertera till standard och Premium allteftersom din användning av registret ökar.

## <a name="sku-features-and-limits"></a>SKU-funktioner och begränsningar

I följande tabell beskrivs funktionerna och begränsningarna för tjänst nivåerna Basic, standard och Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Ändra SKU: er

Du kan ändra ett registers SKU med Azure CLI eller i Azure Portal. Du kan flytta fritt mellan SKU: er så länge SKU: n som du växlar till har den maximala lagrings kapaciteten som krävs. 

### <a name="azure-cli"></a>Azure CLI

Om du vill flytta mellan SKU: er i Azure CLI använder du kommandot [AZ ACR Update][az-acr-update] . Till exempel för att växla till Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

I behållarens register **Översikt** i Azure Portal väljer du **Uppdatera**och väljer sedan en ny **SKU** från List rutan SKU.

![Uppdatera container Registry-SKU: n i Azure Portal][update-registry-sku]

## <a name="pricing"></a>Priser

Pris information om var och en av Azure Container Registry SKU: er finns i [container Registry prissättning][container-registry-pricing].

Mer information om priser för data överföringar finns i [pris information för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Nästa steg

**Azure Container Registrys översikt**

Besök [ACR-översikten][acr-roadmap] om GitHub för att hitta information om kommande funktioner i tjänsten.

**Azure Container Registry UserVoice**

Skicka in och rösta på nya funktions förslag i [ACR UserVoice][container-registry-uservoice].

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
