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
ms.openlocfilehash: 478a62eaddcf3b9b831812a0930ff10c1adce99d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072445"
---
# <a name="speech-service-supported-regions"></a>Med Taltjänsten regioner som stöds

Med taltjänsten kan ditt program kan omvandla ljud till text, utför talöversättning och konvertera text till tal. Tjänsten är tillgänglig i flera regioner med unika slutpunkter för taligenkänning SDK och REST API: er.

Se till att du använder den slutpunkt som matchar regionen för din prenumeration.

## <a name="speech-sdk"></a>Speech SDK

I [talet SDK](speech-sdk.md)anges regioner som en sträng (till exempel som en parameter för att `SpeechConfig.FromSubscription` i tal-SDK för C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Tal till text, text till tal och översättning

Talet SDK är tillgängligt i dessa regioner för **tal igenkänning**, **text till tal**och **översättning**:

| Region           | Parametern för tal-SDK | Tal anpassning av portalen    |
| ---------------- | -------------------- | ------------------------------ |
| Västra USA          | `westus`             | https://westus.cris.ai         |
| Västra USA 2        | `westus2`            | https://westus2.cris.ai        |
| Östra USA          | `eastus`             | https://eastus.cris.ai         |
| USA, östra 2        | `eastus2`            | https://eastus2.cris.ai        |
| Centrala USA       | `centralus`          | https://centralus.cris.ai      |
| Norra centrala USA | `northcentralus`     | https://northcentralus.cris.ai |
| Södra centrala USA | `southcentralus`     | https://southcentralus.cris.ai |
| Indien, centrala    | `centralindia`       | https://centralindia.cris.ai   |
| Östasien        | `eastasia`           | https://eastasia.cris.ai       |
| Sydostasien   | `southeastasia`      | https://southeastasia.cris.ai  |
| Östra Japan       | `japaneast`          | https://japaneast.cris.ai      |
| Sydkorea, centrala    | `koreacentral`       | https://koreacentral.cris.ai   |
| Östra Australien   | `australiaeast`      | https://australiaeast.cris.ai  |
| Centrala Kanada   | `canadacentral`      | https://canadacentral.cris.ai  |
| Norra Europa     | `northeurope`        | https://northeurope.cris.ai    |
| Västra Europa      | `westeurope`         | https://westeurope.cris.ai     |
| Storbritannien, södra         | `uksouth`            | https://uksouth.cris.ai        |
| Frankrike, centrala   | `francecentral`      | https://francecentral.cris.ai  |

### <a name="intent-recognition"></a>Avsiktsigenkänning

Tillgängliga regioner för **avsikts igenkänning** via tal-SDK: n är följande:

| Global region | Region           | Parametern för tal-SDK |
| ------------- | ---------------- | -------------------- |
| Asien          | Östasien        | `eastasia`           |
| Asien          | Sydostasien   | `southeastasia`      |
| Australien     | Östra Australien   | `australiaeast`      |
| Europa        | Norra Europa     | `northeurope`        |
| Europa        | Västra Europa      | `westeurope`         |
| Nordamerika | Östra USA          | `eastus`             |
| Nordamerika | USA, östra 2        | `eastus2`            |
| Nordamerika | Södra centrala USA | `southcentralus`     |
| Nordamerika | Västra centrala USA  | `westcentralus`      |
| Nordamerika | Västra USA          | `westus`             |
| Nordamerika | Västra USA 2        | `westus2`            |
| Sydamerika | Södra Brasilien     | `brazilsouth`        |

Det här är en delmängd av de publicerings regioner som stöds av [language Understandings tjänsten (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Röstassistenter

[Tal-SDK](speech-sdk.md) har stöd för **röst assistent** funktioner i följande regioner:

| Region         | Parametern för tal-SDK |
| -------------- | -------------------- |
| Västra USA        | `westus`             |
| Västra USA 2      | `westus2`            |
| Östra USA        | `eastus`             |
| USA, östra 2      | `eastus2`            |
| Västra Europa    | `westeurope`         |
| Norra Europa   | `northeurope`        |
| Sydostasien | `southeastasia`      |

## <a name="rest-apis"></a>REST API:er

Speech-tjänsten exponerar även REST-slutpunkter för förfrågningar om tal till text och text till tal.

### <a name="speech-to-text"></a>Tal till text

För referens dokumentation för tal till text, se [tal-till-text-REST API](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Text till tal

Dokumentation om text till tal-referenser finns i [text till tal-REST API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
