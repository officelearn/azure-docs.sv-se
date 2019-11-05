---
title: 'Snabb start: Översätt tal-till-text-tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: c45e75038d1731c933ccf8bf26f9de573e409292
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502788"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt översätta tal från ett språk till text på ett annat språk. När ett fåtal krav har uppfyllts tar översättning av tal till text bara fem steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig````-objekt från din prenumerations nyckel och region.
> * Uppdatera ````SpeechConfig````-objektet och ange käll-och mål språk.
> * Skapa ett ````TranslationRecognizer````-objekt med hjälp av ````SpeechConfig````-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````TranslationRecognizer````-objektet.
> * Granska ````TranslationRecognitionResult```` som returneras.
