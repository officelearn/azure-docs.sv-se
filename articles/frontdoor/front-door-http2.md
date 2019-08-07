---
title: Azure frontend-tjänst – HTTP2-stöd | Microsoft Docs
description: Den här artikeln hjälper dig att lära dig mer om HTTP/2-stöd i Azure-tjänsten för front dörr
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
ms.openlocfilehash: c3c1721454c0b3c96071c685a764f34d4fa540b9
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775259"
---
# <a name="http2-support-in-azure-front-door-service"></a>HTTP/2-stöd i Azure-tjänsten för front dörr

HTTP/2-stöd är för närvarande aktivt för alla konfigurationer för front dörren. Ingen ytterligare åtgärd krävs från kunderna.

HTTP/2 är en större revidering av HTTP/1.1. Det ger snabbare webb prestanda, minskad svars tid och bättre användar upplevelse, samtidigt som de välkända HTTP-metoderna, status koderna och semantiken bibehålls. Även om HTTP/2 är utformad för att fungera med HTTP och HTTPS, stöder många klient webbläsare bara HTTP/2 över Transport Layer Security (TLS).

### <a name="http2-benefits"></a>HTTP/2-fördelar

Fördelarna med HTTP/2 är:

*   **Multiplexering och samtidighet**

    Med hjälp av HTTP 1,1 krävs flera TCP-anslutningar för att göra flera resurs begär Anden, och varje anslutning har en associerad prestanda. HTTP/2 gör att flera resurser kan begäras på en enda TCP-anslutning.

*   **Sidhuvud komprimering**

    Genom att komprimera HTTP-huvudena för att hantera resurser, minskas tiden på kabeln avsevärt.

*   **Strömma beroenden**

    Stream-beroenden gör att klienten kan ange för servern vilka resurser har prioritet.


## <a name="http2-browser-support"></a>Stöd för HTTP/2-webbläsare

Alla större webbläsare har implementerat HTTP/2-stöd i sina aktuella versioner. Webbläsare som inte stöds återgår automatiskt till HTTP/1.1.

|Browser|Lägsta version|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om HTTP/2 kan du besöka följande resurser:

- [Start sida för HTTP/2-specifikation](https://http2.github.io/)
- [Vanliga frågor och svar om HTTP/2](https://http2.github.io/faq/)
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
