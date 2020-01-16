---
title: 'Snabb start: identifiera tal från en ljud fil – tal tjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037912"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att identifiera tal från en ljudfil. Efter att ha uppfyllt några krav, tar det bara några steg att känna igen tal från en fil:
> [!div class="checklist"]
> * Skapa ett `SpeechConfig`-objekt från din prenumerations nyckel och region.
> * Skapa ett `AudioConfig`-objekt som anger. WAV-filnamn.
> * Skapa ett `SpeechRecognizer`-objekt med hjälp av `SpeechConfig` och `AudioConfig` objekt ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av `SpeechRecognizer`-objektet.
> * Granska `SpeechRecognitionResult` som returneras.
