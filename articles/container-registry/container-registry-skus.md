---
title: 'Azure-behållaren registret SKU: er'
description: Jämför de olika tjänstnivåerna som är tillgängliga i Azure Container registret.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: a8dcc6fc60b80a19c4edebd57fdad5bb10cfdd0b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165222"
---
# <a name="azure-container-registry-skus"></a>Azure-behållaren registret SKU: er

Azure Container registret (ACR) är tillgänglig i flera servicenivåer, kallas även SKU: er. Dessa SKU: er ger förutsägbara priser och flera alternativ för att justera till kapacitet och användningsmönster privata Docker registret i Azure.

| SKU | Hanterad | Beskrivning |
| --- | :-------: | ----------- |
| **Basic** | Ja | En optimerad för kostnad startpunkten för utvecklare som Lär dig hur Azure-behållare registret. Grundläggande register har samma programmässiga funktioner som Standard och Premium (integrering med Azure Active Directory-autentisering, bild borttagning och web hook), men det finns begränsningar för storlek och användning. |
| **Standard** | Ja | Standard register erbjuder samma funktioner som grundläggande, med ökad Lagringsgränser och genomflöde för avbildningen. Standard register ska uppfylla behoven hos de flesta scenarier för produktion. |
| **Premium** | Ja | Premium-register ger högre gränser på begränsningarna, t.ex lagrings- och samtidiga åtgärder som att aktivera stora mängder. Förutom högre avbildningen genomflödeskapaciteten Premium lägger du till funktioner som [georeplikering] [ container-registry-geo-replication] upprätthålla ett nätverk Stäng register till var och en för att hantera ett enda register över flera regioner distribution. |
| Klassisk | Nej | Klassisk registret SKU aktiverat den första versionen av Azure-behållare Registry-tjänsten i Azure. Klassiska register backas upp av ett lagringskonto som Azure skapar i din prenumeration, vilket begränsar möjligheten för ACR att tillhandahålla högre kapacitet till exempel ökat genomflöde och geo-replikering. På grund av dess begränsade möjligheter planerar vi att inaktualisera klassiska SKU: N i framtiden. |

Om du väljer en högre nivå SKU ger mer prestanda och skalning, men ger alla hanterade SKU: er samma programmässiga funktioner. Med flera servicenivåer, du kan komma igång med Basic och sedan konvertera till Standard- och Premium som din registret Minnesanvändningen ökar.

> [!NOTE]
> På grund av planerad utfasningen av klassisk registret SKU rekommenderar vi du använder Basic, Standard eller Premium för alla nya register. Information om hur du konverterar din befintliga klassiska registret finns [uppgradera ett klassiskt register][container-registry-upgrade].
>

## <a name="managed-vs-unmanaged"></a>Hanterade och ohanterade

Basic, Standard och Premium-SKU: er kallas *hanteras* register och klassisk register som *ohanterade*. Den främsta skillnaden mellan två är hur dina behållaren avbildningar lagras.

### <a name="managed-basic-standard-premium"></a>Hanterad (Basic, Standard, Premium)

Hanterade register förmånen från bildlagring hanteras helt av Azure. Har visas ett lagringskonto som lagrar dina bilder inte i din Azure-prenumeration. Det finns flera fördelar med något av hanterade registret SKU: er, beskrivs djupgående i [behållaren bildlagring i Azure Container registret][container-registry-storage]. Den här artikeln fokuserar på de hanterade registret SKU: er och deras funktioner.

### <a name="unmanaged-classic"></a>Ohanterad (klassisk)

Klassiska register är ”ohanterad” som det lagringskonto som säkerhetskopierar ett klassiskt register befinner sig inom *din* Azure-prenumeration. Därför ansvarar du för hantering av storage-konto som är lagrade i behållaren-avbildningar. Med ohanterad register, du kan inte växla mellan SKU: er som behöver ändras (andra än [uppgraderar] [ container-registry-upgrade] hanterade registret), och flera hanterade register-funktioner är inte tillgängliga (t.ex. behållaren image borttagning [georeplikering][container-registry-geo-replication], och [webhooks][container-registry-webhook]).

Mer information om hur du uppgraderar ett klassiskt register till en hanterad SKU: er finns [uppgradera ett klassiskt register][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>SKU-funktionen matris

I följande tabell beskrivs funktioner och begränsningar för tjänstnivåer Basic, Standard och Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Ändra SKU: er

Du kan ändra ett register SKU med Azure CLI eller i Azure-portalen. Du kan flyttas fritt mellan hanterade SKU: er så länge du växlar till SKU: N har nödvändiga maximal lagringskapacitet. Om du växlar till en hanterad SKU: er från klassiska kan inte du flytta tillbaka till klassiskt – det är en enkelriktad konvertering.

### <a name="azure-cli"></a>Azure CLI

Om du vill flytta mellan SKU: er i Azure CLI, använder den [az acr uppdatering] [ az-acr-update] kommando. Till exempel för att växla till Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

I behållaren registret **översikt** i Azure portal, väljer **uppdatering**, välj sedan en ny **SKU** SKU listrutan.

![Uppdatera registret behållaren SKU i Azure-portalen][update-registry-sku]

Om du har ett klassiskt register kan välja du inte en hanterad SKU i Azure-portalen. I stället måste du först [uppgradera] [ container-registry-upgrade] till en hanterad registret (se [ändra från klassiska](#changing-from-classic)).

## <a name="changing-from-classic"></a>Ändra från klassiska

Det finns ytterligare saker att beakta när du migrerar en ohanterad klassisk registret av hanterade Basic, Standard och Premium-SKU: er. Om klassisk registret innehåller ett stort antal bilder och många gigabyte i storlek kan ta migreringen lite tid. Dessutom `docker push` operations inaktiverade tills migreringen är klar.

Mer information om hur du uppgraderar registret klassisk till en hanterad SKU: er finns [uppgradera ett klassiskt behållaren register][container-registry-upgrade].

## <a name="pricing"></a>Prissättning

Mer information om priser på varje Azure-behållare registret SKU: er, se [behållare registret priser][container-registry-pricing].

## <a name="next-steps"></a>Nästa steg

**Översikt över Azure-behållaren-registret**

Besök den [ACR översikt över] [ acr-roadmap] på GitHub för att hitta information om kommande funktioner i tjänsten.

**Azure-behållaren registret UserVoice**

Skicka och rösta på förslag på nya funktioner i [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
