---
title: 'Snabb start: skapa en anpassad röst assistent – tal tjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241145"
---
I den här snabb starten ska du använda [tal-SDK: n](~/articles/cognitive-services/speech-service/speech-sdk.md) för att skapa ett anpassat program för röst assistent som ansluter till en robot som du redan har skapat och konfigurerat. Om du behöver skapa en bot, se [den relaterade självstudien](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) för en mer omfattande guide.

När du uppfyller några krav, tar det bara några steg att ansluta till den anpassade röst assistenten:
> [!div class="checklist"]
> * Skapa ett `BotFrameworkConfig` objekt från din prenumerations nyckel och region.
> * Skapa ett `DialogServiceConnector` objekt med hjälp `BotFrameworkConfig` av objektet ovan.
> * Starta lyssnings processen för en enda uttryck med hjälp av `DialogServiceConnector` objektet.
> * Granska den `ActivityReceivedEventArgs` returnerade.
