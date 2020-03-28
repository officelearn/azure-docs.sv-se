---
title: 'Snabbstart: Översätt tal-till-text - Taltjänst'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74981210"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt översätta tal från ett språk till text på ett annat språk. När du har uppfyllt några förutsättningar tar översättningen av tal till text bara fem steg:
> [!div class="checklist"]
> * Skapa ````SpeechConfig```` ett objekt från din prenumerationsnyckel och region.
> * Uppdatera ````SpeechConfig```` objektet för att ange käll- och målspråk.
> * Skapa ````TranslationRecognizer```` ett objekt ````SpeechConfig```` med objektet ovanifrån.
> * Starta ````TranslationRecognizer```` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera ````TranslationRecognitionResult```` de returnerade.
