---
title: Azure frontend-tjänst-URL-omskrivning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure-frontend-tjänsten skriver URL-skrivning för dina vägar, om den är konfigurerad.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: dc2126276e3e8e0d35ce8ed1f835544386659eff
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "60736197"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-omskrivning (sökväg för anpassad vidarebefordran)
Azure frontend-tjänsten stöder URL-omskrivning genom att göra det möjligt att konfigurera en valfri **anpassad vidarebefordrings Sök väg** som ska användas för att skapa begäran om att vidarebefordra den till Server delen. Om ingen sökväg för vidarebefordran har angetts kopierar Front Door som standard den inkommande URL-sökvägen till den URL som används i den vidarebefordrade begäran. Värdhuvudet som används i den vidarebefordrade begäran ser ut så som det konfigurerats för den valda serverdelen. Läs [värd rubriken för Server delen](front-door-backend-pool.md#hostheader) för att lära dig vad det gör och hur du kan konfigurera den.

Den kraftfulla delen av URL-omskrivning med anpassad vidarebefordrings Sök väg är att den kopierar någon del av den inkommande sökvägen som matchar en sökväg med jokertecken till den vidarebefordrade sökvägen (dessa Sök vägs segment är de **gröna** segmenten i exemplet nedan):
</br>
![URL-omskrivning i Azures frontend-dörr][1]

## <a name="url-rewrite-example"></a>Exempel på URL-omskrivning
Överväg en regel för routning med följande klient dels värdar och konfigurerade sökvägar:

| Värdar      | Sökvägar       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | foo\*     |
|            | /foo/bar/\* |

Den första kolumnen i tabellen nedan visar exempel på inkommande begär Anden och den andra kolumnen visar vad som skulle vara den "mest aktuella" matchande väg Sök väg ".  De tredje och efterföljande kolumnerna i den första raden i tabellen är exempel på konfigurerade **anpassade vidarebefordrande sökvägar**, med resten av raderna i de kolumnerna som representerar exempel på vad den vidarebefordrade begär ande sökvägen skulle vara om den matchade med begäran i radhöjd.

Om vi t. ex. läser på den andra raden, säger vi att för inkommande begäran `www.contoso.com/sub`, om den anpassade sökvägen för vidarebefordran var `/`, så skulle den vidarebefordrande `/sub`sökvägen vara. Om den anpassade vidarebefordrande sökvägen `/fwd/`var, är den vidarebefordrade sökvägen. `/fwd/sub` Och så vidare, för de återstående kolumnerna. De **betonade** delarna av Sök vägarna nedan representerar de delar som är en del av matchningen av jokertecken.


| Inkommande begäran       | Mest speciell matchnings Sök väg | /          | /fwd/          | foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | foo          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**Build**   | /FWD/**Sub**   | /foo/**Sub**   | /foo/bar/**Sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /FWD/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.-contoso.com/foo         | /foo                     | /          | /fwd/          | foo          | /foo/bar/          |
| www\.-contoso.com/foo/        | foo\*                  | /          | /fwd/          | foo          | /foo/bar/          |
| www\.contoso.com/foo/-**stapel** | foo\*                  | /**bar**   | /FWD/-**fält**   | /foo/-**fält**   | /foo/bar/-**fält**   |


## <a name="optional-settings"></a>Valfria inställningar
Det finns ytterligare valfria inställningar som du kan ange för alla inställningar för routningsregler:

* **Cache-konfiguration** – om det är inaktiverat eller ej angivet kommer begär Anden som matchar den här regeln inte att försöka använda cachelagrat innehåll. i stället hämtas de alltid från Server delen. Läs mer om [cachelagring med front dörren](front-door-caching.md).



## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg