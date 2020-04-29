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
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981210"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt översätta tal från ett språk till text på ett annat språk. När ett fåtal krav har uppfyllts tar översättning av tal till text bara fem steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechConfig```` objekt från din prenumerations nyckel och region.
> * Uppdatera ````SpeechConfig```` objektet för att ange käll-och mål språk.
> * Skapa ett ````TranslationRecognizer```` objekt med hjälp ````SpeechConfig```` av objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````TranslationRecognizer```` objektet.
> * Granska den ````TranslationRecognitionResult```` returnerade.
