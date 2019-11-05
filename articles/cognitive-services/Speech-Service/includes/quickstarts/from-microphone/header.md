---
title: 'Snabb start: identifiera tal från en mikrofon-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 64f084f58ae4b12d92d6dee343a59dd56b1c351e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503509"
---
I den här snabb starten kommer du att använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt identifiera tal från ljuddata som fångats från en mikrofon. Efter att ha uppfyllt några krav, tar igenkänning av tal från en mikrofon bara fyra steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ````SpeechRecognizer````-objekt med hjälp av ````SpeechConfig````-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````SpeechRecognizer````-objektet.
> * Granska ````SpeechRecognitionResult```` som returneras.
