---
title: 'Snabb start: syntetisera tal i ljud filen – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817795"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text till syntetiskt tal i en ljudfil. Efter att ha uppfyllt några krav, tar det bara att syntetisera tal i en fil fem steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ljud konfigurations objekt som anger. WAV-filnamn.
> * Skapa ett ````SpeechSynthesizer````-objekt med hjälp av konfigurations objekt från ovan.
> * Använd ````SpeechSynthesizer````-objektet, konvertera texten till syntetiskt tal och spara den i den angivna ljud filen.
> * Granska ````SpeechSynthesizer```` som returnerats för fel.
