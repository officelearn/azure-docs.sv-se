---
title: 'Snabb start: syntetisera tal-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att syntetisera tal med hjälp av tal-SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503082"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text till syntetiskt tal. Efter att ha uppfyllt några krav, tar åter givningen av syntetiskt tal till standard högtalarna bara fyra steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ````SpeechSynthesizer````-objekt med hjälp av ````SpeechConfig````-objektet ovan.
> * Använd ````SpeechSynthesizer````-objektet för att tala texten.
> * Kontrol lera ````SpeechSynthesisResult```` returnerade för fel.
