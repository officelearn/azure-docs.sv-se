---
title: 'Snabb start: identifiera tal från en ljud fil – tal tjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469257"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att identifiera tal från en ljudfil. Efter att ha uppfyllt några krav, tar igenkänning av tal från en fil bara fem steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ````AudioConfig````-objekt som anger. WAV-filnamn.
> * Skapa ett ````SpeechRecognizer````-objekt med hjälp av ````SpeechConfig```` och ````AudioConfig```` objekt ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````SpeechRecognizer````-objektet.
> * Granska ````SpeechRecognitionResult```` som returneras.
