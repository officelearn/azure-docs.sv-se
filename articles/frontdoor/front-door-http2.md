---
title: Azure Ytterdörr - HTTP2-stöd | Microsoft-dokument
description: Den här artikeln hjälper dig att lära dig mer om HTTP/2-stöd i Azure Front Door
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
ms.openlocfilehash: 8a3ae8065553b34a72528cb0f2681e327dc90097
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985192"
---
# <a name="http2-support-in-azure-front-door"></a>HTTP/2-stöd i Azure Front Door

Http/2-stöd är för närvarande aktivt för alla Azure Front Door-konfigurationer. Inga ytterligare åtgärder krävs från kunder.

HTTP/2 är en större revidering av HTTP/1.1. Det ger snabbare webbprestanda, minskad svarstid och förbättrad användarupplevelse, samtidigt som de välbekanta HTTP-metoderna, statuskoderna och semantiken bibehålls. Även om HTTP/2 är utformat för att fungera med HTTP och HTTPS stöder många klientwebbläsare endast HTTP/2 över TLS (Transport Layer Security).

> [!NOTE]
> HTTP/2-protokollsupport är endast tillgängligt för begäranden från klienter till ytterdörren. Kommunikationen från ytterdörren till baksidan slutar i backend-poolen sker över HTTP/1.1. 

### <a name="http2-benefits"></a>HTTP/2-fördelar

Fördelarna med HTTP/2 inkluderar:

*   **Multiplexering och samtidighet**

    Om du använder HTTP 1.1 krävs flera TCP-anslutningar för att göra flera resursbegäranden, och varje anslutning har prestandaomkostnader som är associerade med den. HTTP/2 gör att flera resurser kan begäras på en enda TCP-anslutning.

*   **Sidkomprimering**

    Genom att komprimera HTTP-huvuden för serverade resurser minskas tiden på tråden avsevärt.

*   **Flödesberoenden**

    Stream-beroenden gör det möjligt för klienten att ange för servern vilka resurser som har prioritet.


## <a name="http2-browser-support"></a>Support för HTTP/2 webbläsare

Alla de stora webbläsarna har implementerat HTTP/2-stöd i sina nuvarande versioner. Webbläsare som inte stöds går automatiskt tillbaka till HTTP/1.1.

|Webbläsare|Lägsta version|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Efterföljande moment

Mer information om HTTP/2 finns på följande resurser:

- [HTTP/2 specifikation hemsida](https://http2.github.io/)
- [Officiella vanliga frågor om HTTP/2](https://http2.github.io/faq/)
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
