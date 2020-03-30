---
title: Ytterdörren i Azure – URL-omskrivning | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå hur Azure Front Door gör URL Skriva om för dina vägar, om den är konfigurerad.
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
ms.openlocfilehash: 1e5bd565be7a1cabf08ddf33c65eb12b5294249f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471480"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL skriva om (anpassad vidarebefordringssökväg)
Azure Front Door stöder URL-omskrivning genom att du kan konfigurera en valfri **anpassad vidarebefordringssökväg** som ska användas när begäran om att vidarebefordra till serverdelen skapas. Om ingen sökväg för vidarebefordran har angetts kopierar Front Door som standard den inkommande URL-sökvägen till den URL som används i den vidarebefordrade begäran. Värdhuvudet som används i den vidarebefordrade begäran ser ut så som det konfigurerats för den valda serverdelen. Läs [Serverda värdhuvud för](front-door-backend-pool.md#hostheader) att ta reda på vad den gör och hur du kan konfigurera den.

Den kraftfulla delen av URL skriva om med hjälp av anpassad vidarebefordringssökväg är att den kopierar någon del av den inkommande sökvägen som matchar till en jokerteckensökväg till den vidarebefordrade sökvägen (dessa bansegment är de **gröna** segmenten i exemplet nedan):
</br>
![Url:en skrivas om i Azure-klientdörren][1]

## <a name="url-rewrite-example"></a>Exempel på omskrivning av URL
Överväg en routningsregel med följande frontend-värdar och sökvägar konfigurerade:

| Värdar      | Sökvägar       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo (foo)        |
|            | /foo/\*     |
|            | /foo/bar/\* |

Den första kolumnen i tabellen nedan visar exempel på inkommande begäranden och den andra kolumnen visar vad som skulle vara den "mest specifika" matchande vägen "Path".  Den tredje och efterföljande kolumnerna i den första raden i tabellen är exempel på konfigurerade **anpassade vidarebefordringssökvägar**, med resten av raderna i dessa kolumner som representerar exempel på vad den vidarebefordrade sökvägen skulle vara om den matchade med begäran på den raden.

Om vi till exempel läser över den andra raden, `www.contoso.com/sub`säger det att för `/`inkommande begäran , om `/sub`den anpassade vidarebefordringsvägen var , skulle den vidarebefordrade sökvägen vara . Om den anpassade vidarebefordringssökvägen `/fwd/`var `/fwd/sub`, skulle den vidarebefordrade sökvägen vara . Och så vidare, för de återstående kolumnerna. De **betonade** delarna av banorna nedan representerar de delar som ingår i jokerteckenmatchningen.


| Inkommande begäran       | Mest specifik matchningssökväg | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**Sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/sub**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo (foo)                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**Bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/bar**bar**   |


## <a name="optional-settings"></a>Valfria inställningar
Det finns ytterligare valfria inställningar som du också kan ange för en viss routningsregelinställningar:

* **Cachekonfiguration** - Om den är inaktiverad eller inte angiven, försöker begäranden som matchar den här routningsregeln inte använda cachelagrat innehåll och hämtas alltid från serverda. Läs mer om [Caching med ytterdörr](front-door-caching.md).



## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg