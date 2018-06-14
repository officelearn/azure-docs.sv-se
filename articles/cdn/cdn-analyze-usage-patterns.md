---
title: Core rapporter från Verizon | Microsoft Docs
description: 'Du kan visa användningsmönster för din CDN med hjälp av följande rapporter: bandbredd, överförda Data, träffar, Cache status, Cache träffar förhållandet, IPV4/IPv6-Data överförs.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: fa828bfa736d677fb4881e5cc2628c0e03eb8749
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
ms.locfileid: "26345255"
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
   
    ![CDN-profilen hantera knappen](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och klicka sedan hovra över den **Core rapporter** utfällbar. Klicka på en rapport i menyn.
   
    ![CDN-hanteringsportalen - Core rapporter-menyn](./media/cdn-reports/cdn-core-reports.png)

3. För varje rapport väljer du ett datumintervall från den **datumintervall** lista. Du kan antingen välja ett fördefinierat datumintervall som **idag** eller **veckans**, eller så kan du välja **anpassade** och manuellt ange ett datumintervall genom att klicka på kalenderikonerna. 

4. När du har valt ett datumintervall, klickar du på **Gå** att generera rapporten. 

4. Om du vill exportera data i Excel-format, klickar du på ikonen Excel den **Gå** knappen.

## <a name="bandwidth"></a>Bandbredd
Rapporten bandbredd består av ett diagram och en datatabell som anger CDN bandbreddsanvändningen för HTTP och HTTPS under en viss tidsperiod i Mbit/s. Du kan visa bandbreddsanvändningen över alla POP eller för en viss POP. Den här rapporten kan du visa trafik toppar och distribution för POP.

Från den **kant noder** väljer **kant noderna** att se trafik från alla noder, eller välj en specifik region.

Rapporten uppdateras var femte minut.

![Bandbredd-rapport](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Data som överförs
Den här rapporten består av ett diagram och en datatabell som anger hur CDN-trafik för HTTP och HTTPS under en viss tidsperiod i GB. Du kan visa hur trafik över alla POP eller för en viss POP. Den här rapporten kan du visa trafik toppar och distribution över POP.

Från den **kant noder** väljer **kant noderna** att se trafik från alla noder, eller välj en specifik region.

Rapporten uppdateras var femte minut.

![Överförda data rapporten](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Träffar (statuskoder)
Den här rapporten beskrivs distribution av begäran statuskoder för ditt innehåll. Varje förfrågan om innehållet genererar en HTTP-statuskod. Statuskoden beskriver hur edge POP hanteras begäran. Till exempel anger en 2xx statuskod att begäran behandlades har till en klient, medan en 4xx statuskod anger att ett fel inträffade. Mer information om HTTP-statuskoder finns [listan över HTTP-statuskoder](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Träffar rapport](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Status för cachelagring
Den här rapporten beskrivs distribution av träffar och cachemissar för klientbegäranden. Eftersom bästa prestanda beror på cacheträffar, kan du optimera hastighet för leverans av data genom att minimera cachemissar och cacheträffar på har upphört att gälla. 

Om du vill minska cachemissar konfigurera ursprungsservern för att minska användningen av följande: 
 * `no-cache`svarsrubriker
 * Cachelagring av frågesträng, såvida inte absolut nödvändigt  
 * Icke-Cacheable ställs svarskoder

Ange en tillgång för att minska utgångna cacheträffar `max-age` till en längre tid att minimera antalet begäranden till den ursprungliga servern.

![Rapport för cache-status](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Huvud-cache statusvärden är:
* TCP_HIT: Hanteras från edge-server. Objektet har i cachen och inte har överskridit sin maximal ålder.
* TCP_MISS: Hanteras från ursprungsservern. Objektet var inte i cachen och svaret var tillbaka till ursprunget.
* TCP_EXPIRED _MISS: hanteras från ursprungsservern efter Omverifiering med ursprung. Objektet är i cacheminnet men har överskridit sin maximal ålder. En validering med ursprung resulterade i cacheobjektet som ersätts av ett nytt svar från ursprunget.
* TCP_EXPIRED _HIT: hanteras från kant efter Omverifiering med ursprung. Objektet är i cacheminnet men har överskridit sin maximal ålder. En validering med den ursprungliga servern resulterade i cacheobjektet som ska ändras.

### <a name="full-list-of-cache-statuses"></a>Fullständig lista över cache-status
* TCP_HIT – denna status rapporteras när en begäran hämtas direkt från POP till klienten. En tillgång hanteras direkt från en POP när cachelagras på POP närmast klienten och har en giltig time to live (TTL). TTL bestäms av följande svarshuvuden:
  
  * Cache-Control: s-maxage
  * Cache-Control: maximal ålder för
  * Förfaller
* TCP_MISS: Denna status anger att en cachelagrad version av den begärda tillgången inte hittades på POP närmast klienten. Tillgången begärs från en ursprungsserver eller en shield ursprungsservern. Om den ursprungliga servern eller shield ursprungsservern returnerar en tillgång, är det skickas till klienten och cachelagras på både klienten och gränsservern. Annars statuskod-200 (till exempel 403 inte hitta otillåtna eller 404) returneras.
* TCP_EXPIRED_HIT: Denna status rapporteras när en begäran som riktar sig till en tillgång med en utgångna TTL behandlades direkt från POP till klienten. Till exempel när tillgången är maximal ålder har gått ut. 
  
   En begäran om har upphört att gälla som vanligtvis resulterar i en begäran om förnyelse till den ursprungliga servern. För en TCP_EXPIRED_HIT status ska ske, måste den ursprungliga servern ange att en nyare version av tillgången inte finns. Den här situationen ger vanligtvis en uppdatering av tillgångens Cache-Control och Expires-huvuden.
* TCP_EXPIRED_MISS: Denna status rapporteras när en nyare version av en utgången cachelagrade tillgång hanteras från POP till klienten. Den här statusen visas när TTL-värdet för den cachelagra resursen har upphört att gälla (till exempel har upphört att gälla maximal ålder) och den ursprungliga servern returnerar en nyare version av tillgången. Den här nya versionen av tillgången levereras till klienten i stället för den cachelagrade versionen. Dessutom cachelagras på gränsservern och klienten.
* CONFIG_NOCACHE: Denna status anger att en kundspecifika konfiguration edge POP hindras tillgången från att cachelagras.
* Inget – denna status anger att en kontroll av innehåll dokumentens inte utfördes.
* TCP_CLIENT_REFRESH_MISS: Denna status rapporteras när HTTP-klienter, till exempel en webbläsare, tvingar en kant POP för att hämta en ny version av en inaktuella tillgång från den ursprungliga servern. Som standard förhindra servrarna att en HTTP-klient att tvinga servrarna för att hämta en ny version av tillgången från den ursprungliga servern.
* TCP_PARTIAL_HIT: Denna status rapporteras när en byteintervallbegäran resulterar i en träff för en delvis cachelagrade tillgång. Det begärda byte-intervallet hanteras direkt från POP till klienten.
* UNCACHEABLE: Denna status rapporteras när en tillgång `Cache-Control` och `Expires` indikerar att det inte ska cachelagras på en POP eller av klienten för HTTP-rubriker. Dessa typer av begäranden hanteras från den ursprungliga servern.

## <a name="cache-hit-ratio"></a>Träffgrad för cache
Den här rapporten visar procentandelen av cachelagrade begäranden som har hanteras direkt från cachen.

Den här rapporten innehåller följande information:

* Det begärda innehållet cachelagrades på närmast beställaren POP.
* Begäran behandlades direkt från i utkanten av nätverket.
* Begäran kräver inte Omverifiering med den ursprungliga servern.

Rapporten innehåller:

* Begäranden som nekats på grund av land filtreringsalternativ.
* Begäranden om tillgångar vars huvuden tyda på att de inte ska cachelagras. Till exempel `Cache-Control: private`, `Cache-Control: no-cache`, eller `Pragma: no-cache` huvuden förhindra en tillgång från att cachelagras.
* Byte range-begäran för delvis cachelagrat innehåll.

Formeln är: (TCP_ träffar / (TCP_ träffar + TCP_MISS)) * 100

![Antal träffar i rapport](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPv4/IPv6-Data som överförs
Den här rapporten visar trafikfördelning för användning i IPV4 eller IPV6.

![IPv4/IPv6-Data som överförs](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Överväganden
Rapporter kan endast genereras under de senaste 18 månaderna.

