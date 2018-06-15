---
title: Verizon-specifika HTTP-huvuden för Azure CDN regelmotor | Microsoft Docs
description: Den här artikeln beskriver hur du använder Verizon-specifika HTTP-huvuden med Azure CDN regelmotor.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: v-deasim
ms.openlocfilehash: 1a4bb48efe2a91c85b773341bb38b0f3ce4c7d9b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517452"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon-specifika HTTP-huvuden för Azure CDN regelmotor

För **Azure CDN Premium från Verizon** produkter, när en HTTP-begäran skickas till den ursprungliga servern punkt av förekomst (POP)-server kan lägga till en eller flera reserverade huvuden (eller proxy särskilda huvuden) i klientbegäran till POP. Dessa huvuden är utöver standard vidarebefordran huvuden som togs emot. Information om standard begärandehuvuden finns [begära fält](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Om du vill förhindra att något av dessa reserverade huvuden som läggs till i Azure CDN (Content Delivery Network) POP-begäran till den ursprungliga servern måste du skapa en regel med den [Proxy särskilda huvuden funktionen](cdn-rules-engine-reference-features.md#proxy-special-headers) i regler-motorn. Exkludera rubriken som du vill ta bort från listan över sidhuvuden i fältet huvuden i den här regeln. Om du har aktiverat den [Debug Cache svarshuvuden funktionen](cdn-rules-engine-reference-features.md#debug-cache-response-headers), bör du lägga till nödvändiga `X-EC-Debug` huvuden. 

Till exempel för att ta bort den `Via` sidhuvud, fältet huvuden i regeln bör innehålla följande lista med rubriker: *X vidarebefordras för, X-vidarebefordras-Proto, X-värden, X Midgress X-Gateway-listan, X-EC-Name, värd*. 

![Proxy särskilda huvuden regel](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

I följande tabell beskrivs sidhuvuden som kan läggas till av Verizon CDN POP i förfrågan:

Huvudet i begäran | Beskrivning | Exempel
---------------|-------------|--------
[via](#via-request-header) | POP-servern via proxy som identifierar begäran till en ursprungsservern. | HTTP/1.1 ECS (dca/1A2B)
X vidarebefordras för | Anger förfrågarens IP-adress.| 10.10.10.10
X vidarebefordras Proto | Anger det begäran-protokollet. | http
X-värden | Anger värdnamnet för en begäran. | CDN.mydomain.com
X-Midgress | Anger om begäran har via proxy via en ytterligare CDN-server. En server till ursprunget shield POP-server eller en POP-servern till ADN gateway-server. <br />Det här huvudet har lagts till begäran endast när midgress trafik äger rum. I det här fallet är rubriken värdet 1 anger att begäran har via proxy via en ytterligare CDN-server.| 1
[Värd](#host-request-header) | Identifierar värden och den port där det begärda innehållet kan hittas. | Marketing.mydomain.com:80
[X-Gateway-lista](#x-gateway-list-request-header) | ADN: Identifierar redundans över ADN Gateway-servrar som är tilldelade till en kund ursprung. <br />Ursprung shield: Anger uppsättningen ursprung shield servrar som tilldelats till en kund ursprung. | `icn1,hhp1,hnd1`
X-EC -_&lt;namn&gt;_ | Huvuden för begäran som börjar med *X EC* (t.ex, X-EC-taggen [X EC Debug](cdn-http-debug-headers.md)) är reserverade för användning av CDN.| brandvägg produktion

## <a name="via-request-header"></a>Via huvudet i begäran
Formatet som den `Via` begäran huvud identifierar en POP server anges med följande syntax:

`Via: Protocol from Platform (POP/ID)` 

De termer som används i syntax definieras enligt följande:
- Protokoll: Anger versionen av protokollet (till exempel HTTP/1.1) som används till proxy begäran. 

- Plattform: Anger den plattform som innehållet har begärts. Följande koder är giltiga för det här fältet: 
    Kod | Plattform
    -----|---------
    ECAcc | Stora HTTP
    ECS   | HTTP-liten
    ECD   | Application delivery network (ADN)

- POP: Anger den [POP](cdn-pop-abbreviations.md) som hanterade begäran. 

- ID: endast för intern användning.

### <a name="example-via-request-header"></a>Exempel Via huvudet i begäran

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Värdhuvudet i begäran
POP-servrar kommer att skriva över den `Host` huvud när båda av följande villkor är uppfyllda:
- Källan för det begärda innehållet är en kund ursprungsservern.
- Motsvarande kund ursprungets värdhuvud för HTTP-alternativet är inte tomt.

Den `Host` skrivs över för att återspegla det värde som anges i alternativet värdhuvud för HTTP-huvudet i begäran.
Om kunden ursprung värdhuvud för HTTP-alternativet är tom, sedan den `Host` huvudet i begäran som skickats av beställaren vidarebefordras till kundens ursprungsservern.

## <a name="x-gateway-list-request-header"></a>Begärandehuvudet X-Gateway-lista
POP-server Lägg till/skrivs den ' begärandehuvudet X-Gateway-listan när något av följande villkor är uppfyllda:
- Begäran pekar på ADN-plattformen.
- Begäran vidarebefordras till en kund ursprungsservern som skyddas av funktionen ursprung Shield.

