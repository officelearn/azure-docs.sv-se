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
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: bee3b64ece3faac6258fe4d017f12833b12e370d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536357"
---
I den här snabb starten ska du använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att identifiera tal från mikrofonens indata och få text avskriften från insamlade ljud. Det är enkelt att integrera den här funktionen i dina appar eller enheter för vanliga igenkännings aktiviteter, till exempel att skriva över konversationer. Den kan också användas för mer komplexa integreringar, t. ex. med hjälp av bot Framework med tal-SDK för att bygga röst assistenter.

Efter att ha uppfyllt några krav, tar igenkänning av tal från en mikrofon bara fyra steg:

> [!div class="checklist"]
> * Skapa ett `SpeechConfig`-objekt från din prenumerations nyckel och region.
> * Skapa ett `SpeechRecognizer`-objekt med hjälp av `SpeechConfig`-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av `SpeechRecognizer`-objektet.
> * Granska `SpeechRecognitionResult` som returneras.
