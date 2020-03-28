---
title: 'Snabbstart: Känna igen tal från en ljudfil - Taltjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037912"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att känna igen tal från en ljudfil. Efter att ha uppfyllt några förutsättningar, erkänner tal från en fil tar bara några steg:
> [!div class="checklist"]
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `AudioConfig` ett objekt som anger . WAV-filnamn.
> * Skapa `SpeechRecognizer` ett objekt `SpeechConfig` `AudioConfig` med hjälp av objekten och ovanifrån.
> * Starta `SpeechRecognizer` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera `SpeechRecognitionResult` de returnerade.
