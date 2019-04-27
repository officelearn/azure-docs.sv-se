---
title: Azure Front luckan Service - URL-omskrivning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure ytterdörren Service fungerar URL-Omskrivningsregler för din vägar, om konfigurerad.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736197"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-omskrivning (anpassade vidarebefordran sökväg)
Azure ytterdörren Service stöder URL-omskrivning genom att låta dig konfigurera en valfri **anpassade vidarebefordran sökvägen** att använda när begäran vidarebefordra till serverdelen. Om ingen sökväg för vidarebefordran har angetts kopierar Front Door som standard den inkommande URL-sökvägen till den URL som används i den vidarebefordrade begäran. Värdhuvudet som används i den vidarebefordrade begäran ser ut så som det konfigurerats för den valda serverdelen. Läs [serverdel värdhuvud](front-door-backend-pool.md#hostheader) den gör och hur du kan konfigurera den.

Den kraftfulla delen av URL-omskrivning med hjälp av anpassade vidarebefordran sökväg är att den kommer att kopiera någon del av den inkommande sökvägen som matchar till en jokersökväg i vidarebefordrade sökvägen (dessa segment för resurssökväg är den **grön** segment i exemplet nedan):
</br>
![Azure ytterdörren URL-omskrivning][1]

## <a name="url-rewrite-example"></a>Exempel för URL-omskrivning
Överväg en routningsregel med följande frontend-värdar och sökvägar som är konfigurerad:

| Värdar      | Sökvägar       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/stapel /\* |

Den första kolumnen i tabellen nedan visar exempel på inkommande begäranden och den andra kolumnen visar vad skulle vara ”de mest specifika” matchande väg ”sökväg”.  De tredje och efterföljande kolumnerna i den första raden i tabellen är exempel på konfigurerade **anpassad vidarebefordran sökvägar**, med resten av raderna i dessa kolumner visar exempel på vad den vidarebefordrade begäran sökvägen skulle vara om den matchade på begäran på den raden.

Till exempel om vi läser över den andra raden, det texten att för inkommande begäran `www.contoso.com/sub`, om sökvägen för anpassade vidarebefordran var `/`, och sedan vidarebefordrade sökvägen skulle bli `/sub`. Om sökvägen för anpassade vidarebefordran var `/fwd/`, och sedan vidarebefordrade sökvägen skulle bli `/fwd/sub`. Och så vidare, för övriga kolumner. Den **framhållit** delar av sökvägar nedan motsvarar de delar som ingår i matchningen med jokertecken.


| Inkommande begäran       | De mest specifika matchning sökväg | /          | /fwd/          | /foo/          | /foo/stapel /          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/stapel /          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/stapel/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a, b och c** | /foo/stapel/**a, b och c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/stapel /          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/stapel /          |
| www\.contoso.com/foo/**stapel** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**stapel**   | /foo/stapel/**stapel**   |


## <a name="optional-settings"></a>Valfria inställningar
Det finns ytterligare valfria inställningar som du kan också ange för alla angivna regelinställningar för routning:

* **Cachelagra Configuration** – om inaktiverat eller inget värde anges kommer begäranden som matchar regeln routning inte kommer att använda cachelagrat innehåll och hämtar i stället alltid från serverdelen. Läs mer om [cachelagring med ytterdörren](front-door-caching.md).



## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg