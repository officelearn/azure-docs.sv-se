---
title: Redundans och korrigering – Azure Cache for Redis
description: Lär dig mer om redundans, korrigering och uppdateringsprocessen för Azure Cache för Redis.
author: asasine
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 6ff33bd594181aabc4fd7d55ce33f780a0d06086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122200"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Redundans och korrigering för Azure Cache för Redis

För att skapa motståndskraftiga och framgångsrika klientprogram är det viktigt att förstå redundans i kontexten för Azure Cache for Redis-tjänsten. En redundans kan vara en del av planerade hanteringsåtgärder eller orsakas av oplanerade maskinvaru- eller nätverksfel. En vanlig användning av cache redundans kommer när hanteringstjänsten korrigeringar Azure Cache för Redis binärfiler. Den här artikeln beskriver vad en redundans är, hur den inträffar under korrigering och hur du skapar ett flexibelt klientprogram.

## <a name="what-is-a-failover"></a>Vad är en redundans?

Låt oss börja med en översikt över redundans för Azure Cache för Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>En snabb sammanfattning av cachearkitektur

En cache är konstruerad av flera virtuella datorer med separata, privata IP-adresser. Varje virtuell dator, även känd som en nod, är ansluten till en delad belastningsutjämnare med en enda virtuell IP-adress. Varje nod kör Redis-serverprocessen och är tillgänglig med hjälp av värdnamnet och Redis-portarna. Varje nod betraktas som antingen en huvud- eller repliknod. När ett klientprogram ansluter till en cache går dess trafik genom den här belastningsutjämnaren och dirigeras automatiskt till huvudnoden.

I en grundläggande cache är den enda noden alltid en mallsida. I en Standard- eller Premium-cache finns det två noder: en väljs som huvudsida och den andra är repliken. Eftersom Standard- och Premium-cacheminnen har flera noder kan en nod vara otillgänglig medan den andra fortsätter att bearbeta begäranden. Klustrade cacheminnen består av många shards, var och en med distinkta huvud- och repliknoder. En shard kan vara nere medan de andra förblir tillgängliga.

> [!NOTE]
> En grundläggande cache har inte flera noder och erbjuder inte ett servicenivåavtal (SLA) för dess tillgänglighet. Grundläggande cacheminnen rekommenderas endast för utvecklings- och testningsändamål. Använd en Standard- eller Premium-cache för en multinoddistribution för att öka tillgängligheten.

### <a name="explanation-of-a-failover"></a>Förklaring av en redundans

En redundans uppstår när en repliknod befordras till en huvudnod och den gamla huvudnoden stänger befintliga anslutningar. När huvudnoden har säkerhetskopierts märker den ändringen i roller och nedgraderar sig själv till att bli en replik. Den ansluter sedan till den nya bakgrunden och synkroniserar data. En redundans kan planeras eller oplaneras.

En *planerad redundans* sker under systemuppdateringar, till exempel Redis-korrigering eller OS-uppgraderingar, och hanteringsåtgärder, till exempel skalning och omstart. Eftersom noderna får förhandsmeddelande om uppdateringen kan de kooperativt byta roller och snabbt uppdatera belastningsutjämnaren för ändringen. En planerad redundans avslutas vanligtvis på mindre än 1 sekund.

En *oplanerad redundans* kan inträffa på grund av maskinvarufel, nätverksfel eller andra oväntade avbrott i huvudnoden. Repliknoden befordrar sig till master, men processen tar längre tid. En repliknod måste först identifiera att huvudnoden inte är tillgänglig innan redundansprocessen kan initieras. Repliknoden måste också kontrollera att det här oplanerade felet inte är tillfälligt eller lokalt för att undvika en onödig redundans. Den här fördröjningen i identifiering innebär att en oplanerad redundans vanligtvis avslutas inom 10 till 15 sekunder.

## <a name="how-does-patching-occur"></a>Hur uppstår korrigering?

Azure Cache för Redis-tjänsten uppdaterar regelbundet cacheminnet med de senaste plattformsfunktionerna och korrigeringarna. Om du vill korrigera en cache följer tjänsten följande steg:

1. Hanteringstjänsten väljer en nod som ska korrigeras.
1. Om den valda noden är en huvudnod befordras motsvarande repliknod kooperativt. Denna kampanj anses vara en planerad redundans.
1. Den valda noden startas om för att ta de nya ändringarna och kommer tillbaka upp som en repliknod.
1. Repliknoden ansluter till huvudnoden och synkroniserar data.
1. När datasynkroniseringen är klar upprepas korrigeringsprocessen för de återstående noderna.

