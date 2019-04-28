---
title: Core-rapporter från Verizon | Microsoft Docs
description: 'Du kan visa användningsmönster i CDN-nätverket med hjälp av följande rapporter: Bandbredd, överförda Data, träffar, cachelagring, träffrekvensen för cacheminnet, IPV4/IPV6-Data som överförs.'
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
ms.openlocfilehash: 6eb0fe592196466f7f49c21ce38afdf13b254d86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061586"
---
# <a name="core-reports-from-verizon"></a>Core-rapporter från Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Genom att använda Verizon Core-rapporter via portalen hantera för Verizon profiler kan visa du användningsmönster i CDN-nätverket med följande rapporter:

* Bandbredd
* Data som överförs
* Träffar
* Status för cachelagring
* Träffrekvensen för cacheminnet
* IPv4/IPV6-Data som överförs

## <a name="accessing-verizon-core-reports"></a>Åtkomst till Verizon Core-rapporter
1. Från bladet för CDN-profil klickar du på den **hantera** knappen.
   
    ![Knappen för CDN-profil hantera](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och sedan hovra över den **Core-rapporter** utfällbar meny. Klicka på en rapport i menyn.
   
    ![CDN-hanteringsportalen - Core-rapporter-menyn](./media/cdn-reports/cdn-core-reports.png)

3. För varje rapport väljer du ett datumintervall från den **datumintervall** lista. Du kan antingen välja ett fördefinierat datumintervall, till exempel **idag** eller **veckans**, eller så kan du välja **anpassade** och manuellt anger ett datumintervall genom att klicka på kalenderikonerna. 

4. När du har valt ett datumintervall, klickar du på **Gå** att generera rapporten. 

4. Om du vill exportera data i Excel-format, klickar du på ikonen Excel den **Gå** knappen.

## <a name="bandwidth"></a>Bandbredd
Rapporten bandbredd består av ett diagram och en datatabell som anger CDN-bandbreddsanvändning för HTTP och HTTPS under en viss tidsperiod, i Mbit/s. Du kan visa bandbreddsanvändningen över alla POP-servrar eller för en viss POP. Den här rapporten kan du visa toppar i trafiken och distribution för POP.

Från den **Kantnoder** väljer **alla Kantnoder** att se trafik från alla noder eller välja en specifik region.

Rapporten uppdateras var femte minut.

![Bandbredd-rapport](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Data som överförs
Den här rapporten består av ett diagram och en datatabell som anger hur CDN trafik för HTTP och HTTPS under en viss tidsperiod, i GB. Du kan visa hur trafik över alla POP-servrar eller för en viss POP. Den här rapporten kan du visa toppar i trafiken och distribution över POP.

Från den **Kantnoder** väljer **alla Kantnoder** att se trafik från alla noder eller välja en specifik region.

Rapporten uppdateras var femte minut.

![Överförda data rapporten](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Träffar (statuskoder)
Den här rapporten beskriver distributionen av begäran statuskoder för ditt innehåll. Alla förfrågningar om innehåll genererar en HTTP-statuskod. Statuskoden beskriver hur avgränsade POP-servrar hanteras begäran. Statuskoden 2xx anger till exempel att begäran behandlades har till en klient, medan en 4xx-statuskod anger att ett fel uppstod. Mer information om HTTP-statuskoder finns i [lista över HTTP-statuskoder](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Träffar rapport](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Status för cachelagring
Den här rapporten beskriver distributionen av cacheträffar och cachemissar för klientbegäranden. Eftersom bästa prestanda beror på cacheträffar, kan du optimera hastighet för leverans av data genom att minimera cachemissar och har upphört att gälla cacheträffar. 

För att minska cachemissar kan du konfigurera ursprungsservern för att minska användningen av följande: 
 * `no-cache` Svarshuvuden
 * Frågesträng cachelagring, såvida inte strikt behövs  
 * Icke webbtillämpningar svarskoder

Ange en tillgång för att minska har upphört att gälla cacheträffar `max-age` till en lång tid att minimera antalet begäranden till den ursprungliga servern.

![Cache statusar rapport](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Viktigaste för cachelagring är:
* TCP_HIT: Hanteras från edge-servern. Objektet har i cachen och inte har överskridit sin maxåldern.
* TCP_MISS: Hanteras från ursprungsservern. Objektet var inte i cacheminnet och svaret tillbaka till ursprunget.
* TCP_EXPIRED _MISS: Hanteras från ursprungsservern efter omverifieringen med ursprung. Objektet har i cacheminnet, men har överskridit sin maxåldern. En omverifieringen med ursprung resulterade i cacheobjektet ersättas med ett nytt svar från ursprunget.
* TCP_EXPIRED _HIT: Hanteras från Edge efter omverifieringen med ursprung. Objektet är i cacheminnet, men har överskridit sin maxåldern. En omverifieringen med den ursprungliga servern resulterade i cacheobjektet som ska ändras.

### <a name="full-list-of-cache-statuses"></a>Fullständig lista över för cachelagring
* TCP_HIT – denna status rapporteras när en begäran hanteras direkt från POP till klienten. En tillgång hanteras direkt från en POP när den cachelagras på POP närmast klienten och har en giltig time to live (TTL). TTL bestäms av de följande svarshuvuden:
  
  * Cache-Control: s-maxage
  * Cache-Control: max-age
  * Går ut
* TCP_MISS: Denna status anger att en cachelagrad version av den begärda tillgången inte hittades på den POP-plats som är närmast klienten. Tillgången har begärts från en ursprungsserver eller en shield ursprungsservern. Om den ursprungliga servern eller shield ursprungsservern returnerar du en tillgång, den hanteras av klienten och cachelagras på både klienten och edge-servern. I annat fall en icke-200-statuskod (till exempel 403 förbjudet eller 404 hittades inte) returneras.
* TCP_EXPIRED_HIT: Denna status rapporteras när en begäran som riktar sig mot en tillgång med en har upphört att gälla TTL behandlades direkt från POP till klienten. Till exempel när tillgången är maxåldern har gått ut. 
  
   En begäran om har upphört att gälla som vanligtvis resulterar i en begäran om förnyelse till den ursprungliga servern. TCP_EXPIRED_HIT status ska ske, måste den ursprungliga servern indikerar att det inte finns en nyare version av tillgången. Den här situationen uppstår vanligen i en uppdatering av tillgångens Cache-Control och Expires-huvuden.
* TCP_EXPIRED_MISS: Denna status rapporteras när en nyare version av en har upphört att gälla cachelagrade tillgång har hämtats från POP till klienten. Den här statusen visas när TTL-värdet för ett cachelagrat tillgången har upphört att gälla (till exempel har gått ut max-age) och ursprungsservern returnerar en nyare version av tillgången. Den nya versionen av tillgången levereras till klienten i stället för den cachelagrade versionen. Dessutom cachelagras på edge-servern och klienten.
* CONFIG_NOCACHE: Denna status anger att en kund konfiguration edge POP förhindrade tillgången att cachelagras.
* Ingen - denna status anger att en cacheminnet content färskhet kontroll inte har utförts.
* TCP_CLIENT_REFRESH_MISS: Denna status rapporteras när en HTTP-klient, till exempel en webbläsare, tvingar en kant POP för att hämta en ny version av en inaktuella tillgång från den ursprungliga servern. Som standard förhindra servrarna att en HTTP-klient framtvinga edge-servrar för att hämta en ny version av tillgången från den ursprungliga servern.
* TCP_PARTIAL_HIT: Denna status rapporteras när en byte-Intervallbegäran resulterar i en träff för en delvis cachelagrade tillgång. Det begärda byte-intervallet är omedelbart hanteras från POP till klienten.
* UNCACHEABLE: Denna status rapporteras när en tillgång `Cache-Control` och `Expires` rubriker indikerar att det inte ska cachelagras på en POP eller av HTTP-klienten. Dessa typer av begäranden hanteras från den ursprungliga servern.

## <a name="cache-hit-ratio"></a>Träffrekvensen för cacheminnet
Den här rapporten visar procentandelen av cachelagrade begäranden som har hanteras direkt från cachen.

Rapporten innehåller följande information:

* Det begärda innehållet har cachelagrats på närmast den som begär POP.
* Begäran har behandlats direkt från gränsen på vårt nätverk.
* Begäran kräver inte omverifieringen med den ursprungliga servern.

Rapporten innehåller inte:

* Begäranden som nekats på grund av land filtreringsalternativ.
* Begäranden för tillgångar vars rubriker tyda på att de inte ska cachelagras. Till exempel `Cache-Control: private`, `Cache-Control: no-cache`, eller `Pragma: no-cache` rubriker förhindra att en tillgång att cachelagras.
* Begäranden med byte-intervall för delvis cachelagrat innehåll.

Formeln är: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

![Cacheträff rapport](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPv4/IPV6-Data som överförs
Den här rapporten visar trafikfördelning för användning i IPV4 eller IPV6.

![IPv4/IPV6-Data som överförs](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Överväganden
Rapporter kan bara aktiveras under de senaste 18 månaderna.

