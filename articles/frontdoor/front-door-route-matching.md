---
title: Azure ytterdörren Service – routning regel matchande övervakning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure ytterdörren Service matchar vilken regel för vidarebefordran för en inkommande begäran
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
ms.openlocfilehash: 23582215654ff2d5003fe611c7149ad760d72bc5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957048"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Hur ytterdörren matchar begäranden till en regel för vidarebefordran

När du upprättar en anslutning och gör en SSL-handskakning, när en begäran landar på en miljö med ytterdörren en av de första sakerna som ytterdörren gör är att bestämma från alla konfigurationer, vilka viss routningsregel att matcha begäran till och sedan vidta åtgärden som definierats. Följande dokument beskriver hur ytterdörren avgör vilken väg konfiguration som ska användas vid bearbetning av en HTTP-begäran.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktur för en ytterdörren route-konfiguration
En ytterdörren routning regelkonfigurationen består av två huvuddelar: en ”vänster” och en ”höger”. Vi matchar den inkommande begäran till vänster på vägen medan till höger som definierar hur vi behandla begäran.

### <a name="incoming-match-left-hand-side"></a>Inkommande matchar (vänster)
Följande egenskaper avgör om den inkommande begäranden matchar routningsregel (eller till vänster):

* **HTTP-protokoll** (HTTP/HTTPS)
* **Värdar** (till exempel www.foo.com \*. bar.com)
* **Sökvägar** (till exempel /\*, /users/\*, /file.gif)

De här egenskaperna expanderas ut internt så att varje kombination av protokoll/Värdsökvägen är en potentiell matchningsuppsättning.

### <a name="route-data-right-hand-side"></a>Dirigera data (till höger)
Beslut av behandla begäran, beror på om cachelagring är aktiverat eller inte för den specifika vägen. Om vi inte har ett cachelagrat svar för begäran, så kommer vi vidarebefordra begäran till den korrekta serverdelen i konfigurerade serverdelspoolen.

## <a name="route-matching"></a>Dirigera matchar
Det här avsnittet fokuserar på hur vi matchar till en viss ytterdörren routningsregel. Det grundläggande konceptet är att vi alltid matchar till den **de mest specifika matchar det första** söker bara på ”vänster”.  Vi först matcha baserat på HTTP-protokollet, sedan Frontend-värden och sedan sökvägen.

### <a name="frontend-host-matching"></a>Frontend-värden som matchar
När matchning Frontend-värdar, använder vi logiken enligt nedan:

1. Sök efter eventuella routning med en exakt matchning på värden.
2. Om inga exakta frontend-värdar matchar Avvisa begäran och skicka ett 400 Felaktig begäran-fel.

För att förklara ytterligare den här processen kan du nu ska vi titta på en exempelkonfiguration av ytterdörren vägar (endast vänster):

| Routingregeln | Frontend-värdar | Sökväg |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /Users/\* |
| C | www.Fabrikam.com, foo.adventure works.com  | /\*, /images/\* |

Om följande förfrågningar skickades till ytterdörren, skulle de matchar mot följande regler för vidarebefordran ovan:

| Inkommande frontend-värd | Matchade routning regler |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www.Fabrikam.com | C |
| images.Fabrikam.com | Fel 400: Felaktig begäran |
| foo.adventure works.com | C |
| contoso.com | Fel 400: Felaktig begäran |
| www.Adventure-Works.com | Fel 400: Felaktig begäran |
| www.northwindtraders.com | Fel 400: Felaktig begäran |

### <a name="path-matching"></a>Sökvägsmatchning
När du avgör den specifika frontend-värden och filtrering möjliga routningsregler och bara vägar med den frontend-värden, filtrerar ytterdörren sedan regler för routning baserat på sökvägen för begäran. Vi använder en liknande logik som frontend-värdar:

1. Leta efter någon regel för vidarebefordran med en exakt matchning i sökvägen
2. Om ingen exakt matchning sökvägar, leta efter routningsregler med jokertecken sökväg som matchar
3. Om det finns inga regler för routning med en matchande sökväg Avvisa begäran och returnera en 400: Felaktig begäran fel HTTP-svar.

>[!NOTE]
> Alla sökvägar utan jokertecken anses vara exakt matchning sökvägar. Även om sökvägen slutar med ett snedstreck, betraktas fortfarande exakt matchning.

För att förklara ytterligare kan du nu ska vi titta på en annan uppsättning exempel:

| Routingregeln | Frontend-värd    | Sökväg     |
|-------|---------|----------|
| A     | www.contoso.com | /        |
| B     | www.contoso.com | /\*      |
| C     | www.contoso.com | /AB      |
| D     | www.contoso.com | /ABC     |
| E     | www.contoso.com | /ABC/    |
| F     | www.contoso.com | /ABC/\*  |
| G     | www.contoso.com | / abc/def |
| H     | www.contoso.com | /Path/   |

Med denna konfiguration kan det skulle resultera i följande exempel matchande tabell:

| Inkommande begäran    | Matchade väg |
|---------------------|---------------|
| www.contoso.com/            | A             |
| www.contoso.com/a           | B             |
| www.contoso.com/AB          | C             |
| www.contoso.com/ABC         | D             |
| www.contoso.com/abzzz       | B             |
| www.contoso.com/ABC/        | E             |
| www.contoso.com/ABC/d       | F             |
| www.contoso.com/ABC/def     | G             |
| www.contoso.com/ABC/defzzz  | F             |
| www.contoso.com/ABC/def/ghi | F             |
| www.contoso.com/Path        | B             |
| www.contoso.com/Path/       | H             |
| www.contoso.com/Path/zzz    | B             |

>[!WARNING]
> </br> Om det finns inga regler för routning för en exakt matchning frontend-värd med en allomfattande dirigera sökväg (`/*`), så inte att vara en matchning för att någon regel för vidarebefordran.
>
> Exempel på konfiguration:
>
> | Routa | Värd             | Sökväg    |
> |-------|------------------|---------|
> | A     | Profile.contoso.com | /API/\* |
>
> Matchande tabell:
>
> | Inkommande begäran       | Matchade väg |
> |------------------------|---------------|
> | Profile.domain.com/Other | Ingen. Fel 400: Felaktig begäran |

### <a name="routing-decision"></a>Beslut om routning
När vi har kopplats till en enda ytterdörren routningsregel kan behöver vi välja hur du vill bearbeta begäran. Om en matchande routningsregel ytterdörren har ett cachelagrat svar som är tillgängliga hämtar samma hanteras tillbaka till klienten. Annars hämtar utvärderas nästa sak är om du har konfigurerat [URL-Omskrivningsregler (anpassade vidarebefordran sökväg)](front-door-url-rewrite.md) för matchade routningen regel eller inte. Om det inte finns en anpassad vidarebefordran sökväg som definierats, hämtar begäran vidarebefordras till den korrekta serverdelen i konfigurerade serverdelspoolen skick. Annars begäran sökvägen uppdateras enligt den [sökvägen för anpassade vidarebefordran](front-door-url-rewrite.md) definierade och sedan vidare till serverdelen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en ytterdörren](quickstart-create-front-door.md).
- Lär dig [hur ytterdörren fungerar](front-door-routing-architecture.md).
