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
ms.openlocfilehash: 088e581da7511797a0f39959d867c6298262462a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242338"
---
# <a name="regions-of-the-speech-service"></a>Med Taltjänsten-regioner

Speech-tjänsten finns i olika regioner.
När du skapar en prenumeration väljer du en tillgänglig region utifrån dina behov.

När du använder din prenumeration kan behöva du för den region som du har valt.

## <a name="rest-api"></a>REST-API

Använd REST API för att välja rätt regionspecifika slutpunkterna.
Se [REST API: er](rest-apis.md) mer information.

## <a name="speech-sdk"></a>Speech SDK

I den [tal-tjänst-SDK](speech-sdk.md), regioner har angetts som en sträng (till exempel som en parameter till `SpeechConfig.FromSubscription` i tal SDK för C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Regioner för taligenkänning och översättning

I följande tabell visas tillgängliga regioner för **taligenkänning** och **translation**.

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


### <a name="regions-for-intent-recognition"></a>Regioner för taligenkänning

Tillgängliga regioner för **avsiktsigenkänning** via tal SDK listas på den [Language Understanding Tjänstesida region](/azure/cognitive-services/luis/luis-reference-regions).
För varje publishing-region som visas, bestäms parametern motsvarande tal SDK region som den första delen av domännamnet för slutpunkten.
Till exempel använda `westus` Ange regionen West US publicering.
