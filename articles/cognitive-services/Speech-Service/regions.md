---
title: Tal-regionerna
titlesuffix: Azure Cognitive Services
description: Referens för regioner Speech-tjänsten.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 8d36036332e939075ffac8763bec9c23d8e4a3f7
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712899"
---
# <a name="speech-service-supported-regions"></a>Med Taltjänsten regioner som stöds

Med taltjänsten kan ditt program kan omvandla ljud till text, utför talöversättning och konvertera text till tal. Tjänsten är tillgänglig i flera regioner med unika slutpunkter för taligenkänning SDK och REST API: er.

Se till att du använder den slutpunkt som matchar regionen för din prenumeration.

## <a name="speech-sdk"></a>Speech SDK

I den [tal-tjänst-SDK](speech-sdk.md), regioner har angetts som en sträng (till exempel som en parameter till `SpeechConfig.FromSubscription` i tal SDK för C#).

### <a name="speech-recognition-and-translation"></a>Taligenkänning och översättning

Tal-SDK är tillgänglig i dessa regioner för **taligenkänning** och **translation**:

  Region | Parametern för tal-SDK | Tal anpassning av portalen
 ------|-------|--------
 Västra USA | `westus` | https://westus.cris.ai
 USA, västra 2 | `westus2` | https://westus2.cris.ai
 Östra USA | `eastus` | https://eastus.cris.ai
 USA, östra 2 | `eastus2` | https://eastus2.cris.ai
 Östasien | `eastasia` | https://eastasia.cris.ai
 Sydostasien | `southeastasia` | https://southeastasia.cris.ai
 Norra Europa | `northeurope` | https://northeurope.cris.ai
 Västra Europa | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>Avsiktsigenkänning

**Taligenkänning** för tal SDK filresurser regioner har stöd för med LUIS. En fullständig lista över tillgängliga regioner finns i [publicering regioner och slutpunkter – LUIS](https://docs.microsoft.comazure/cognitive-services/luis/luis-reference-regions)

Tillgängliga regioner för **avsiktsigenkänning** via tal SDK listas på den [Language Understanding Tjänstesida region](/azure/cognitive-services/luis/luis-reference-regions).

För varje publishing-region som visas, använder de angivna **API Regionsnamn**. Till exempel använda `westus` för USA, västra.

## <a name="rest-apis"></a>REST API:er

Speech-tjänsten exponerar även REST-slutpunkter för förfrågningar om tal till text och text till tal.

### <a name="speech-to-text"></a>Tal-till-text

Tal till text referensdokumentation finns [REST API: er](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Text-till-tal

För text till tal-referensdokumentation finns i [REST API: er](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
