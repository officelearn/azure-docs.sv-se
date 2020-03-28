---
title: 'Snabbstart: Känna igen tal från en mikrofon - Taltjänst'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75467899"
---
I den här snabbstarten ska du använda [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt känna igen tal från en mikrofoninmatning och hämta textavskrifteringen från det tagna ljudet. Det är enkelt att integrera den här funktionen i dina appar eller enheter för vanliga igenkänningsuppgifter, till exempel att överföra konversationer. Den kan också användas för mer komplexa integrationer, som att använda Bot Framework med Tal SDK för att bygga röstassistenter.

Efter att ha uppfyllt några förutsättningar, erkänner tal från en mikrofon tar bara fyra steg:

> [!div class="checklist"]
> * Skapa `SpeechConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `SpeechRecognizer` ett objekt `SpeechConfig` med objektet ovanifrån.
> * Starta `SpeechRecognizer` igenkänningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera `SpeechRecognitionResult` de returnerade.
