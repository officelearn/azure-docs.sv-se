---
title: 'Snabb start: Översätt tal till flera språk – tal tjänst'
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
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980920"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt översätta tal från ett språk till tal på ett annat språk. Efter att ha uppfyllt några krav, tar det bara sex steg att översätta tal till text på flera språk:
> [!div class="checklist"]
> * Skapa ett ````SpeechTranslationConfig```` objekt från din prenumerations nyckel och region.
> * Uppdatera ````SpeechTranslationConfig```` objektet för att ange käll språket för tal igenkänning.
> * Uppdatera ````SpeechTranslationConfig```` objektet för att ange flera översättnings mål språk.
> * Skapa ett ````TranslationRecognizer```` objekt med hjälp ````SpeechTranslationConfig```` av objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````TranslationRecognizer```` objektet.
> * Granska den ````TranslationRecognitionResult```` returnerade.
