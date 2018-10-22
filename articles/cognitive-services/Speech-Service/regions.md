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
ms.openlocfilehash: a5fce6f9547a96da3ce482ce388e5ba2093f2af4
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468134"
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

  Region | Parametern för tal-SDK | Portalen
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
