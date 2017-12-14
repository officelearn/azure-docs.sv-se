---
title: "Azure-behållaren registret SKU: er"
description: "Jämförelser mellan de olika tjänstnivåerna som är tillgängliga i Azure Container registret"
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: cf7724dd9e3e870cfd7ec0b5d2e4ea1d0694e9de
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-skus"></a>Azure-behållaren registret SKU: er

Azure Container registret (ACR) är tillgänglig i flera servicenivåer, kallas även SKU: er. Dessa SKU: er ger förutsägbara priser och flera alternativ för hur du vill använda privata Docker registret i Azure. Om du väljer en högre nivå SKU ger mer prestanda och skalning. Alla SKU: er ger dock samma programmässiga funktioner att aktivera en utvecklare Kom igång med Basic och konvertera till Standard- och Premium som registret Minnesanvändningen ökar.

## <a name="basic"></a>Basic
En optimerad för kostnad startpunkten för utvecklare som Lär dig hur Azure-behållare registret. Grundläggande register har samma programmässiga funktioner som Standard och Premium (integrering med Azure Active Directory-autentisering, bild borttagning och web hook), men det finns begränsningar för storlek och användning.

## <a name="standard"></a>Standard
Standard register erbjuder samma funktioner som grundläggande, med ökad Lagringsgränser och genomflöde för avbildningen. Standard register ska uppfylla behoven hos de flesta scenarier för produktion.

## <a name="premium"></a>Premium
Premium-register ger högre gränser på begränsningarna, t.ex lagrings- och samtidiga åtgärder som att aktivera stora mängder. Förutom högre avbildningen genomflödeskapaciteten Premium lägger du till funktioner som [georeplikering](container-registry-geo-replication.md) för att hantera ett enda register över flera regioner, underhålla nätverket Stäng register i varje distribution.

## <a name="classic"></a>Klassisk
Klassisk registret SKU aktiverat den första versionen av Azure-behållare Registry-tjänsten i Azure. Klassiska register backas upp av ett lagringskonto som Azure skapar i din prenumeration, vilket begränsar möjligheten för ACR att tillhandahålla högre kapacitet till exempel ökat genomflöde och geo-replikering. På grund av dess begränsade möjligheter planerar vi att inaktualisera klassiska SKU: N i framtiden.

> [!NOTE]
> På grund av planerad utfasningen av klassisk registret SKU rekommenderar vi du använder Basic, Standard eller Premium för alla nya register. Information om hur du konverterar din befintliga klassiska registret finns [ändra SKU: er](#changing-skus).
>

## <a name="registry-sku-feature-matrix"></a>Registret SKU funktionen matris

I följande tabell beskrivs funktioner och begränsningar för tjänstnivåer Basic, Standard och Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="manage-registry-size"></a>Hantera registret storlek
Begränsningar för lagring på varje SKU är avsedda att justera ett typiskt scenario: grundläggande för att komma igång, Standard för flesta appar produktions- och Premium för storskaliga prestanda och [georeplikering](container-registry-geo-replication.md). Du bör hantera dess storlek genom att regelbundet ta bort oanvända innehåll under hela livslängd i registret.

Du hittar den nuvarande användningen av ett register i behållaren registret **översikt** i Azure-portalen:

![Information om användning av registret i Azure-portalen](media/container-registry-skus/registry-overview-quotas.png)

Du kan hantera storleken på registret genom att ta bort databaser i Azure-portalen.

Under **SERVICES**väljer **databaser**, högerklicka på databasen som du vill ta bort och välj sedan **ta bort**.

![Ta bort en databas på Azure-portalen](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>Ändra SKU: er

Du kan ändra ett register SKU i Azure-portalen.

I registret **översikt** i Azure portal, väljer **uppdatering**, välj sedan en ny **SKU** SKU listrutan.

![Uppdatera registret behållaren SKU i Azure-portalen](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>Ändra från klassiska
När du ändrar ett klassiskt register till Basic, Standard eller Premium, Azure kopior befintliga behållaren bilder från det associerade lagringskontot i din prenumeration till ett lagringskonto som hanteras av Azure. Den här processen kan ta lite tid.

Under konverteringen, `docker pull` fortsätter att fungera, men `docker push` är spärrat tills konverteringen har slutförts.

När slutfört, används inte längre prenumerationskonto för lagring av ACR.

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>Varför ändra från klassiska till Basic, Standard eller Premium?

På grund av begränsade möjligheter för klassisk register rekommenderar vi att du ändrar klassisk-register för nivåerna Basic, Standard och Premium. Dessa på högre nivå SKU: er integrera djupare registret funktionerna i Azure. Några av dessa funktioner är:

* Azure Active Directory-integrering för enskilda autentisering (se [az acr inloggning](/cli/azure/acr?view=azure-cli-latest#az_acr_login))
* Stöd för avbildningen och tagg borttagning
* [Geo-replikering](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

De flesta av alla, beror ett klassiskt register på lagringskontot att Azure etablerade i Azure-prenumerationen automatiskt när du skapade i registret. Däremot Basic, Standard och Premium-SKU: er dra nytta av *hanteras lagring*. Det vill säga Azure transparent hanterar lagring på bilderna du--skapas inte ett separat lagringskonto i din egen prenumeration.

Några av fördelarna med hanterade lagringspoolen som tillhandahålls av Basic, Standard och Premium register:

* Behållaren bilderna [krypterat i vila](../storage/common/storage-service-encryption.md).
* Avbildningar lagras med [geo-redundant lagring](../storage/common/storage-redundancy.md#geo-redundant-storage), tillåta säkerhetskopiering av dina bilder med flera regioner replikering.
* Möjligheten att [flytta till mellan SKU: er](#changing-skus), aktivera högre kapacitet när du väljer en högre nivå SKU. Med varje SKU uppfyller ACR dina behov för genomströmning som dina behov öka. Den underliggande implementeringen av hur ACR uppnår det önskade genomflödet uttrycks som *avsikt* (genom att välja högre SKU: er), utan att du behöver hantera information om genomförandet.

## <a name="pricing"></a>Prissättning

Mer information om priser på varje Azure-behållare registret SKU: er, se [behållare registret priser](https://azure.microsoft.com/pricing/details/container-registry/).

## <a name="next-steps"></a>Nästa steg

**Översikt över Azure-behållaren-registret**

Besök den [ACR översikt över](https://aka.ms/acr/roadmap) på GitHub för att hitta information om kommande funktioner i tjänsten.

**Azure-behållaren registret UserVoice**

Skicka och rösta på förslag på nya funktioner i [ACR UserVoice](https://feedback.azure.com/forums/903958-azure-container-registry).