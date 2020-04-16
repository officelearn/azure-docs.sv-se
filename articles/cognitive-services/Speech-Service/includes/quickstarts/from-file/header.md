---
title: 'Snabbstart: Känna igen tal från en ljudfil - Taltjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400538"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att känna igen tal från en ljudfil. Efter att ha uppfyllt några förutsättningar, erkänner tal från en fil tar bara några steg:
> [!div class="checklist"]
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `AudioConfig` ett objekt som anger . WAV-filnamn.
> * Skapa `SpeechRecognizer` ett objekt `SpeechConfig` `AudioConfig` med hjälp av objekten och ovanifrån.
> * Starta `SpeechRecognizer` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera `SpeechRecognitionResult` de returnerade.
