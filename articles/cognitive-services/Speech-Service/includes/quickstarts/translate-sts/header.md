---
title: 'Snabb start: Översätt tal till tal-tal-tjänsten'
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
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981380"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt översätta tal från ett språk till tal på ett annat språk. Efter att ha uppfyllt några krav, tar det bara att översätta tal till tal sex steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechTranslationConfig```` objekt från din prenumerations nyckel och region.
> * Uppdatera ````SpeechTranslationConfig```` objektet för att ange käll-och mål språk.
> * Uppdatera ````SpeechTranslationConfig```` objektet för att ange röst namnet för tal utdatafilen.
> * Skapa ett ````TranslationRecognizer```` objekt med hjälp ````SpeechTranslationConfig```` av objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````TranslationRecognizer```` objektet.
> * Granska den ````TranslationRecognitionResult```` returnerade.
