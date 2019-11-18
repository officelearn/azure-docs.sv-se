---
title: 'Snabb start: identifiera tal, avsikter och entiteter – tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: df4604560e05899461b11ec0788f72b27241f1b9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125619"
---
I den här snabb starten kommer du att använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt identifiera tal från ljuddata som fångats från en mikrofon. Efter att ha uppfyllt några krav, tar igenkänning av tal från en mikrofon bara fyra steg:
> [!div class="checklist"]
>
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ````IntentRecognizer````-objekt med hjälp av ````SpeechConfig````-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````IntentRecognizer````-objektet.
> * Granska ````IntentRecognitionResult```` som returneras.
