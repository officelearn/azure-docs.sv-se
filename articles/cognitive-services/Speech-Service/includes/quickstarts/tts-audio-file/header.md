---
title: 'Snabb start: syntetisera tal i ljud filen – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469798"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text till syntetiskt tal i en ljudfil. Efter att ha uppfyllt några krav, tar det bara att syntetisera tal i en fil fem steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ljud konfigurations objekt som anger. WAV-filnamn.
> * Skapa ett ````SpeechSynthesizer````-objekt med hjälp av konfigurations objekt från ovan.
> * Använd ````SpeechSynthesizer````-objektet, konvertera texten till syntetiskt tal och spara den i den angivna ljud filen.
> * Granska ````SpeechSynthesizer```` som returnerats för fel.
