---
title: Azure Ytterdörr - Routningsregel matchning övervakning | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå hur Azure Front Door matchar vilken routningsregel som ska användas för en inkommande begäran
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
ms.openlocfilehash: 605974e76c3ca878784129f7c9827a78d0642da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471599"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Så här matchar Ytterdörren begäranden till en routningsregel

Efter att ha upprättat en anslutning och gjort en SSL-handskakning, när en begäran landar på en ytterdörr miljö en av de första sakerna som Ytterdörren gör är att bestämma från alla konfigurationer, som särskild routing regel för att matcha begäran till och sedan ta den definierade åtgärden. I följande dokument beskrivs hur Ytterdörren avgör vilken vägkonfiguration som ska användas vid bearbetning av en HTTP-begäran.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktur för en konfiguration av ytterdörrens rutt
En konfiguration av routningsregeln för ytterdörren består av två huvuddelar: en "vänster sida" och en "höger sida". Vi matchar den inkommande begäran till vänster sida av rutten medan den högra sidan definierar hur vi behandlar begäran.

### <a name="incoming-match-left-hand-side"></a>Inkommande matchning (vänster sida)
Följande egenskaper avgör om den inkommande begäran matchar routningsregeln (eller vänster sida):

* **HTTP-protokoll** (HTTP/HTTPS)
* **Värdar** (till exempel\.www foo.com, \*.bar.com)
* **Sökvägar** (till exempel\*/ ,\*/users/ , /file.gif)

Dessa egenskaper expanderas ut internt så att varje kombination av protokoll/värd/sökväg är en potentiell matchningsuppsättning.

### <a name="route-data-right-hand-side"></a>Ruttdata (höger sida)
Beslutet om hur begäran ska behandlas beror på om cachelagring är aktiverat eller inte för den specifika vägen. Så om vi inte har ett cachelagrat svar för begäran vidarebefordrar vi begäran till lämplig serverning i den konfigurerade serverda poolen.

## <a name="route-matching"></a>Ruttmatchning
Det här avsnittet fokuserar på hur vi matchar en viss routningsregel för ytterdörren. Grundtanken är att vi alltid matchar den **mest specifika matchen först** och tittar bara på "vänstersidan".  Vi matchar först baserat på HTTP-protokoll, sedan Frontend värd, sedan sökvägen.

### <a name="frontend-host-matching"></a>Matchande klientdelsvärd
När du matchar Frontend-värdar använder vi logiken enligt nedan:

1. Leta efter routning med en exakt matchning på värden.
2. Om inga exakta klientdelsvärdar matchar, avvisar du begäran och skickar ett fel på 400 felaktiga begäranden.

För att förklara denna process ytterligare, låt oss titta på ett exempel konfiguration av Ytterdörren rutter (endast vänster sida):

| Routingregeln | Frontend värdar | Sökväg |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /användare/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Om följande inkommande begäranden skickades till Ytterdörren skulle de matcha mot följande routningsregler ovanifrån:

| Värd för inkommande klientdel | Matchade routningsregeler |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Fel 400: Felaktig begäran |
| foo.adventure-works.com | C |
| contoso.com | Fel 400: Felaktig begäran |
| www\.adventure-works.com | Fel 400: Felaktig begäran |
| www\.northwindtraders.com | Fel 400: Felaktig begäran |

### <a name="path-matching"></a>Matchning av sökväg
När du har bestämt den specifika frontend-värden och filtrerat möjliga routningsregler till bara de vägar med den frontend-värden filtrerar ytterdörren sedan routningsreglerna baserat på sökvägen för begäran. Vi använder en liknande logik som frontend värdar:

1. Leta efter en routningsregel med en exakt matchning på banan
2. Om ingen exakt matchning sökvägar, leta efter routningsregler med en jokerteckensökväg som matchar
3. Om inga routningsregler hittas med en matchande sökväg, avvisa sedan begäran och returnera ett HTTP-svar på 400: Felbegäran.

>[!NOTE]
> Alla sökvägar utan jokertecken anses vara exakta matchningsvägar. Även om banan slutar i ett snedstreck anses den fortfarande vara exakt matchning.

För att förklara ytterligare, låt oss titta på en annan uppsättning exempel:

| Routingregeln | Frontend värd    | Sökväg     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc (abc)     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /sökväg/   |

Med tanke på att konfigurationen, följande exempel matchande tabell skulle resultera:

| Inkommande begäran    | Matchad rutt |
|---------------------|---------------|
| www\.contoso.com/            | A             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> Om det inte finns några routningsregler för en klientdel med exakt`/*`matchning med en catch-all-väg sökväg ( ) kommer det inte att finnas någon matchning till någon routningsregel.
>
> Exempel konfiguration:
>
> | Routa | Värd             | Sökväg    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Matchande tabell:
>
> | Inkommande begäran       | Matchad rutt |
> |------------------------|---------------|
> | profile.domain.com/other | Inga. Fel 400: Felaktig begäran |

### <a name="routing-decision"></a>Beslut om routning
När vi har matchat till en enda routningsregel för ytterdörren måste vi välja hur begäran ska behandlas. Om för den matchade routningsregeln har Ytterdörren ett cachelagrat svar tillgängligt så serveras samma tillbaka till klienten. Annars är nästa sak som utvärderas om du har konfigurerat [URL Skriv om (anpassad vidarebefordringssökväg)](front-door-url-rewrite.md) för den matchade routningsregeln eller inte. Om det inte finns en anpassad vidarebefordringssökväg definierad vidarebefordras begäran till lämplig serverning i den konfigurerade serverda poolen som den är. Annars uppdateras sökvägen för begäran enligt den [anpassade vidarebefordringssökvägen](front-door-url-rewrite.md) som definierats och sedan vidarebefordras till backend.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
