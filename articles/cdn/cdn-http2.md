---
title: "HTTP-2-stöd i Azure CDN | Microsoft Docs"
description: "Läs mer om stöd för HTTP-2 och CDN."
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: rli
ms.openlocfilehash: 4f8dd685c3ae89535217d7a17a01c5129ca7e6e4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="http2-support-in-azure-cdn"></a>HTTP-2-stöd i Azure CDN

HTTP-2 är en större ändring till HTTP/1.1\. Det ger snabbare webbprestanda, minskade svarstid och ger en förbättrad upplevelse samtidigt bekant HTTP-metoderna, statuskoder och semantik. Även om HTTP-2 är utformat för att arbeta med HTTP och HTTPS, stöder endast HTTP/2 via TLS många klientens webbläsare.

###<a name="http2-benefits"></a>Fördelar med HTTP-2

Fördelarna med HTTP/2 inkluderar:

*   **Multiplexering och samtidighet**

    Med HTTP 1.1 flera gör flera förfrågningar för resursen kräver flera TCP-anslutningar och varje anslutning har prestanda försämras som är kopplade till den. HTTP-2 kan flera resurser som krävs för en TCP-anslutning.

*   **Komprimering av huvud**

    Genom att komprimera HTTP-huvuden för hanteras resurser minskas tiden på kabeln avsevärt.

*   **Stream-beroenden**

    Dataströmmen beroenden kan klienten för att ange att servern där resurser har prioritet.


##<a name="http2-browser-support"></a>Stöd för HTTP-2-webbläsare

Alla större webbläsare har implementerat HTTP/2-stöd i aktuell version. Webbläsare som inte stöds kommer den automatiskt reserv vid HTTP/1.1.

|Webbläsare|Lägsta Version|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

##<a name="enabling-http2-support-in-azure-cdn"></a>Aktivera HTTP-2-stöd i Azure CDN

För närvarande stöd för HTTP-2 är aktiv för **Azure CDN från Akamai** och **Azure CDN från Verizon** profiler. Ingen ytterligare åtgärd krävs från kunder.

##<a name="next-steps"></a>Nästa steg

Fördelarna med HTTP/2 i praktiken finns [i den här demon från Akamai](https://http2.akamai.com/demo).

Mer information om HTTP-2 finns i följande resurser:

*   [HTTP-2-specifikationen webbsida](https://http2.github.io/)
*   [Officiell HTTP/2 vanliga frågor och svar](https://http2.github.io/faq/)
*   [Akamai HTTP/2-information](https://http2.akamai.com/)

Mer information om tillgängliga funktioner i Azure CDN finns i [översikt över Azure CDN](https://azure.microsoft.com/documentation/articles/cdn-overview/).