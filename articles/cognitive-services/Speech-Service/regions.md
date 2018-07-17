---
title: Tal-regionerna | Microsoft Docs
description: Referens för regioner Speech-tjänsten.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 11360d163fdba057d373d091d46903cde7789a8b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071427"
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
