---
title: Suveräna moln – tal service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder suveräna moln
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: 3b96ded52aa2148df9e96d6f1e878d50b821abab
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170472"
---
# <a name="speech-services-with-sovereign-clouds"></a>Tal tjänster med suveräna moln

## <a name="azure-government-united-states"></a>Azure Government (USA)

Endast federala, delstatliga, lokala och stambaserade amerikanska myndigheter har tillgång till den här dedikerade instansen som hanteras av specialgranskade amerikanska medborgare.
- Regioner: US Gov, Virginia
- SR i SpeechSDK:*config. FromHost ("WSS://Virginia.stt.Speech.Azure.us", "\<din nyckel\>");*
- TTS i SpeechSDK: *config. FromHost ("https[]()://Virginia.TTS.Speech.Azure.us", "\<din nyckel\>");*
- Autentiseringstoken: https[]()://Virginia.API.Cognitive.Microsoft.us/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.us  
- Custom Speech Portal: https://virginia.cris.azure.us/Home/CustomSpeech
- Tillgängliga SKU: er: S0
- Funktioner som stöds:
  - Tal till text
  - Custom Speech (akustisk/språk anpassning)
  - Text till tal
  - Tal översättare
- Funktioner som inte stöds
  - Custom Voice
  - Neurala röster för text till tal
- Språk som stöds: språk för följande språk stöds.
  - Arabiska (ar-*)
  - Kinesiska (zh-*)
  - Engelska (en-*)
  - Franska (FR-*)
  - Tyska (de-*)
  - Hindi
  - Koreanska
  - Ryska
  - Spanska (ES-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure Kina

I Kina är ett Azure-datacenter med direkt åtkomst till Kina, mobil, telekom, Kina, Unicom och andra större stamnät nätverk, för att kinesiska användare ska kunna tillhandahålla snabb och stabil lokal nätverks åtkomst.
- Regioner: Kina, östra 2 (Shanghai)
- SR i SpeechSDK: *config. FromHost ("WSS://chinaeast2.stt.Speech.Azure.cn", "\<din nyckel\>");*
- TTS i SpeechSDK: *config. FromHost ("https[]()://chinaeast2.TTS.Speech.Azure.cn", "\<din nyckel\>");*
- Autentiseringstoken: https[]()://chinaeast2.API.Cognitive.Microsoft.cn/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- Custom Speech Portal: https://chinaeast2.cris.azure.cn/Home/CustomSpeech
- Tillgängliga SKU: er: S0
- Funktioner som stöds:
  - Tal till text
  - Custom Speech (akustisk/språk anpassning)
  - Text till tal
  - Tal översättare
- Funktioner som inte stöds
  - Custom Voice
  - Neurala röster för text till tal
- Språk som stöds: språk för följande språk stöds.
  - Arabiska (ar-*)
  - Kinesiska (zh-*)
  - Engelska (en-*)
  - Franska (FR-*)
  - Tyska (de-*)
  - Hindi
  - Koreanska
  - Ryska
  - Spanska (ES-*)

