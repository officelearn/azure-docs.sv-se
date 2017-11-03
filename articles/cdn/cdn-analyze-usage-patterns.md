---
title: "Core rapporter från Verizon | Microsoft Docs"
description: "Du kan visa användningsmönster för din CDN med hjälp av följande rapporter: bandbredd, överförda Data, träffar, Cache status, Cache träffar förhållandet, IPV4/IPv6-Data överförs."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e9ee0041061296e313b3372dce13b5b86b2369c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="core-reports-from-verizon"></a>Core rapporter från Verizon

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Genom att använda Verizon Core rapporter via hantera portal för Verizon profiler kan visa du användningsmönster för din CDN med följande rapporter:

* Bandbredd
* Data som överförs
* Träffar
* Cache-status
* Träffgrad för cache
* IPv4/IPv6-Data som överförs

## <a name="accessing-verizon-core-reports"></a>Åtkomst till Verizon Core rapporter
1. CDN-profilbladet klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och klicka sedan hovra över den **Core rapporter** utfällbar.  Klicka på önskad rapporten i menyn.
   
    ![CDN-hanteringsportalen - Core rapporter-menyn](./media/cdn-reports/cdn-core-reports.png)

## <a name="bandwidth"></a>Bandbredd
Rapporten bandbredd består av ett diagram och en datatabell som anger bandbreddsanvändningen för HTTP och HTTPS under en viss tidsperiod. Du kan visa bandbreddsanvändningen över alla CDN POP eller en specifik CDN POP. Den här rapporten kan du visa trafik toppar och distribution över CDN POP i Mbit/s.

* Välj alla Edge noder för att se trafik från alla noder eller välj en specifik region/nod i listrutan.
* Ange datumintervall för att visa data för dagens denna vecka/denna månad, etc. eller ange anpassade datum och klicka sedan på **Gå** att kontrollera ditt val har uppdaterats.
* Du kan exportera och hämta data genom att klicka på ikonen för excel-blad som visas bredvid **Gå**.

Rapporten uppdateras var femte minut.

