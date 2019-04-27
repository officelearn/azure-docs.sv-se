---
title: Azure ytterdörren Service – stöd för HTTP2 | Microsoft Docs
description: Den här artikeln hjälper dig att lära dig om HTTP/2-stöd i Azure ytterdörren Service
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 33e738f31be493d5890fc972ec71e7c6cd733bf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736459"
---
# <a name="http2-support-in-azure-front-door-service"></a>Stöd för HTTP/2 i Azure ytterdörren Service
HTTP/2 är en huvudversion till HTTP/1.1. Det ger snabbare webbprestanda, minskade svarstid och förbättrad upplevelse samtidigt som den välbekanta HTTP-metoder och statuskoder semantik. Även om HTTP/2 är utformat för att arbeta med HTTP och HTTPS, har många klientens webbläsare stöder HTTP/2 endast över Transport Layer Security (TLS).

### <a name="http2-benefits"></a>HTTP/2-fördelar

Fördelarna med HTTP/2 är:

*   **Multiplexing och samtidighet**

    Med HTTP 1.1 kan flera begäranden för resursen kräver flera TCP-anslutningar och varje anslutning har prestanda försämras kopplade till den. HTTP/2 kan flera resurser som krävs för en enda TCP-anslutning.

*   **Komprimering**

    Genom att komprimera HTTP-huvuden för resurser som hanteras, minskar tiden på kabeln avsevärt.

*   **Stream-beroenden**

    Stream beroenden kan klienten för att ange att servern vilka resurser som har prioritet.


## <a name="http2-browser-support"></a>Stöd för HTTP/2-webbläsare

Alla större webbläsare har implementerat stöd för HTTP/2 i aktuell version. Stöds inte webbläsare används automatiskt HTTP/1.1.

|Webbläsare|Lägsta Version|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-front-door-service"></a>Aktivera stöd för HTTP/2 i Azure ytterdörren Service

För närvarande är stöd för HTTP/2 aktiv för alla konfigurationer med åtkomsten. Ingen ytterligare åtgärd krävs från kunder.

## <a name="next-steps"></a>Nästa steg

Mer information om HTTP/2 finns i följande resurser:

- [Startsida för HTTP/2-specifikation](https://http2.github.io/)
- [Officiella HTTP/2 vanliga frågor och svar](https://http2.github.io/faq/)
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
