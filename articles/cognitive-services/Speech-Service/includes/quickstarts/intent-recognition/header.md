---
title: 'Snabb start: identifiera tal, avsikter och entiteter – tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660559"
---
I den här snabb starten använder du tjänsten [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) och language Understanding (Luis) för att identifiera avsikter från ljuddata som fångats från en mikrofon. Mer specifikt använder du talet SDK för att avbilda tal och en fördefinierad domän från LUIS för att identifiera avsikter för start automatisering, som att aktivera och inaktivera ett ljus. 

Efter att ha uppfyllt några krav, tar det bara några steg att känna igen tal och identifiera syften från en mikrofon:

> [!div class="checklist"]
>
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ````IntentRecognizer````-objekt med hjälp av ````SpeechConfig````-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````IntentRecognizer````-objektet.
> * Granska ````IntentRecognitionResult```` som returneras.
