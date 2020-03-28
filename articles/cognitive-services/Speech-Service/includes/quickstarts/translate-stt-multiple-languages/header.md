---
title: 'Snabbstart: Översätt tal till flera språk - Taltjänst'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980920"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt översätta tal från ett språk till tal på ett annat språk. När du har uppfyllt några förutsättningar tar översättning av tal till text på flera språk bara sex steg:
> [!div class="checklist"]
> * Skapa ````SpeechTranslationConfig```` ett objekt från din prenumerationsnyckel och region.
> * Uppdatera ````SpeechTranslationConfig```` objektet för att ange källspråket för taligenkänning.
> * Uppdatera ````SpeechTranslationConfig```` objektet för att ange flera översättningsmålspråk.
> * Skapa ````TranslationRecognizer```` ett objekt ````SpeechTranslationConfig```` med objektet ovanifrån.
> * Starta ````TranslationRecognizer```` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera ````TranslationRecognitionResult```` de returnerade.
