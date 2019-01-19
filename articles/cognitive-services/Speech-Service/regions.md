---
title: Regioner – Speech Services
titlesuffix: Azure Cognitive Services
description: Referens för regioner Speech-tjänsten.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: e33bf7cd98cdd5862af6f4d68d3d73de1a07d229
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413166"
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

Tillgängliga regioner för **avsiktsigenkänning** via tal SDK är följande:

 Global region | Region | Parametern för tal-SDK
 ------|-------|--------
 Asien | Östasien | `eastasia`
 Asien | Sydostasien | `southeastasia`
 Australien | Östra Australien | `australiaeast`
 Europa | Norra Europa | `northeurope`
 Europa | Västra Europa | `westeurope`
 Nordamerika | Östra USA | `eastus`
 Nordamerika | USA, östra 2 | `eastus2`
 Nordamerika | Södra centrala USA | `southcentralus`
 Nordamerika | Västra centrala USA | `westcentralus`
 Nordamerika | Västra USA | `westus`
 Nordamerika | Västra USA 2 | `westus2`
 Sydamerika | Södra Brasilien | `brazilsouth`

Det här är en delmängd av publishing regioner som stöds av den [tjänst för Språkförståelse (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

## <a name="rest-apis"></a>REST API:er

Speech-tjänsten exponerar även REST-slutpunkter för förfrågningar om tal till text och text till tal.

### <a name="speech-to-text"></a>Tal-till-text

Tal till text referensdokumentation finns [REST API: er](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Text till tal

För text till tal-referensdokumentation finns i [REST API: er](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
