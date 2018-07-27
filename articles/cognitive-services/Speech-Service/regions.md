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
ms.openlocfilehash: c026c636030b397da612b3c1409b80f0ff970f57
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284752"
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

Tabellen nedan visar en lista över tillgängliga regioner för **taligenkänning** och **translation**:

Region| Värde för parametern region i tal-SDK
-|-
Västra USA| `westus`
Östasien| `eastasia`
Norra Europa| `northeurope`

Tillgängliga regioner för **avsiktsigenkänning** via tal SDK finns i den [Language Understanding Tjänstesida region](/azure/cognitive-services/luis/luis-reference-regions).
För varje publishing-region som visas, bestäms parametern motsvarande tal SDK region som den första delen av domännamnet för slutpunkten.
Till exempel använda `westus` Ange regionen West US publicering.
