---
title: HTTP/2-stöd i Azure CDN | Microsoft-dokument
description: Läs mer om HTTP/2- och CDN-stöd.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67849979"
---
# <a name="http2-support-in-azure-cdn"></a>HTTP/2-stöd i Azure CDN

HTTP/2 är en större revidering av HTTP/1.1\. Det ger snabbare webbprestanda, minskad svarstid och förbättrad användarupplevelse, samtidigt som de välbekanta HTTP-metoderna, statuskoderna och semantiken bibehålls. Även om HTTP/2 är utformat för att fungera med HTTP och HTTPS stöder många klientwebbläsare endast HTTP/2 via TLS.

### <a name="http2-benefits"></a>HTTP/2 Fördelar

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

## <a name="enabling-http2-support-in-azure-cdn"></a>Aktivera HTTP/2-stöd i Azure CDN

Http/2-stöd är för närvarande aktivt för alla Azure CDN-profiler. Inga ytterligare åtgärder krävs från kunder.

## <a name="next-steps"></a>Efterföljande moment

Om du vill se fördelarna med HTTP/2 i aktion, se [den här demon från Akamai](https://http2.akamai.com/demo).

Mer information om HTTP/2 finns på följande resurser:

*   [HTTP/2 specifikation hemsida](https://http2.github.io/)
*   [Officiella vanliga frågor om HTTP/2](https://http2.github.io/faq/)
*   [Akamai HTTP/2 information](https://http2.akamai.com/)

Mer information om Azure CDN:s tillgängliga funktioner finns i [Azure CDN Översikt](https://azure.microsoft.com/documentation/articles/cdn-overview/).