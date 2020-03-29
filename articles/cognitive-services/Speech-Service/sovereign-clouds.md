---
title: Suveräna moln - Taltjänst
titleSuffix: Azure Cognitive Services
description: Läs om hur du använder suveräna moln
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228083"
---
# <a name="speech-services-with-sovereign-clouds"></a>Taltjänster med suveräna moln

## <a name="azure-government-united-states"></a>Azure-regeringen (USA)

Endast amerikanska federala, statliga, lokala och stamregeringar och deras partner har tillgång till denna dedikerade instans med operationer som kontrolleras av kontrollerade amerikanska medborgare.
- Regioner: US Gov Virginia
- SR i SpeechSDK:*config. FromHost("wss://virginia.stt.speech.azure.us",\<" your-key\>");*
- TTS i SpeechSDK: *config. FromHost("https://virginia.tts.speech.azure.us",[]()"\<your-key\>");*
- Autentiseringstoken: https[]()://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Azure-portal:https://portal.azure.us  
- Anpassad talportal:https://virginia.cris.azure.us/Home/CustomSpeech
- Tillgängliga SKU:er: S0
- Funktioner som stöds:
  - Tal till text
  - Anpassat tal (Akustisk/språkanpassning)
  - Text till tal
  - Talöversättare
- Funktioner som inte stöds
  - Anpassad röst
  - Neurala röster för Text-till-tal
- Språk som stöds: Språk för följande språk stöds.
  - Arabiska (ar-*)
  - Kinesiska (zh-*)
  - Engelska (en-*)
  - Franska (fr-*)
  - Tyska (de-*)
  - Hindi
  - Koreansk
  - Ryska
  - Spanska (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure Kina

Finns i Kina, ett Azure-datacenter med direkt åtkomst till China Mobile, China Telecom, China Unicom och andra stora stamnät för operatörer, för kinesiska användare att tillhandahålla snabb och stabil lokal nätverksåtkomstupplevelse.
- Regioner: China East 2 (Shanghai)
- SR i SpeechSDK: *config. FromHost("wss://chinaeast2.stt.speech.azure.cn",\<" your-key\>");*
- TTS i SpeechSDK: *config. FromHost("https[]()://chinaeast2.tts.speech.azure.cn", "\<your-key\>");*
- Autentiseringstoken: https[]()://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Azure-portal:https://portal.azure.cn
- Anpassad talportal:https://speech.azure.cn/CustomSpeech
- Tillgängliga SKU:er: S0
- Funktioner som stöds:
  - Tal till text
  - Anpassat tal (Akustisk/språkanpassning)
  - Text till tal
  - Talöversättare
- Funktioner som inte stöds
  - Anpassad röst
  - Neurala röster för Text-till-tal
- Språk som stöds: Språk för följande språk stöds.
  - Arabiska (ar-*)
  - Kinesiska (zh-*)
  - Engelska (en-*)
  - Franska (fr-*)
  - Tyska (de-*)
  - Hindi
  - Koreansk
  - Ryska
  - Spanska (es-*)

