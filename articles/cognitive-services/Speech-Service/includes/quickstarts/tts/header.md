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
ms.openlocfilehash: d19f779f67cc0dea8cc7f06aa275885d75c3092e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818125"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att konvertera text till syntetiskt tal. Efter att ha uppfyllt några krav, tar åter givningen av syntetiskt tal till standard högtalarna bara fyra steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Skapa ett ````SpeechSynthesizer````-objekt med hjälp av ````SpeechConfig````-objektet ovan.
> * Använd ````SpeechSynthesizer````-objektet för att tala texten.
> * Kontrol lera ````SpeechSynthesisResult```` returnerade för fel.
