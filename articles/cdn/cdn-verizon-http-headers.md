---
title: Verizon-specifika HTTP-huvuden för Azure CDN-regler motor | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder Verizon-specifika HTTP-huvuden med Azure CDN-regelmotor.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: a5881bea578f2791f8dc0d6e760fd15c6f47e435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593253"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon-specifika HTTP-huvuden för Azure CDN-regler motor

För **Azure CDN Premium från Verizon-produkter** kan POP-servern lägga till ett eller flera reserverade rubriker (eller proxyspecialhuvuden) när en HTTP-begäran skickas till ursprungsservern. Dessa rubriker är utöver de vanliga vidarebefordringsrubriker som tas emot. Information om standardfråre för begäran finns i [Fält för begäran](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Om du vill förhindra att något av dessa reserverade rubriker läggs till i POP-begäran om Azure CDN (Content Delivery Network) till ursprungsservern måste du skapa en regel med [funktionen Proxy Special Headers](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) i regelmotorn. I den här regeln utesluter du det huvud som du vill ta bort från standardlistan med rubriker i sidhuvudena. Om du har aktiverat [funktionen Debug Cache Response Headers](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers)måste `X-EC-Debug` du lägga till nödvändiga rubriker. 

Om du till `Via` exempel vill ta bort huvudet bör huvudfältet i regeln innehålla följande lista med rubriker: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Proxy specialrubriker regel](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

I följande tabell beskrivs de rubriker som kan läggas till av Verizon CDN POP i begäran:

Begärandehuvud | Beskrivning | Exempel
---------------|-------------|--------
[Via](#via-request-header) | Identifierar POP-servern som proxied begäran till en ursprungsserver. | HTTP/1.1 ECS (dca/1A2B)
X-vidarebefordrad-för | Anger beställarens IP-adress.| 10.10.10.10
X-vidarebefordrad-Proto | Anger protokollet för begäran. | http
X-värd | Anger begärans värdnamn. | cdn.mydomain.com
X-Midgress | Anger om begäran har hanterats via ytterligare en CDN-server. Till exempel en POP-server-till-ursprung-sköldserver eller en POP-server-till-ADN-gateway-server. <br />Det här huvudet läggs till i begäran endast när midgress trafik sker. I det här fallet är huvudet inställt på 1 för att ange att begäran har proxied genom en ytterligare CDN-server.| 1
[Värd](#host-request-header) | Identifierar värden och porten där det begärda innehållet kan hittas. | marketing.mydomain.com:80
[X-Gateway-lista](#x-gateway-list-request-header) | ADN: Identifierar redundanslistan över ADN Gateway-servrar som tilldelats en kundursprung. <br />Ursprungssköld: Anger den uppsättning ursprungssköldservrar som tilldelats en kundursprung. | `icn1,hhp1,hnd1`
X-EC-_&lt;namn&gt;_ | Begäranderubriker som börjar med *X-EG* (till exempel X-EC-Tag, [X-EC-Debug](cdn-http-debug-headers.md)) är reserverade för användning av CDN.| waf-produktion

## <a name="via-request-header"></a>Via begäran header
Det format genom `Via` vilket begäranden identifierar en POP-server anges av följande syntax:

`Via: Protocol from Platform (POP/ID)` 

De termer som används i syntaxen definieras på följande sätt:
- Protokoll: Anger den version av protokollet (till exempel HTTP/1.1) som används för att proxy begäran. 

- Plattform: Anger den plattform där innehållet begärdes. Följande koder gäller för det här fältet: 

    Kod | Plattform
    -----|---------
    Ecacc (europeiska) | HTTP Stor
    Ecs   | HTTP Liten
    Ecd   | Nätverk för programleverans (ADN)

- POP: Anger [pop](cdn-pop-abbreviations.md) som hanterade begäran. 

- ID: Endast för internt bruk.

### <a name="example-via-request-header"></a>Exempel via begäran header

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Huvud för värdbegäran
POP-servrarna skriver över `Host` huvudet när båda följande villkor är uppfyllda:
- Källan för det begärda innehållet är en kundursprungsserver.
- Motsvarande kundursprung http-värdhuvud är inte tomt.

Förfrånadshuvudet `Host` skrivs över för att återspegla det värde som definierats i alternativet HTTP-värdhuvud.
Om kundens ursprungs HTTP-värdhuvud alternativet är `Host` inställt på tom, vidarebefordras begäranden som skickas av beställaren till kundens ursprungsserver.

## <a name="x-gateway-list-request-header"></a>X-Gateway-list-begäranhuvud
En POP-server lägger till/skriver över "X-Gateway-List-begäranden när något av följande villkor är uppfyllda:
- Begäran pekar på ADN-plattformen.
- Begäran vidarebefordras till en kundursprungsserver som skyddas av Origin Shield-funktionen.

