---
title: 'Snabb start: syntetisera tal i ljud filen – tal tjänst'
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
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502949"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text till syntetiskt tal i en ljudfil. Efter att ha uppfyllt några krav, tar det bara att syntetisera tal i en fil fem steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ljud konfigurations objekt som anger. WAV-filnamn.
> * Skapa ett ````SpeechSynthesizer````-objekt med hjälp av konfigurations objekt från ovan.
> * Använd ````SpeechSynthesizer````-objektet, konvertera texten till syntetiskt tal och spara den i den angivna ljud filen.
> * Granska ````SpeechSynthesizer```` som returnerats för fel.
