---
title: Tal service regioner | Microsoft Docs
description: Referens för regioner för tjänsten tal.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1eb3768f5a5c5a27a45dde3f62f862f36fa3e8ac
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098506"
---
# <a name="regions-of-the-speech-service"></a>Områden av tjänsten tal

Dikterings-tjänsten är tillgänglig i olika regioner.
När du skapar en prenumeration kan du välja en tillgänglig region, beroende på dina behov.

Du måste kontot för den region som du valde när du använder din prenumeration.

## <a name="rest-api"></a>REST-API

Med hjälp av REST-API, Välj rätt regionspecifika slutpunkter.
Se [REST API: er](rest-apis.md) mer information.



## <a name="speech-sdk"></a>Speech SDK

I den [tal SDK](speech-sdk.md), regioner har angetts som en sträng (till exempel som en parameter för [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) i tal SDK för C#).

I tabellen nedan visas de tillgängliga regionerna för taligenkänning och översättning:

Region| Värdet för parametern region i tal SDK
-|-
Västra USA| `westus`
Östasien| `eastasia`
Norra Europa| `northeurope`

Tillgängliga regioner för avsiktshantering recognition via SDK tal visas i den [språk förstå region sida](/azure/cognitive-services/luis/luis-reference-regions).
För varje publishing region som listas, bestäms motsvarande tal SDK region parameter som den första delen av namnet på slutpunkten.
Till exempel använda `westus` som anger västra USA publishing området.
