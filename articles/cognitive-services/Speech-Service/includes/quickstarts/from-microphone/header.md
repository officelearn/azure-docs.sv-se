---
title: 'Snabb start: identifiera tal från en mikrofon-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/07/2019
ms.author: erhopf
ms.openlocfilehash: f3f28cdd8c3c77a5cde2ead86c49b41d54bab5d7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961170"
---
I den här snabb starten kommer du att använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att interaktivt identifiera tal från ljuddata som fångats från en mikrofon. Efter att ha uppfyllt några krav, tar igenkänning av tal från en mikrofon bara fyra steg:

> [!div class="checklist"]
> * Skapa ett `SpeechConfig`-objekt från din prenumerations nyckel och region.
> * Skapa ett `SpeechRecognizer`-objekt med hjälp av `SpeechConfig`-objektet ovan.
> * Starta igenkännings processen för en enda uttryck med hjälp av `SpeechRecognizer`-objektet.
> * Granska `SpeechRecognitionResult` som returneras.
