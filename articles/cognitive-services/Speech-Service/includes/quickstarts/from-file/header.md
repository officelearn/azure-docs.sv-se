---
title: 'Snabb start: identifiera tal från en ljud fil – tal tjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400538"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att identifiera tal från en ljudfil. Efter att ha uppfyllt några krav, tar det bara några steg att känna igen tal från en fil:
> [!div class="checklist"]
> * Skapa ett `SpeechConfig` objekt från din prenumerations nyckel och region.
> * Skapa ett `AudioConfig` objekt som anger. WAV-filnamn.
> * Skapa ett `SpeechRecognizer` objekt med hjälp `SpeechConfig` av `AudioConfig` objekten och.
> * Starta igenkännings processen för en enda uttryck med hjälp av `SpeechRecognizer` objektet.
> * Granska den `SpeechRecognitionResult` returnerade.
