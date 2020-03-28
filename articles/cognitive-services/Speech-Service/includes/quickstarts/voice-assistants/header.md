---
title: 'Snabbstart: Skapa en anpassad röstassistent - Taltjänst'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241145"
---
I den här snabbstarten använder du [Tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för att skapa ett anpassat röstassistentprogram som ansluter till en robot som du redan har skapat och konfigurerat. Om du behöver skapa en bot, se [den relaterade handledningen](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) för en mer omfattande guide.

När du har uppfyllt några förutsättningar tar du bara några steg att ansluta din anpassade röstassistent:
> [!div class="checklist"]
> * Skapa `BotFrameworkConfig` ett objekt från din prenumerationsnyckel och region.
> * Skapa `DialogServiceConnector` ett objekt `BotFrameworkConfig` med objektet ovanifrån.
> * Starta `DialogServiceConnector` lyssningsprocessen för ett enda uttryck med hjälp av objektet.
> * Inspektera `ActivityReceivedEventArgs` de returnerade.
