---
title: 'Azure Container Registry SKU: er'
description: Jämför för olika tjänstnivåer som är tillgängliga i Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 7fdc1417ac524b422a12a087cf1661040efb9f8a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521966"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU: er

Azure Container Registry (ACR) är tillgänglig i flera tjänstnivåer, kallas SKU: er. Dessa SKU: er ger förutsägbara priser och flera alternativ för att justera till kapacitet och användningsmönster för ditt privata Docker-register i Azure.

| SKU | Hanterad | Beskrivning |
| --- | :-------: | ----------- |
| **Basic** | Ja | En kostnadsoptimerad startpunkt för utvecklare som lär sig Azure Container Registry. Grundläggande register har samma programmässiga funktioner som Standard och Premium (till exempel Azure Active Directory [autentisering integration](container-registry-authentication.md#individual-login-with-azure-ad), [bild borttagning][container-registry-delete], och [webhooks][container-registry-webhook]). Inkluderad lagring och dataflöde som bilden är dock bäst för lägre Användningsscenarier. |
| **Standard** | Ja | Standardregister erbjuder samma funktioner som grundläggande med ökad inkluderade lagrings- och dataflöde. Standard-register bör uppfylla behoven för de flesta produktionsscenarier. |
| **Premium** | Ja | Premium-register ger högsta mängden lagringsutrymme och samtidiga åtgärder som att aktivera scenarier med hög volym. Förutom den högre bild dataflöde Premium lägger till funktioner inklusive [geo-replikering] [ container-registry-geo-replication] för att hantera ett enda register i flera regioner [innehåll förtroende (förhandsversion)](container-registry-content-trust.md) för avbildning taggen signering, och [brandväggar och virtuella nätverk (förhandsversion)](container-registry-vnet.md) att begränsa åtkomsten till registret. |
|  Klassiska (*inte tillgänglig efter April 2019*) | Nej | Denna SKU aktiverat den första versionen av Azure Container Registry-tjänst i Azure. Klassiskt register backas upp av ett lagringskonto som skapas av Azure i din prenumeration, vilket begränsar möjligheten för ACR och programlivscykler på högre nivå, till exempel ökat genomflöde och geo-replikering. |

> [!IMPORTANT]
> Klassisk registret SKU håller **inaktuell**, och är inte tillgängliga efter **April 2019**. Vi rekommenderar att du använder Basic, Standard eller Premium för alla nya register. Alla befintliga klassiska register bör uppgraderas innan April 2019. Uppgradera information finns i [uppgradera ett klassiskt register][container-registry-upgrade].

Basic, Standard och Premium-SKU: er (gemensamt kallade *hanterade register*) får du samma programmässiga funktioner. De också alla dra nytta av [bildlagring] [ container-registry-storage] hanteras helt av Azure. Välja en högre nivå SKU ger mer prestanda och skalning. Med flera tjänstnivåer, du kan komma igång med Basic och sedan konvertera till Standard och Premium när ditt register användningen ökar.

## <a name="sku-feature-matrix"></a>SKU-funktionen matris

I följande tabell beskrivs funktionerna och begränsningarna för tjänstnivåerna Basic, Standard och Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Ändra SKU: er

Du kan ändra ett register-SKU med Azure CLI eller i Azure-portalen. Du kan flyttas fritt mellan hanterade SKU: er så länge du växlar till SKU: N har den nödvändiga maximala lagringskapaciteten. När du växlar till en av de hanterade SKU: er från klassiskt läge, kan inte du flytta tillbaka till klassisk – det är en enkelriktad konvertering.

### <a name="azure-cli"></a>Azure CLI

Om du vill flytta mellan SKU: er i Azure CLI, använder den [az acr update] [ az-acr-update] kommando. Till exempel för att växla till Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

I behållarregistret **översikt** i Azure-portalen väljer du **uppdatering**, välj sedan en ny **SKU** SKU listrutan.

![Uppdatera SKU för container registry på Azure-portalen][update-registry-sku]

Om du har ett klassiskt register kan välja du inte en hanterad SKU i Azure-portalen. I stället måste du först [uppgradera] [ container-registry-upgrade] till ett hanterat register.

## <a name="pricing"></a>Prissättning

Information om var och en av Azure Container Registry SKU: er om priser finns i [prissättning för Container Registry][container-registry-pricing].

Mer information om priser för överföring av data finns i [prisinformation för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Nästa steg

**Azure Container Registry-översikt**

Gå till den [ACR-översikt] [ acr-roadmap] på GitHub för att hitta information om kommande funktioner i tjänsten.

**Azure Container Registry UserVoice**

Skicka in och rösta på förslag på nya funktioner i [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
