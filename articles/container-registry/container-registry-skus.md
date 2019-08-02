---
title: 'Azure Container Registry SKU: er'
description: Jämför de olika tjänst nivåerna som är tillgängliga i Azure Container Registry.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: bf620178a0c10661126b3e52c7b908ccc9a90d89
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68311893"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU: er

Azure Container Registry (ACR) finns på flera tjänst nivåer, som kallas SKU: er. Dessa SKU: er tillhandahåller förutsägbar prissättning och flera alternativ för anpassning av kapacitets-och användnings mönstren för ditt privata Docker-register i Azure.

| SKU | Hanterad | Beskrivning |
| --- | :-------: | ----------- |
| **Basic** | Ja | En kostnadsoptimerad startpunkt för utvecklare som lär sig Azure Container Registry. Grundläggande register har samma programmerings funktioner som standard och Premium (till exempel Azure Active Directory [autentisering](container-registry-authentication.md#individual-login-with-azure-ad), avbildnings [borttagning][container-registry-delete]och Webhooks). [][container-registry-webhook] Den inkluderade lagrings-och avbildnings data flödet är dock lämpligast för lägre användnings scenarier. |
| **Standard** | Ja | Standard register ger samma funktioner som Basic, med ökat lagrings utrymme och avbildnings data flöde. Standard-register bör uppfylla behoven för de flesta produktionsscenarier. |
| **Premium** | Ja | Premium register ger högsta mängd av inkluderad lagring och samtidiga åtgärder, vilket möjliggör stora volymer. Förutom högre bild data flöde lägger Premium till funktioner som [geo-replikering][container-registry-geo-replication] för att hantera ett enda register över flera regioner, [innehålls förtroende](container-registry-content-trust.md) för signering av bildtagg och [brand väggar och virtuella nätverk (för hands version)](container-registry-vnet.md) till begränsa åtkomsten till registret. |
|  Klassisk (*inte tillgängligt efter 2019 april*) | Nej | Den här SKU: n aktiverade den första versionen av Azure Container Registrys tjänsten i Azure. Klassiska register backas upp av ett lagrings konto som Azure skapar i din prenumeration, vilket begränsar möjligheten för ACR att tillhandahålla funktioner på högre nivå, till exempel ökat data flöde och geo-replikering. |

> [!IMPORTANT]
> Den klassiska register-SKU:n är inaktuell och kommer inte att vara tillgänglig efter **2019 april**. Vi rekommenderar att du använder Basic, standard eller Premium för alla nya register. Alla befintliga klassiska register bör uppgraderas före 2019 april. Uppgraderings information finns i [uppgradera ett klassiskt register][container-registry-upgrade].

SKU: erna Basic, standard och Premium (kollektivt kallat *hanterade register*) tillhandahåller samma programmerings funktioner. De har också all nytta av [image Storage][container-registry-storage] som hanteras helt av Azure. Att välja ett SKU på en högre nivå ger bättre prestanda och skalning. Med flera tjänst nivåer kan du komma igång med Basic och sedan konvertera till standard och Premium allteftersom din användning av registret ökar.

## <a name="sku-feature-matrix"></a>SKU-funktions mat ris

I följande tabell beskrivs funktionerna och begränsningarna för tjänst nivåerna Basic, standard och Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Ändra SKU: er

Du kan ändra ett registers SKU med Azure CLI eller i Azure Portal. Du kan flytta fritt mellan hanterade SKU: er så länge SKU: n som du växlar till har den maximala lagrings kapaciteten som krävs. När du växlar till en av de hanterade SKU: erna från klassisk kan du inte gå tillbaka till klassisk – det är en enkelriktad konvertering.

### <a name="azure-cli"></a>Azure CLI

Om du vill flytta mellan SKU: er i Azure CLI använder du kommandot [AZ ACR Update][az-acr-update] . Till exempel för att växla till Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

I behållarens register **Översikt** i Azure Portal väljer du **Uppdatera**och väljer sedan en ny **SKU** från List rutan SKU.

![Uppdatera container Registry-SKU: n i Azure Portal][update-registry-sku]

Om du har ett klassiskt register kan du inte välja en hanterad SKU i Azure Portal. I stället måste du först [Uppgradera][container-registry-upgrade] till ett hanterat register.

## <a name="pricing"></a>Prissättning

Pris information om var och en av Azure Container Registry SKU: er finns i [container Registry prissättning][container-registry-pricing].

Mer information om priser för data överföringar finns i [pris information för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Nästa steg

**Azure Container Registrys översikt**

Besök [ACR][acr-roadmap] -översikten om GitHub för att hitta information om kommande funktioner i tjänsten.

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
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
