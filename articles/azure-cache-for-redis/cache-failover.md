---
title: Redundans och korrigering – Azure cache för Redis | Microsoft Docs
description: Läs om redundans, uppdatering och uppdaterings processen för Azure cache för Redis.
services: cache
author: asasine
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 305511efe86d2b241ef5014d9c3f0501cfd3fbdc
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675907"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Redundans och korrigering för Azure cache för Redis

Det är viktigt att förstå vad en redundansväxling är i sammanhanget med Azure cache för Redis-tjänsten för att skapa elastiska och lyckade klient program. En vanlig orsak till att en cache-redundans kommer från hanterings tjänsten med korrigering av Redis-binärfiler. Den här artikeln beskriver vad en redundansväxling är, hur de sker under korrigeringen och hur du skapar ett elastiskt klient program.

## <a name="what-is-a-failover"></a>Vad är en redundansväxling?

### <a name="a-quick-summary-of-our-architecture"></a>En snabb översikt över vår arkitektur

En cache är konstruerad av flera virtuella datorer med separata privata IP-adresser. Varje virtuell dator, som även kallas nod, är ansluten till en delad belastningsutjämnare med en enda virtuell IP-adress. Varje nod kör Redis-serverns process och kan nås via värd namnet och Redis-portarna. Varje nod betraktas antingen som en Master-eller Replica-nod. När ett klient program ansluter till ett cacheminne går dess trafik genom belastningsutjämnaren och dirigeras automatiskt till huvudnoden.

I en grundläggande cache är den enda noden alltid en huvud server. I en standard-eller Premium-cache finns det två noder där en har valt huvud repliken och den andra är repliken. Eftersom standard-och Premium-cachen har flera noder, kan en nod vara otillgänglig medan den andra fortsätter att bearbeta begär Anden. Klustrade cacheminnen består av många Shards, var och en med distinkta huvud-och replik-noder. En Shard kan vara nere medan de andra är tillgängliga.

> [!NOTE]
> En grundläggande cache har inte flera noder och erbjuder inte något service nivå avtal för tillgänglighet. Grundläggande cacheminnen rekommenderas endast för utvecklings-och testnings ändamål. Använd en standard-eller Premium-cache för en distribution med flera noder för att öka tillgängligheten.

### <a name="a-failover-explained"></a>En klar redundansväxling

En redundansväxling inträffar när en nod i noden blir en huvud nod och den gamla huvudnoden stänger befintliga anslutningar. När huvudnoden kommer att säkerhets kopie ras, kommer den att Observera att rollerna ändras och degradera sig själva för att bli en replik. Den ansluter sedan till den nya huvud servern och synkroniserar data. En redundansväxling kan vara planerad eller oplanerad.

En planerad redundansväxling sker under system uppdateringar som Redis korrigering eller operativ Systems uppgraderingar och hanterings åtgärder som skalning och omstart. Eftersom noderna får ett avancerat meddelande om uppdateringen, kan de enkelt växla roller och snabbt uppdatera belastningsutjämnaren för ändringen. En planerad redundansväxling ska slutföras på mindre än en sekund.

En oplanerad redundansväxling kan inträffa på grund av maskin varu fel, nätverks fel eller andra oväntade avbrott till huvudnoden. Noden replikering kommer att befordra sig själv till Master, men processen tar längre tid. En nod för replikering måste först identifiera huvudnoden är inte tillgänglig innan redundansväxlingen kan initieras. Replik-noden måste också kontrol lera att oplanerat fel inte är tillfälligt eller lokalt för att undvika en overeager-redundansväxling. Den här fördröjningen innebär att en oplanerad redundansväxling vanligt vis är inom 10 till 15 sekunder.

## <a name="how-does-patching-occur"></a>Hur sker uppdatering?

Azure cache för Redis-tjänsten har regelbundet underhåll för att uppdatera din cache med de senaste plattforms funktionerna och korrigeringarna. För att korrigera en cache följer tjänsten följande steg:

1. Hanterings tjänsten väljer en nod som ska korrigeras.
1. Om den valda noden är en huvud-nod, marknadsförs dess rotnod. Den här kampanjen betraktas som planerad redundansväxling.
1. Den valda noden startas om för att ta de nya ändringarna och kommer att säkerhets kopie ras som en Replica-nod. Replik-noder ansluter till huvudnoden och synkroniserar data.
1. När datasynkroniseringen är klar upprepas korrigerings processen för de återstående noderna.

