---
title: Regioner - Taltjänst
titleSuffix: Azure Cognitive Services
description: En lista över tillgängliga regioner och slutpunkter för taltjänsten, inklusive tal-till-text, text-till-tal och talöversättning.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220472"
---
# <a name="speech-service-supported-regions"></a>Regioner som stöds av taltjänsten

Med taltjänsten kan programmet konvertera ljud till text, utföra talöversättning och dold text till tal. Tjänsten är tillgänglig i flera regioner med unika slutpunkter för API:erna Tal SDK och REST.

Talportalen för att utföra anpassade konfigurationer till din talupplevelse för alla regioner finns här:https://speech.microsoft.com

För anrop av din Taltjänst kontrollerar du att samtalet matchar regionen för din prenumeration.

## <a name="speech-sdk"></a>Speech SDK

I [Tal-SDK](speech-sdk.md)anges regioner som en sträng (till exempel `SpeechConfig.FromSubscription` som en parameter till i Tal-SDK för C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Tal-till-text, text-till-tal och översättning

Portalen för talanpassning finns här:https://speech.microsoft.com

Taltjänsten är tillgänglig i dessa regioner för **taligenkänning,** **text till tal**och **översättning:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Om du använder [Tal-SDK](speech-sdk.md)anges regioner av **regionidentifieraren** (till `SpeechConfig.FromSubscription`exempel som en parameter till ). Kontrollera att regionen matchar regionen för din prenumeration.

### <a name="intent-recognition"></a>Avsiktsigenkänning

Tillgängliga regioner för **avsiktsigenkänning** via Tal-SDK är följande:

| Global region | Region           | Regionidentifierare |
| ------------- | ---------------- | -------------------- |
| Asien          | Asien, östra        | `eastasia`           |
| Asien          | Sydostasien   | `southeastasia`      |
| Australien     | Australien, östra   | `australiaeast`      |
| Europa        | Europa, norra     | `northeurope`        |
| Europa        | Europa, västra      | `westeurope`         |
| Nordamerika | USA, östra          | `eastus`             |
| Nordamerika | USA, östra 2        | `eastus2`            |
| Nordamerika | USA, södra centrala | `southcentralus`     |
| Nordamerika | USA, västra centrala  | `westcentralus`      |
| Nordamerika | USA, västra          | `westus`             |
| Nordamerika | USA, västra 2        | `westus2`            |
| Sydamerika | Brasilien, södra     | `brazilsouth`        |

Det här är en delmängd av de publiceringsregioner som stöds av [tjänsten Språkförståelse (LUIS).](/azure/cognitive-services/luis/luis-reference-regions)

### <a name="voice-assistants"></a>Röstassistenter

[Tal-SDK](speech-sdk.md) stöder **röstassistentfunktioner** i dessa regioner:

| Region         | Regionidentifierare |
| -------------- | -------------------- |
| USA, västra        | `westus`             |
| USA, västra 2      | `westus2`            |
| USA, östra        | `eastus`             |
| USA, östra 2      | `eastus2`            |
| Europa, västra    | `westeurope`         |
| Europa, norra   | `northeurope`        |
| Sydostasien | `southeastasia`      |

## <a name="rest-apis"></a>REST API:er

Taltjänsten visar också REST-slutpunkter för tal-till-text- och text-till-tal-begäranden.

### <a name="speech-to-text"></a>Tal till text

Referensdokumentation mellan tal och text finns i [REST API för tal till text](rest-speech-to-text.md).

Slutpunkten för REST API har följande format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Ersätt `<REGION_IDENTIFIER>` med identifieraren som matchar regionen för din prenumeration från den här tabellen:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Språkparametern måste läggas till i URL:en för att undvika att ett 4xx HTTP-fel inte tas emot. Språket som är inställt på amerikansk engelska med slutpunkten för västra USA är till exempel: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Text till tal

Dokumentation för text-till-tal-referens finns i [REST API för text till tal](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
