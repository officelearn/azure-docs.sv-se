---
title: Regioner – tal tjänst
titleSuffix: Azure Cognitive Services
description: En lista över tillgängliga regioner och slut punkter för tal tjänsten, inklusive tal-till-text, text till tal och tal översättning.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 409ce8b904997f2ab75f70b2138ec5b1e70a0e69
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816659"
---
# <a name="speech-service-supported-regions"></a>Regioner som stöds av tal tjänster

Med taltjänsten kan ditt program kan omvandla ljud till text, utför talöversättning och konvertera text till tal. Tjänsten är tillgänglig i flera regioner med unika slutpunkter för taligenkänning SDK och REST API: er.

Se till att du använder den slutpunkt som matchar regionen för din prenumeration.

## <a name="speech-sdk"></a>Speech SDK

I [talet SDK](speech-sdk.md)anges regioner som en sträng (till exempel som en parameter för att `SpeechConfig.FromSubscription` i tal-SDK för C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Tal till text, text till tal och översättning

Talet SDK är tillgängligt i dessa regioner för **tal igenkänning**, **text till tal**och **översättning**:

| Region           | Parametern för tal-SDK | Tal anpassning av portalen    |
| ---------------- | -------------------- | ------------------------------ |
| USA, västra          | `westus`             | https://westus.cris.ai         |
| USA, västra 2        | `westus2`            | https://westus2.cris.ai        |
| USA, östra          | `eastus`             | https://eastus.cris.ai         |
| USA, östra 2        | `eastus2`            | https://eastus2.cris.ai        |
| USA, centrala       | `centralus`          | https://centralus.cris.ai      |
| USA, norra centrala | `northcentralus`     | https://northcentralus.cris.ai |
| USA, södra centrala | `southcentralus`     | https://southcentralus.cris.ai |
| Indien, centrala    | `centralindia`       | https://centralindia.cris.ai   |
| Asien, östra        | `eastasia`           | https://eastasia.cris.ai       |
| Asien, sydöstra   | `southeastasia`      | https://southeastasia.cris.ai  |
| Japan, östra       | `japaneast`          | https://japaneast.cris.ai      |
| Sydkorea, centrala    | `koreacentral`       | https://koreacentral.cris.ai   |
| Australien, östra   | `australiaeast`      | https://australiaeast.cris.ai  |
| Kanada, centrala   | `canadacentral`      | https://canadacentral.cris.ai  |
| Europa, norra     | `northeurope`        | https://northeurope.cris.ai    |
| Europa, västra      | `westeurope`         | https://westeurope.cris.ai     |
| Storbritannien, södra         | `uksouth`            | https://uksouth.cris.ai        |
| Frankrike, centrala   | `francecentral`      | https://francecentral.cris.ai  |

### <a name="intent-recognition"></a>Avsiktsigenkänning

Tillgängliga regioner för **avsikts igenkänning** via tal-SDK: n är följande:

| Global region | Region           | Parametern för tal-SDK |
| ------------- | ---------------- | -------------------- |
| Asien          | Asien, östra        | `eastasia`           |
| Asien          | Asien, sydöstra   | `southeastasia`      |
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

Det här är en delmängd av de publicerings regioner som stöds av [language Understandings tjänsten (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Röstassistenter

[Tal-SDK](speech-sdk.md) har stöd för **röst assistent** funktioner i följande regioner:

| Region         | Parametern för tal-SDK |
| -------------- | -------------------- |
| USA, västra        | `westus`             |
| USA, västra 2      | `westus2`            |
| USA, östra        | `eastus`             |
| USA, östra 2      | `eastus2`            |
| Europa, västra    | `westeurope`         |
| Europa, norra   | `northeurope`        |
| Asien, sydöstra | `southeastasia`      |

## <a name="rest-apis"></a>REST API:er

Speech-tjänsten exponerar även REST-slutpunkter för förfrågningar om tal till text och text till tal.

### <a name="speech-to-text"></a>Tal till text

För referens dokumentation för tal till text, se [tal-till-text-REST API](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Text till tal

Dokumentation om text till tal-referenser finns i [text till tal-REST API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
