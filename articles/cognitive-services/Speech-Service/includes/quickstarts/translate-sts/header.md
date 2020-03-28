---
title: 'Snabbstart: Översätt tal-till-tal - Taltjänst'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74981380"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt översätta tal från ett språk till tal på ett annat språk. Efter att ha uppfyllt några förutsättningar tar översättningen av tal till tal bara sex steg:
> [!div class="checklist"]
> * Skapa ````SpeechTranslationConfig```` ett objekt från din prenumerationsnyckel och region.
> * Uppdatera ````SpeechTranslationConfig```` objektet för att ange käll- och målspråk.
> * Uppdatera ````SpeechTranslationConfig```` objektet för att ange röstnamnet för talutdata.
> * Skapa ````TranslationRecognizer```` ett objekt ````SpeechTranslationConfig```` med objektet ovanifrån.
> * Starta ````TranslationRecognizer```` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera ````TranslationRecognitionResult```` de returnerade.
