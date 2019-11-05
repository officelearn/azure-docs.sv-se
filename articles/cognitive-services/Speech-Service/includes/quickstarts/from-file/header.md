---
title: 'Snabb start: identifiera tal från en ljud fil – tal tjänst'
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
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504139"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att identifiera tal från en ljudfil. Efter att ha uppfyllt några krav, tar igenkänning av tal från en fil bara fem steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ````AudioConfig````-objekt som anger. WAV-filnamn.
> * Skapa ett ````SpeechRecognizer````-objekt med hjälp av ````SpeechConfig```` och ````AudioConfig```` objekt ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````SpeechRecognizer````-objektet.
> * Granska ````SpeechRecognitionResult```` som returneras.
