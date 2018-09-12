---
title: Tal-regionerna
description: Referens för regioner Speech-tjänsten.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379578"
---
# <a name="regions-of-the-speech-service"></a>Med taltjänsten-regioner

Speech-tjänsten finns i olika regioner.
När du skapar en prenumeration kan du välja en tillgänglig region, beroende på dina behov.

När du använder din prenumeration som du behöver för den region som du valt.

## <a name="rest-api"></a>REST-API

Med hjälp av REST-API, Välj rätt regionspecifika-slutpunkter.
Se [REST API: er](rest-apis.md) mer information.

## <a name="speech-sdk"></a>Speech SDK

I den [tal SDK](speech-sdk.md), regioner har angetts som en sträng (till exempel som en parameter till [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) i tal SDK för C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Regioner för taligenkänning och översättning

Tabellen nedan visar en lista över tillgängliga regioner för **taligenkänning** och **translation**:

Region| Värde för parametern region i tal-SDK| Portal
-|-
USA, västra| `westus`| https://westus.cris.ai
USA, västra 2| `westus2`| https://westus2.cris.ai
USA, östra| `eastus`| https://eastus.cris.ai
USA, östra 2| `eastus2`| https://eastus2.cris.ai
Asien, östra| `eastasia`| https://eastasia.cris.ai
Sydostasien| `southeastasia`| https://southeastasia.cris.ai
Europa, norra| `northeurope`| https://northeurope.cris.ai
Europa, västra|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>Regioner för taligenkänning

Tillgängliga regioner för **avsiktsigenkänning** via tal SDK finns i den [Language Understanding Tjänstesida region](/azure/cognitive-services/luis/luis-reference-regions).
För varje publishing-region som visas, bestäms parametern motsvarande tal SDK region som den första delen av domännamnet för slutpunkten.
Till exempel använda `westus` Ange regionen West US publicering.