![Bandbredd-rapport](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Data som överförs
Den här rapporten består av en tabell för diagram och data som anges trafik användning för HTTP och HTTPS under en viss tidsperiod. Du kan visa hur trafik över alla CDN POP eller en viss POP. Den här rapporten kan du visa trafik toppar och distribution över CDN POP i GB.

* Välj alla Edge noder för att se trafik från alla anteckningar eller välj en specifik region/nod i listrutan.
* Ange datumintervall för att visa data för dagens denna vecka/denna månad, etc. eller ange anpassade datum och klicka sedan på **Gå** att kontrollera ditt val har uppdaterats.
* Du kan exportera och hämta data genom att klicka på ikonen för excel-blad som visas bredvid **Gå**.

Rapporten uppdateras var femte minut.

![Överförda data rapporten](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Träffar (statuskoder)
Den här rapporten beskrivs distribution av begäran statuskoder för ditt innehåll. Varje förfrågan om innehållet genererar en HTTP-statuskod. Statuskoden beskriver hur edge POP hanteras begäran. Till exempel anger en 2xx statuskod att begäran behandlades har till en klient, medan en 4xx statuskod anger att ett fel inträffade. Mer information om HTTP-statuskoder finns [statuskoder](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

* Ange datumintervall för att visa data för dagens denna vecka/denna månad, etc. eller ange anpassade datum och klicka sedan på **Gå** att kontrollera ditt val har uppdaterats.
* Du kan exportera och ladda ned data genom att klicka på excel-blad finns bredvid **Gå**.

![Träffar rapport](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Status för cachelagring
Den här rapporten beskrivs distribution av träffar och cachemissar för klientbegäran. Eftersom bästa prestanda kommer från cacheträffar, kan du optimera data leverans hastigheter genom att minimera cachemissar och cacheträffar på har upphört att gälla. Du kan minska cachemissar genom att konfigurera ursprungsservern för att undvika att tilldela ”no-cache” svarshuvuden, undvika frågesträng cachelagring utom där det är absolut nödvändigt och undvika icke Cacheable ställs svarskoder. Du kan undvika utgångna cacheträffar genom att göra en tillgång max-ålder så länge som möjligt för att minimera antalet begäranden till den ursprungliga servern.

![Rapport för cache-status](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Huvud-cache statusvärden är:
* TCP_HIT: Hanteras från kant. Objektet har i cachen och inte har överskridit sin maximal ålder.
* TCP_MISS: Hanteras från ursprunget. Objektet var inte i cachen och svaret var tillbaka till ursprunget.
* TCP_EXPIRED _MISS: hanteras från ursprunget efter Omverifiering med ursprung. Objektet är i cacheminnet men har överskridit sin maximal ålder. En validering med ursprung resulterade i cacheobjektet som ersätts av ett nytt svar från ursprunget.
* TCP_EXPIRED _HIT: hanteras från kant efter Omverifiering med ursprung. Objektet är i cacheminnet men har överskridit sin maximal ålder. En validering med den ursprungliga servern resulterade i cacheobjektet som ska ändras.
* Ange datumintervall för att visa data för dagens denna vecka/denna månad, etc. eller ange anpassade datum och klicka sedan på **Gå** att kontrollera ditt val har uppdaterats.
* Du kan exportera och hämta data genom att klicka på ikonen för excel-blad som visas bredvid **Gå**.

### <a name="full-list-of-cache-statuses"></a>Fullständig lista över cache-status
* TCP_HIT – denna status rapporteras när en begäran hämtas direkt från POP till klienten. En tillgång hanteras direkt från en POP när cachelagras på POP närmast klienten och har en giltig time to live (TTL). TTL bestäms av följande svarshuvuden:
  
  * Cache-Control: s-maxage
  * Cache-Control: maximal ålder för
  * Upphör att gälla
* TCP_MISS – denna status anger att en cachelagrad version av den begärda tillgången inte hittades på POP närmast klienten. Tillgången begärs från en ursprungsserver eller en shield ursprungsservern. Om den ursprungliga servern eller shield ursprungsservern returnerar en tillgång, är det skickas till klienten och cachelagras på både klienten och gränsservern. Annars statuskod-200 (till exempel 403 inte hitta otillåtna eller 404) returneras.
* TCP_EXPIRED _HIT – denna status rapporteras när en begäran som riktar sig till en tillgång med en utgångna TTL behandlades direkt från POP till klienten. Till exempel när tillgången är maximal ålder har gått ut. 
  
    En begäran om har upphört att gälla som vanligtvis resulterar i en begäran om förnyelse till den ursprungliga servern. För en TCP_EXPIRED _HIT ska ske, måste den ursprungliga servern ange att en nyare version av tillgången inte finns. Den här situationen ger vanligtvis en uppdatering av tillgångens Cache-Control och Expires-huvuden.
* TCP_EXPIRED _MISS – denna status rapporteras när en nyare version av en utgången cachelagrade tillgång hanteras från POP till klienten. Den här statusen visas när TTL-värdet för den cachelagra resursen har upphört att gälla (till exempel har upphört att gälla maximal ålder) och den ursprungliga servern returnerar en nyare version av tillgången. Den här nya versionen av tillgången levereras till klienten i stället för den cachelagrade versionen. Dessutom cachelagras på gränsservern och klienten.
* CONFIG_NOCACHE – denna status anger att en kundspecifik konfiguration på vår edge POP hindras tillgången från att cachelagras.
* Inget – denna status anger att en kontroll av innehåll dokumentens inte utfördes.
* TCP_ CLIENT_REFRESH _MISS – denna status rapporteras när HTTP-klienter, till exempel en webbläsare, tvingar en kant POP för att hämta en ny version av en inaktuella tillgång från den ursprungliga servern.
  
    Som standard förhindra våra servrar att en HTTP-klienten tvingar våra servrar för att hämta en ny version av tillgången från den ursprungliga servern.
* TCP_ PARTIAL_HIT – denna status rapporteras när en byteintervallbegäran resulterar i en träff för en delvis cachelagrade tillgång. Det begärda byte-intervallet hanteras direkt från POP till klienten.
* UNCACHEABLE – denna status rapporteras när en tillgång Cache-Control och Expires-huvuden anger att det inte ska cachelagras på en POP eller genom att HTTP-klienten. Dessa typer av begäranden hanteras från den ursprungliga servern.

## <a name="cache-hit-ratio"></a>Träffgrad för cache
Den här rapporten visar procentandelen av cachelagrade begäranden som har hanteras direkt från cachen.

Den här rapporten innehåller följande information:

* Det begärda innehållet cachelagrades på närmast beställaren POP.
* Begäran behandlades direkt från i utkanten av nätverket.
* Begäran kräver inte Omverifiering med den ursprungliga servern.

Rapporten innehåller:

* Begäranden som nekats på grund av land filtreringsalternativ.
* Begäranden om tillgångar vars huvuden tyda på att de inte ska cachelagras. Till exempel Cache-Control: privat Cache-Control: no-cache eller Pragma: no-cache-huvuden som förhindrar att en tillgång cachelagras.
* Byte range-begäran för delvis cachelagrat innehåll.

Formeln är: (TCP_ träffar / (TCP_ träffar + TCP_MISS)) * 100

* Ange datumintervall för att visa data för dagens denna vecka/denna månad, etc. eller ange anpassade datum och klicka sedan på **Gå** att kontrollera ditt val har uppdaterats.
* Du kan exportera och hämta data genom att klicka på ikonen för excel-blad som visas bredvid **Gå**.

![Antal träffar i rapport](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPv4/IPv6-Data som överförs
Den här rapporten visar trafikfördelning för användning i IPV4 eller IPV6.

![IPv4/IPv6-Data som överförs](./media/cdn-reports/cdn-ipv4-ipv6.png)

* Ange datumintervall för att visa data för dagens denna vecka/denna månad, etc., eller ange anpassade datum.
* Klicka på **Gå** att kontrollera ditt val har uppdaterats.

## <a name="considerations"></a>Överväganden
Rapporter kan endast genereras under de senaste 18 månaderna.

