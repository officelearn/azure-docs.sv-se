---
title: 'Snabb start: Översätt tal till tal-tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 805193c08e57ddc18dfca0a78c6b58cf895baec1
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817596"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt översätta tal från ett språk till tal på ett annat språk. Efter att ha uppfyllt några krav, tar det bara att översätta tal till tal sex steg:
> [!div class="checklist"]
> * Skapa ett ````SpeechTranslationConfig````-objekt från din prenumerations nyckel och region.
> * Uppdatera ````SpeechTranslationConfig````-objektet och ange käll-och mål språk.
> * Uppdatera ````SpeechTranslationConfig````-objektet och ange röst namnet för tal utdatafilen.
> * Skapa ett ````TranslationRecognizer````-objekt med hjälp av ````SpeechTranslationConfig````-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````TranslationRecognizer````-objektet.
> * Granska ````TranslationRecognitionResult```` som returneras.