Eftersom korrigeringen är en planerad redundansväxling, höjer noden replikering snabbt sig själv för att bli Master och påbörjar behandlings begär Anden och nya anslutningar. Basic-cacheminnen har ingen nod för replikering och är inte tillgängliga förrän uppdateringen är klar. Varje Shard i en klustrad cache korrigeras separat och stänger inte anslutningar till en annan Shard.

> [!IMPORTANT]
> Noderna korrigeras en i taget för att förhindra data förlust. Grundläggande cacheminnen kommer att ha data förlust. Klustrade cacheminnen korrigeras en Shard i taget.

Flera cacheminnen i samma resurs grupp och region har också uppdaterats en i taget.  Cacheminnen som finns i olika resurs grupper eller olika regioner kan återkallas samtidigt.

Eftersom fullständig datasynkronisering sker innan processen upprepas, är det osannolikt att data förlust inträffar när du använder en standard-eller Premium-cache. Du kan skydda mot data förlust genom att [Exportera](cache-how-to-import-export-data.md#export) data och aktivera [persistence](cache-how-to-premium-persistence.md).

### <a name="additional-cache-load"></a>Ytterligare cache-belastning

När en redundansväxling inträffar måste standard-och Premium-cachen replikera data från en nod till en annan. Den här replikeringen orsakar en belastnings ökning i både server minne och CPU. Om cache-instansen redan är hårt inläst kan klient programmen uppleva en ökad fördröjning. I extrema fall kan klient program ta emot tids gräns undantag. [Konfigurera](cache-configure.md#memory-policies) cachens `maxmemory-reserved` inställning för att minimera effekten av den här extra belastningen.

## <a name="how-does-a-failover-impact-my-client-application"></a>Hur påverkar en växling vid fel mitt klient program?

Antalet fel som visas av klient programmet beror på hur många åtgärder som var beroende av anslutningen vid redundansväxlingen. Alla anslutningar som dirigeras via noden som stängde anslutningar kommer att se fel. Många klient bibliotek kan utlösa olika typer av fel, inklusive tids gräns undantag, anslutnings undantag eller socket-undantag när anslutningar bryts. Antalet och typen av undantag beror på var i kod Sök vägen som begäran kommer när cachen stänger dess anslutningar. Till exempel kan en åtgärd som skickar en begäran men inte har tagit emot ett svar när redundansväxlingen uppstår kan få ett timeout-undantag. Nya begär Anden för det stängda anslutningsobjektet får anslutnings undantag tills åter anslutningen har genomförts.

De flesta klient bibliotek försöker återansluta till cacheminnet om det är konfigurerat för att göra det, men oförutsedda buggar kan ibland placera biblioteks objekt i ett oåterkalleligt tillstånd. Om felet kvarstår under längre tid än en förkonfigurerad tids period ska anslutningsobjektet återskapas. I .NET och andra objektorienterade språk kan du återskapa anslutningen utan att starta om programmet med [ett Lazy \<T \>-mönster](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="what-should-i-do-in-my-application"></a>Vad ska jag göra i mitt program?

Eftersom en redundansväxling inte kan undvikas fullständigt bör klient program vara skrivna för återhämtning till anslutnings avbrott och misslyckade förfrågningar. Trots att de flesta klient bibliotek automatiskt återansluter till cache-slutpunkten försöker få klient bibliotek försöka att försöka utföra misslyckade förfrågningar igen. Beroende på program scenariot kan det vara en bra idé att använda logiken igen.

Om du vill testa ett klient programs återhämtning använder du en [omstart](cache-administration.md#reboot) som en manuell utlösare för anslutnings avbrott. Dessutom rekommenderar vi att du [schemalägger uppdateringar](cache-administration.md#schedule-updates) i en cache för att se till att hanterings tjänsten har Redis runtime-korrigeringsfiler som gäller under angivna vecko Visa fönster. Dessa fönster väljs vanligt vis för perioder när klient program trafiken är lägre för att undvika potentiella incidenter.

### <a name="client-network-configuration-changes"></a>Konfigurations ändringar för klient nätverk

Vissa nätverks konfigurations ändringar på klient sidan kan utlösa fel på "ingen anslutning är tillgänglig".  Om du byter ut klient programmets virtuella IP-adress mellan mellanlagrings-och produktions platser eller skalar du storlek/antal instanser av programmet kan det orsaka ett anslutnings problem som senast är mindre än en minut. Klient programmet kommer sannolikt att förlora anslutning till andra externa nätverks resurser utöver Redis.

## <a name="next-steps"></a>Nästa steg

- [Schemalägg uppdateringar](cache-administration.md#schedule-updates) för cacheminnet.
- Testa program återhämtning med en [omstart](cache-administration.md#reboot).
- [Konfigurera](cache-configure.md#memory-policies) minnes reservationer och-principer.
