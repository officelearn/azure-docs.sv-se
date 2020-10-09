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
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "78228083"
---
# <a name="speech-services-with-sovereign-clouds"></a>Tal tjänster med suveräna moln

## <a name="azure-government-united-states"></a>Azure Government (USA)

Endast amerikanska federala, statliga, lokala och stambaserad myndigheter och deras partner har till gång till den här dedikerade instansen med åtgärder som styrs av en kontrollerad USA-medborgare.
- Regioner: US Gov, Virginia
- SR i SpeechSDK:*config. FromHost ("WSS://Virginia.stt.Speech.Azure.us", " \<your-key\> ");*
- TTS i SpeechSDK: *config. FromHost ("https []() ://Virginia.TTS.Speech.Azure.us", " \<your-key\> ");*
- Autentiseringstoken: https []() ://Virginia.API.Cognitive.Microsoft.us/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.us  
- Custom Speech Portal: https://virginia.cris.azure.us/Home/CustomSpeech
- Tillgängliga SKU: er: S0
- Funktioner som stöds:
  - Tal till text
  - Custom Speech (akustisk/språk anpassning)
  - Text till tal
  - Tal översättare
- Funktioner som inte stöds
  - Anpassad röst
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
- SR i SpeechSDK: *config. FromHost ("WSS://chinaeast2.stt.Speech.Azure.cn", " \<your-key\> ");*
- TTS i SpeechSDK:  *config. FromHost ("https []() ://chinaeast2.TTS.Speech.Azure.cn", " \<your-key\> ");*
- Autentiseringstoken: https []() ://chinaeast2.API.Cognitive.Azure.cn/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- Custom Speech Portal: https://speech.azure.cn/CustomSpeech
- Tillgängliga SKU: er: S0
- Funktioner som stöds:
  - Tal till text
  - Custom Speech (akustisk/språk anpassning)
  - Text till tal
  - Tal översättare
- Funktioner som inte stöds
  - Anpassad röst
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

