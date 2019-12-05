---
title: 'Snabb start: Översätt tal till flera språk – tal tjänst'
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
ms.openlocfilehash: bc35c1efcad2ca9ebb5eaf23dd84f7189858a159
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817195"
---
I den här snabb starten ska du använda [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt översätta tal från ett språk till tal på ett annat språk. Efter att ha uppfyllt några krav, tar det bara sex steg att översätta tal till text på flera språk:
> [!div class="checklist"]
> * Skapa ett ````SpeechTranslationConfig````-objekt från din prenumerations nyckel och region.
> * Uppdatera ````SpeechTranslationConfig````-objektet och ange käll språket för tal igenkänning.
> * Uppdatera ````SpeechTranslationConfig````-objektet och ange flera översättnings mål språk.
> * Skapa ett ````TranslationRecognizer````-objekt med hjälp av ````SpeechTranslationConfig````-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av ````TranslationRecognizer````-objektet.
> * Granska ````TranslationRecognitionResult```` som returneras.
