---
title: Verizon-specifika HTTP-huvuden för Azure CDN regelmotor | Microsoft Docs
description: Den här artikeln beskriver hur du använder Verizon-specifika HTTP-huvuden med Azure CDN regelmotor.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: 7fa76a2c5b01e623e490edd0091f7fb372b7085f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093246"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon-specifika HTTP-huvuden för Azure CDN regelmotor

För **Azure CDN Premium från Verizon** produkter, när en HTTP-begäran skickas till den ursprungliga servern, point of presence (POP)-servern kan lägga till en eller flera reserverade rubriker (eller särskilda proxy-huvuden) i klientbegäran till POP. Dessa rubriker måste vara uppfyllda utöver standard vidarebefordran huvuden som togs emot. Läs om hur standard begärandehuvuden [begär fält](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Om du vill förhindra att en av dessa reserverade huvuden som läggs till i Azure CDN (Content Delivery Network) POP-begäran till den ursprungliga servern, måste du skapa en regel med den [Proxy särskilda rubriker funktionen](cdn-rules-engine-reference-features.md#proxy-special-headers) i regelmotorn. Undanta rubriken som du vill ta bort från listan över sidhuvuden i fältet huvuden i den här regeln. Om du har aktiverat den [felsöka Cache svarshuvuden funktionen](cdn-rules-engine-reference-features.md#debug-cache-response-headers), se till att lägga till nödvändiga `X-EC-Debug` rubriker. 

Till exempel för att ta bort den `Via` rubrik som är fältet rubriker i regeln bör innehålla följande lista med rubriker: *X-vidarebefordrade-, X-vidarebefordrade-protokoll, X-värden, X-Midgress X-Gateway-listan, X-EG-Name, värd*. 

![Regel för proxy särskilda rubriker](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

I följande tabell beskrivs de rubriker som kan läggas till med Verizon CDN POP i begäran:

Begärandehuvud | Beskrivning | Exempel
---------------|-------------|--------
[via](#via-request-header) | Identifierar POP-servern som körs via en proxy begäran till en ursprungsservern. | HTTP/1.1 ECS (dca/1A2B)
X-vidarebefordrade-för | Anger den som begär IP-adress.| 10.10.10.10
X-vidarebefordrade-protokoll | Anger den begäran-protokollet. | http
X-värden | Anger den förfrågans värdnamn. | CDN.mydomain.com
X-Midgress | Anger om begäran har via proxy till en ytterligare CDN-server. Till exempel en server till ursprunget shield POP-server eller en POP-servern till ADN gateway-server. <br />Den här rubriken läggs till i begäran endast när midgress trafik äger rum. I det här fallet är huvudet inställd till 1 anger att begäran har via proxy till en ytterligare CDN-server.| 1
[Värd](#host-request-header) | Identifierar värden och den port där du kan hitta det begärda innehållet. | Marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: Identifierar listan redundans av ADN Gateway-servrar som tilldelats ett kund-ursprung. <br />Ursprung shield: Anger uppsättningen ursprungsservrar shield som tilldelats ett kund-ursprung. | `icn1,hhp1,hnd1`
X-EG -_&lt;namn&gt;_ | Begärandehuvuden som börjar med *X-EG* (till exempel X-EG-tagg, [X-EG-Debug](cdn-http-debug-headers.md)) är reserverade för användning av CDN.| waf-produktion

## <a name="via-request-header"></a>Via huvudet i begäran
Format som den `Via` begäran rubrik identifierar en POP-server anges med följande syntax:

`Via: Protocol from Platform (POP/ID)` 

De termer som används i syntax definieras enligt följande:
- Protokoll: Anger versionen av protokollet (till exempel HTTP/1.1) används för att proxy begäran. 

- Plattform: Anger den plattform som innehållet har begärts. Följande koder är giltiga för det här fältet: 
    Kod | Plattform
    -----|---------
    ECAcc | HTTP-stor
    ECS   | HTTP-liten
    ECD   | Application delivery network (ADN)

- POP: Anger den [POP](cdn-pop-abbreviations.md) som hanteras begäran. 

- ID: endast för internt bruk.

### <a name="example-via-request-header"></a>Exempel Via huvudet i begäran

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Värdhuvud för begäran
POP-servrar kommer att skriva över den `Host` rubrik när båda av följande villkor föreligger:
- Källan för det begärda innehållet är en kund ursprungsservern.
- Motsvarande kund ursprungets HTTP-Värdrubriken alternativet är inte tom.

Den `Host` huvudet i begäran kommer att skrivas över för att återspegla värdet som definieras i HTTP-Värdrubriken-alternativet.
Om kunden ursprunget HTTP-Värdrubriken alternativet är inställt på tom, sedan den `Host` förfrågnings-huvud som skickas av den som begär vidarebefordras till kundens ursprungsservern.

## <a name="x-gateway-list-request-header"></a>Begärandehuvud i X-Gateway-List
En POP-server lägger du till/skriver över den ' X Gatewaylista begärandehuvudet när något av följande villkor är uppfyllda:
- Begäran pekar på ADN-plattformen.
- Begäran vidarebefordras till en kund ursprungsservern som skyddas av funktionen ursprung Shield.

