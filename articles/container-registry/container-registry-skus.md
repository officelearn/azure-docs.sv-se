---
title: 'Azure Container Registry SKU: er'
description: Jämför för olika tjänstnivåer som är tillgängliga i Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2018
ms.author: danlep
ms.openlocfilehash: 80ee1033ef7a5fee849126ab2b614de18b71e051
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855858"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU: er

Azure Container Registry (ACR) är tillgänglig i flera tjänstnivåer, kallas SKU: er. Dessa SKU: er ger förutsägbara priser och flera alternativ för att justera till kapacitet och användningsmönster för ditt privata Docker-register i Azure.

| SKU | Hanterad | Beskrivning |
| --- | :-------: | ----------- |
| **Basic** | Ja | En kostnadsoptimerad startpunkt för utvecklare som lär sig Azure Container Registry. Grundläggande register har samma programmässiga funktioner som Standard och Premium (integrering med Azure Active Directory-autentisering, bild borttagning och webhooks). Inkluderad lagring och dataflöde som bilden är dock bäst för lägre Användningsscenarier. |
| **Standard** | Ja | Standardregister erbjuder samma funktioner som grundläggande med ökad inkluderade lagrings- och dataflöde. Standard-register bör uppfylla behoven för de flesta produktionsscenarier. |
| **Premium** | Ja | Premium-register ger högsta mängden lagringsutrymme och samtidiga åtgärder som att aktivera scenarier med hög volym. Förutom högre bild dataflöde Premium lägger du till funktioner som [georeplikering] [ container-registry-geo-replication] för att hantera ett enda register i flera regioner och [innehåll förtroende (förhandsversion)](container-registry-content-trust.md)för signering av bild taggen. |
| Klassiska<sup>1</sup> | Nej | Denna SKU aktiverat den första versionen av Azure Container Registry-tjänst i Azure. Klassiskt register backas upp av ett lagringskonto som skapas av Azure i din prenumeration, vilket begränsar möjligheten för ACR och programlivscykler på högre nivå, till exempel ökat genomflöde och geo-replikering. |

<sup>1</sup> klassiska SKU blir **inaktuell** i **mars 2019**. Använd Basic, Standard eller Premium för alla nya behållarregister.

Välja en högre nivå SKU ger mer prestanda och skalning, men innehåller alla hanterade SKU: er samma programmässiga funktioner. Med flera tjänstnivåer, du kan komma igång med Basic och sedan konvertera till Standard och Premium när ditt register användningen ökar.

## <a name="managed-vs-unmanaged"></a>Hanterade och ohanterade

Basic, Standard och Premium-SKU: er kallas *hanteras* register och klassiskt register som *ohanterade*. Den viktigaste skillnaden mellan två är hur dina behållaravbildningar lagras.

### <a name="managed-basic-standard-premium"></a>Hanterad (Basic, Standard, Premium)

Hanterade register förmånen från bildlagring hanteras helt av Azure. Det vill säga visas ett storage-konto som lagrar dina avbildningar inte i Azure-prenumerationen. Det finns flera fördelar med att använda en av hanterat register SKU: er, beskrivs djupgående i [bildlagring för behållare i Azure Container Registry][container-registry-storage]. Den här artikeln fokuserar på hanterat register-SKU: er och deras funktioner.

### <a name="unmanaged-classic"></a>Ohanterade (klassisk)

> [!IMPORTANT]
> Klassiska SKU: N är inaktuell och kommer att vara tillgänglig efter mars 2019. Använd Basic, Standard eller Premium för alla nya register.

Klassiskt register är ”ohanterade” i den mening att storage-kontot som säkerhetskopierar ett klassiskt register befinner sig inom *din* Azure-prenumeration. Därför måste ansvarar du för hanteringen av storage-konto där dina behållaravbildningar lagras. Med ohanterade register, du kan inte växla mellan SKU: er när dina behov ändras (annat än [uppgraderar] [ container-registry-upgrade] till ett hanterat register), och flera funktioner för hanterade register är inte tillgängliga (t.ex. bild för borttagning av behållare, [georeplikering][container-registry-geo-replication], och [webhooks][container-registry-webhook]).

Mer information om hur du uppgraderar ett klassiskt register till en av de hanterade SKU: er finns i [uppgradera ett klassiskt register][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>SKU-funktionen matris

I följande tabell beskrivs funktionerna och begränsningarna för tjänstnivåerna Basic, Standard och Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Ändra SKU: er

Du kan ändra ett register-SKU med Azure CLI eller i Azure-portalen. Du kan flyttas fritt mellan hanterade SKU: er så länge du växlar till SKU: N har den nödvändiga maximala lagringskapaciteten. Om du byter till en av de hanterade SKU: er från klassiskt läge kan flytta du inte tillbaka till klassisk--är det en enkelriktad konvertering.

### <a name="azure-cli"></a>Azure CLI

Om du vill flytta mellan SKU: er i Azure CLI, använder den [az acr update] [ az-acr-update] kommando. Till exempel för att växla till Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

I behållarregistret **översikt** i Azure-portalen väljer du **uppdatering**, välj sedan en ny **SKU** SKU listrutan.

![Uppdatera SKU för container registry på Azure-portalen][update-registry-sku]

Om du har ett klassiskt register kan välja du inte en hanterad SKU i Azure-portalen. I stället måste du först [uppgradera] [ container-registry-upgrade] till ett hanterat register (se [ändra från klassisk](#changing-from-classic)).

## <a name="changing-from-classic"></a>Ändra från klassiskt läge

Det finns fler saker att beakta när du migrerar en ohanterad klassiskt register till hanterade Basic, Standard eller Premium-SKU: er. Om din klassiskt register innehåller ett stort antal bilder och är många gigabyte i storlek, kan migreringen ta lite tid. Dessutom `docker push` åtgärder är inaktiverade tills migreringen är klar.

Mer information om hur du uppgraderar din klassiskt register till en av de hanterade SKU: er finns i [uppgradera ett klassiskt behållarregister][container-registry-upgrade].

## <a name="pricing"></a>Prissättning

Information om var och en av Azure Container Registry SKU: er om priser finns i [prissättning för Container Registry][container-registry-pricing].

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
[container-registry-webhook]: container-registry-webhook.md
