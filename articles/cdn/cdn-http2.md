---
title: Stöd för HTTP/2 i Azure CDN | Microsoft Docs
description: Läs mer om stöd för HTTP/2 och CDN.
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
ms.author: rli
ms.openlocfilehash: c59ecceee35587f1b7d844f160ac0f61c5288e0e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593752"
---
# <a name="http2-support-in-azure-cdn"></a>Stöd för HTTP/2 i Azure CDN

HTTP/2 är en huvudversion till HTTP/1.1\. Det ger snabbare webbprestanda, minskade svarstid och förbättrad upplevelse samtidigt som den välbekanta HTTP-metoder och statuskoder semantik. Även om HTTP/2 är utformat för att arbeta med HTTP och HTTPS, stöd många webbläsare för klienten endast för HTTP/2 via TLS.

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

|Browser|Lägsta Version|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Aktivera stöd för HTTP/2 i Azure CDN

För närvarande är stöd för HTTP/2 aktiv för alla Azure CDN-profiler. Ingen ytterligare åtgärd krävs från kunder.

## <a name="next-steps"></a>Nästa steg

Fördelarna med HTTP/2 i praktiken finns i [den här demon från Akamai](https://http2.akamai.com/demo).

Mer information om HTTP/2 finns i följande resurser:

*   [Startsida för HTTP/2-specifikation](https://http2.github.io/)
*   [Officiella HTTP/2 vanliga frågor och svar](https://http2.github.io/faq/)
*   [Akamai HTTP/2-information](https://http2.akamai.com/)

Läs mer om Azure CDN tillgängliga funktioner i den [översikt över Azure CDN](https://azure.microsoft.com/documentation/articles/cdn-overview/).