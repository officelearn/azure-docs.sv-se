---
title: 'Snabb start: identifiera tal från en mikrofon-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467899"
---
I den här snabb starten ska du använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att identifiera tal från mikrofonens indata och få text avskriften från insamlade ljud. Det är enkelt att integrera den här funktionen i dina appar eller enheter för vanliga igenkännings aktiviteter, till exempel att skriva över konversationer. Den kan också användas för mer komplexa integreringar, t. ex. med hjälp av bot Framework med tal-SDK för att bygga röst assistenter.

Efter att ha uppfyllt några krav, tar igenkänning av tal från en mikrofon bara fyra steg:

> [!div class="checklist"]
> * Skapa ett `SpeechConfig`-objekt från din prenumerations nyckel och region.
> * Skapa ett `SpeechRecognizer`-objekt med hjälp av `SpeechConfig`-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av `SpeechRecognizer`-objektet.
> * Granska `SpeechRecognitionResult` som returneras.
