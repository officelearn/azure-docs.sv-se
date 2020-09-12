---
title: Azure frontend-HTTP2-support | Microsoft Docs
description: Den här artikeln hjälper dig att lära dig mer om HTTP/2-stöd i Azures front dörr
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 7cdcef80ef2433d34b32589d2539089fb9801eb1
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399437"
---
# <a name="http2-support-in-azure-front-door"></a>Stöd för HTTP/2 i Azures front dörr

HTTP/2-stöd är för närvarande aktivt för alla konfigurationer för Azures frontend-dörr. Ingen ytterligare åtgärd krävs från kunderna.

HTTP/2 är en större revidering av HTTP/1.1. Det ger snabbare webb prestanda, minskad svars tid och bättre användar upplevelse, samtidigt som de välkända HTTP-metoderna, status koderna och semantiken bibehålls. Även om HTTP/2 är utformad för att fungera med HTTP och HTTPS, stöder många klient webbläsare bara HTTP/2 över Transport Layer Security (TLS).

> [!NOTE]
> Stöd för HTTP/2-protokoll är bara tillgängligt för begär Anden från klienter till front dörren. Kommunikationen från Front dörren till Server delen i backend-poolen sker över HTTP/1.1. 

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

|Webbläsare|Lägsta version|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Efterföljande moment

Om du vill veta mer om HTTP/2 kan du besöka följande resurser:

- [Start sida för HTTP/2-specifikation](https://http2.github.io/)
- [Vanliga frågor och svar om HTTP/2](https://http2.github.io/faq/)
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