Eftersom korrigering är en planerad redundans befordras sig repliknoden snabbt till att bli en huvudsida och börjar underhålla begäranden och nya anslutningar. Grundläggande cacheminnen har ingen repliknod och är inte tillgängliga förrän uppdateringen är klar. Varje fragment i en klustrad cache korrigeras separat och stänger inte anslutningar till en annan fragment.

> [!IMPORTANT]
> Noder korrigeras en i taget för att förhindra dataförlust. Grundläggande cacheminnen kommer att ha dataförlust. Klustrade cacheminnen korrigeras en shard i taget.

Flera cacheminnen i samma resursgrupp och region är också korrigerade en i taget.  Cacheminnen som finns i olika resursgrupper eller olika regioner kan korrigeras samtidigt.

Eftersom fullständig datasynkronisering sker innan processen upprepas är det osannolikt att data går förlorade när du använder en Standard- eller Premium-cache. Du kan ytterligare skydda dig mot dataförlust genom [att exportera](cache-how-to-import-export-data.md#export) data och aktivera [persistens](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Ytterligare cachebelastning

När en redundans inträffar måste standard- och Premium-cacheminnena replikera data från en nod till en annan. Den här replikeringen orsakar en viss belastningsökning i både serverminne och CPU. Om cacheinstansen redan är kraftigt inläst kan klientprogram uppleva ökad svarstid. I extrema fall kan klientprogram få time out-undantag. [Konfigurera](cache-configure.md#memory-policies) `maxmemory-reserved` cachens inställning för att minska effekten av den här extra inläsningen.

## <a name="how-does-a-failover-affect-my-client-application"></a>Hur påverkar en redundans min klientprogram?

Antalet fel som visas av klientprogrammet beror på hur många åtgärder som väntar på den anslutningen vid tidpunkten för redundansen. Alla anslutningar som dirigeras genom noden som stängde anslutningarna ser fel. Många klientbibliotek kan skapa olika typer av fel när anslutningar bryts, inklusive time-out-undantag, anslutningsundantag eller socketundantag. Antalet och typen av undantag beror på var i kodsökvägen begäran är när cachen stänger sina anslutningar. En åtgärd som skickar en begäran men inte har fått något svar när redundansen inträffar kan till exempel få ett time-out-undantag. Nya begäranden på det stängda anslutningsobjektet får anslutningsundantag tills återanslutningen sker.

De flesta klientbibliotek försöker återansluta till cachen om de är konfigurerade för att göra det. Oförutsedda fel kan dock ibland placera biblioteksobjekten i ett oåterkalleligt tillstånd. Om fel kvarstår längre än en förkonfigurerad tid ska anslutningsobjektet återskapas. I Microsoft.NET och andra objektorienterade språk kan återskapa anslutningen utan att starta om programmet åstadkommas med hjälp av [ett Lazy\<T-mönster\> ](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Hur gör jag mitt program flexibelt?

Eftersom du inte kan undvika redundans helt skriver du klientprogrammen för återhämtning vid anslutningsavbrott och misslyckade begäranden. Även om de flesta klientbibliotek automatiskt återansluter till cacheslutpunkten, försöker få av dem att försöka igen misslyckade begäranden. Beroende på programscenariot kan det vara klokt att använda logik för återförsök med backoff.

Om du vill testa ett klientprograms återhämtning använder du en [omstart](cache-administration.md#reboot) som en manuell utlösare för anslutningsavbrott. Dessutom rekommenderar vi att du [schemalägger uppdateringar](cache-administration.md#schedule-updates) på en cache. Be hanteringstjänsten att använda Redis runtime-korrigeringar under angivna veckofönster. Dessa fönster är vanligtvis perioder när klientprogramtrafiken är låg, för att undvika potentiella incidenter.

### <a name="client-network-configuration-changes"></a>Ändringar av klientnätverkskonfiguration

Vissa nätverkskonfigurationsändringar på klientsidan kan utlösa fel i "Ingen anslutning tillgänglig". Sådana ändringar kan omfatta:

- Byta ett klientprograms virtuella IP-adress mellan mellanlagring och produktionsplatser.
- Skala storleken eller antalet instanser av ditt program.

Sådana ändringar kan orsaka ett anslutningsproblem som varar mindre än en minut. Klientprogrammet förlorar förmodligen anslutningen till andra externa nätverksresurser utöver Azure Cache for Redis-tjänsten.

## <a name="next-steps"></a>Nästa steg

- [Schemalägg uppdateringar](cache-administration.md#schedule-updates) för cacheminnet.
- Testa programmets återhämtning genom att använda en [omstart](cache-administration.md#reboot).
- [Konfigurera](cache-configure.md#memory-policies) minnesreservationer och principer.
