---
title: Hur du konfigurerar datapersistence för Premium Azure Redis-Cache
description: Lär dig hur du konfigurerar och hanterar data beständiga din Premium-nivån Azure Redis-Cache-instanser
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: wesmc
ms.openlocfilehash: 270158bbf85a58a48a367a091ad2b09a9d114b2b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910861"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Hur du konfigurerar datapersistence för Premium Azure Redis-Cache
Azure Redis-Cache har olika cache-erbjudanden som ger flexibilitet vid val av cachestorlek och funktioner, inklusive funktioner för Premium-nivån, till exempel klustring, beständiga och stöd för virtuella nätverk. Den här artikeln beskriver hur du konfigurerar persistence i en premium Azure Redis-Cache-instans.

Information om andra cache premium-funktioner finns [introduktion till Azure Redis Cache Premium-nivån](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Vad är beständiga data?
[Redis-persistence](https://redis.io/topics/persistence) kan du bevara data som lagras i Redis. Du kan också ta ögonblicksbilder och säkerhetskopiera data som du kan läsa in vid maskinvarufel. Det här är en stor fördel över Basic eller Standard nivå där alla data som lagras i minnet och det kan finnas en potentiell dataförlust om ett fel uppstår där cachenoder är nere. 

Azure Redis-Cache erbjuder Redis-datapersistence följande modeller:

* **RDB beständiga** -när RDB (Redis-databas) beständiga är konfigurerad, Azure Redis-Cache kvarstår en ögonblicksbild av Redis-cache i en Redis binärformat till disk baserat på en konfigurerbar säkerhetskopieringsfrekvens. Om ett allvarligt fel inträffar som inaktiverar både den primära servern och repliken cache har cacheminnet byggts med den senaste ögonblicksbilden. Lär dig mer om den [fördelar](https://redis.io/topics/persistence#rdb-advantages) och [nackdelar](https://redis.io/topics/persistence#rdb-disadvantages) av RDB persistence.
* **AOF beständiga** -beständiga när AOF (Lägg till fil) har konfigurerats, Azure Redis-Cache sparar varje skrivåtgärd till en logg som har sparats minst en gång per sekund på ett Azure Storage-konto. Om ett allvarligt fel inträffar som inaktiverar både den primära servern och repliken cache har cacheminnet byggts med hjälp av de lagrade skrivåtgärder. Lär dig mer om den [fördelar](https://redis.io/topics/persistence#aof-advantages) och [nackdelar](https://redis.io/topics/persistence#aof-disadvantages) av AOF beständighet.

Beständiga konfigureras från den **nytt Redis-Cache** bladet under Skapa cache och på den **resurs menyn** befintliga premium cachelagrar.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

När en premium-prisnivån är markerad, klickar du på **Redis-persistence**.

![Redis-persistence][redis-cache-persistence]

Stegen i nästa avsnitt beskrivs hur du konfigurerar Redis-persistence på din nya premium-cachen. När Redis-persistence har konfigurerats klickar du på **skapa** att skapa ditt nya cacheminne premium med Redis-persistence.

## <a name="enable-redis-persistence"></a>Aktivera Redis-persistence

Redis-persistence har aktiverats på den **Redis-datapersistence** bladet genom att välja antingen **RDB** eller **AOF** beständighet. För nya cacheminnen används det här bladet under skapandeprocessen cache enligt beskrivningen i föregående avsnitt. För befintliga cacheminnen på **Redis-datapersistence** bladet kan nås från den **resurs menyn** för ditt cacheminne.

![Redis-inställningar][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurera RDB persistence

Om du vill aktivera RDB beständiga **RDB**. Om du vill inaktivera RDB beständiga på tidigare aktiverade premium cache **inaktiverade**.

![Redis-persistence RDB][redis-cache-rdb-persistence]

Om du vill konfigurera säkerhetskopieringsintervallet, Välj en **säkerhetskopieringsfrekvens** från den nedrullningsbara listan. Alternativen är **15 minuter**, **30 minuter**, **60 minuter**, **6 timmar**, **12 timmar**, och **24 timmar**. Det här intervallet börjar räkna när den föregående åtgärden har slutförts och när det ska gå en ny säkerhetskopia initieras.

Klicka på **Storage-konto** att välja lagringskontot för att använda och välj antingen den **primärnyckel** eller **sekundärnyckeln** ska användas från den **Lagringsnyckel** listrutan. Du måste välja ett lagringskonto i samma region som cache, och en **Premiumlagring** konto rekommenderas eftersom premium-lagring har högre kapacitet. 

> [!IMPORTANT]
> Om lagringsnyckeln för ditt konto beständiga genereras, måste du konfigurera om den önskade nyckeln från den **Lagringsnyckel** listrutan.
> 
> 

Klicka på **OK** att spara den beständiga konfigurationen.

Nästa säkerhetskopiering (eller första säkerhetskopieringen för nya cacheminnen) initieras när långa säkerhetskopieringsfrekvens intervall.

## <a name="configure-aof-persistence"></a>Konfigurera AOF persistence

Om du vill aktivera AOF beständiga **AOF**. Om du vill inaktivera AOF beständiga på tidigare aktiverade premium cache **inaktiverade**.

![Redis-persistence AOF][redis-cache-aof-persistence]

Om du vill konfigurera AOF beständiga, ange en **första Lagringskonto**. Det här lagringskontot måste vara i samma region som cache, och en **Premiumlagring** konto rekommenderas eftersom premium-lagring har högre kapacitet. Du kan också konfigurera en ytterligare lagringskontonamnet **andra Lagringskonto**. Om ett andra storage-konto har konfigurerats, skrivs skrivningar till cachen repliken till den här andra storage-konto. För varje konfigurerade storage-konto kan du välja antingen den **primärnyckel** eller **sekundärnyckeln** ska användas från den **Lagringsnyckel** listrutan. 

> [!IMPORTANT]
> Om lagringsnyckeln för ditt konto beständiga genereras, måste du konfigurera om den önskade nyckeln från den **Lagringsnyckel** listrutan.
> 
> 

När AOF persistence har aktiverats kan du skriva operations till cachen sparas i avsedda storage-konto (eller konton om du har konfigurerat ett andra storage-konto). Om ett oåterkalleligt fel som tar ned både den primära servern och repliken cache används lagrade AOF loggen för att bygga om cachen.

## <a name="persistence-faq"></a>Beständiga vanliga frågor och svar
I följande lista innehåller svar på vanliga frågor om Azure Redis-Cache-persistence.

* [Kan jag aktivera persistence på en tidigare skapad cache?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Kan jag aktivera AOF och RDB beständiga på samma gång?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Vilken beständiga modell ska jag välja?](#which-persistence-model-should-i-choose)
* [Vad händer om jag har skalas till en annan storlek och en säkerhetskopia återställs som togs före skalning igen?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB beständiga
* [Kan jag ändra frekvensen för säkerhetskopiering RDB när du har skapat cacheminnet?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Varför är det mer än 60 minuter mellan säkerhetskopieringar om jag har en RDB säkerhetskopieringsfrekvens 60 minuter?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Vad händer med de gamla RDB säkerhetskopiorna när en ny säkerhetskopia görs?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF beständiga
* [När bör jag använda ett andra storage-konto?](#when-should-i-use-a-second-storage-account)
* [Stöder AOF beständiga påverkar hela svarstid, prestanda, och min-cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hur tar jag bort andra lagringskontot?](#how-can-i-remove-the-second-storage-account)
* [Vad är en omarbetning och hur påverkar min cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Vad bör jag förvänta dig när skalning en cache med AOF aktiverad?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hur organiseras mina AOF data i lagring?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Kan jag aktivera persistence på en tidigare skapad cache?
Ja, Redis-persistence konfigureras skapa cache och på befintliga premium cacheminnen.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Kan jag aktivera AOF och RDB beständiga på samma gång?

Nej, du kan aktivera endast RDB eller AOF, men inte båda samtidigt.

### <a name="which-persistence-model-should-i-choose"></a>Vilken beständiga modell ska jag välja?

AOF beständiga sparar varje skrivning till en logg som har vissa inverkan på genomflöde jämfört med RDB beständiga som sparar säkerhetskopior baserat på konfigurerad säkerhetskopieringsintervallet med minimal inverkan på prestanda. Välj AOF beständiga om din primära målet är att minimera dataförlust och du kan hantera en minskning i dataflöde för ditt cacheminne. Välj RDB beständiga om du vill underhålla optimal genomströmning på ditt cacheminne men ändå vill en mekanism för att återställa data.

* Lär dig mer om den [fördelar](https://redis.io/topics/persistence#rdb-advantages) och [nackdelar](https://redis.io/topics/persistence#rdb-disadvantages) av RDB persistence.
* Lär dig mer om den [fördelar](https://redis.io/topics/persistence#aof-advantages) och [nackdelar](https://redis.io/topics/persistence#aof-disadvantages) av AOF beständighet.

Läs mer på prestanda när du använder AOF beständiga [har AOF beständiga påverkar hela svarstid, prestanda, och min-cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Vad händer om jag har skalas till en annan storlek och en säkerhetskopia återställs som togs före skalning igen?

För både RDB och AOF:

* Om du har skalats till en större storlek, påverkas inte.
* Om du har skalats till en mindre storlek, och du har en anpassad [databaser](cache-configure.md#databases) inställningar som är större än den [databaser gränsen](cache-configure.md#databases) för din nya storleken är inte återställa data i dessa databaser. Mer information finns i [är mina egna databaser anger berörda under skalning?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Om du har skalats till en mindre storlek, och det finns inte tillräckligt med utrymme i mindre storlek ska innehålla alla data från den senaste säkerhetskopieringen, nycklar kommer att avlägsnas under återställningsprocessen, vanligtvis med den [allkeys lru](http://redis.io/topics/lru-cache) av principen.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Kan jag ändra frekvensen för säkerhetskopiering RDB när du har skapat cacheminnet?
Ja, du kan ändra frekvensen för säkerhetskopiering för RDB på den **Redis-datapersistence** bladet. Instruktioner finns i [konfigurera Redis beständiga](#configure-redis-persistence).

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Varför är det mer än 60 minuter mellan säkerhetskopieringar om jag har en RDB säkerhetskopieringsfrekvens 60 minuter?
RDB beständiga säkerhetskopieringsfrekvens intervall startar inte förrän föregående säkerhetskopieringen har slutförts. Om frekvensen för säkerhetskopiering är 60 minuter och det tar en säkerhetskopieringsprocess 15 minuter att slutföra, startar inte nästa säkerhetskopiering till 75 minuter efter starttiden på säkerhetskopian som tidigare.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Vad händer med de gamla RDB säkerhetskopiorna när en ny säkerhetskopia görs?
Alla RDB beständiga säkerhetskopior utom det senaste tas bort automatiskt. Borttagningen kan inte sker omedelbart men gamla säkerhetskopior bevaras inte under obestämd tid.


### <a name="when-should-i-use-a-second-storage-account"></a>När bör jag använda ett andra storage-konto?

Du bör använda ett andra storage-konto för AOF när du tror att du har högre än förväntade uppsättningen åtgärder i cachen.  Konfigurera sekundära lagringskontot bidrar till ditt cacheminne inte nå lagringsgränser för bandbredd.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Stöder AOF beständiga påverkar hela svarstid, prestanda, och min-cache?

AOF beständiga påverkar genomströmning av ungefär 15% – 20% när cachen understiger största belastning (processor- och läsa in både under 90%). Det får inte vara problem med nätverkssvarstiden när cachen är inom de angivna gränserna. Dock uppnår cachen dessa gränser snabbare med AOF aktiverad.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hur tar jag bort andra lagringskontot?

Du kan ta bort AOF beständiga sekundära storage-konto genom att ange andra lagringskontot för att vara samma som det första storage-kontot. Instruktioner finns i [konfigurera AOF beständiga](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Vad är en omarbetning och hur påverkar min cache?

När filen AOF blir tillräckligt stor i kö på en omarbetning automatiskt i cachen. Omarbetningen storlek AOF-fil med en mindre uppsättning åtgärder som behövs för att skapa den aktuella datauppsättningen. Under omskrivningar, förväntar du dig att uppnå prestandabegränsningarna snabbare särskilt vid hantering av stora datamängder. Omskrivningar ske mindre ofta som filen AOF blir större, men kan ta lång tid när det sker.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Vad bör jag förvänta dig när skalning en cache med AOF aktiverad?

Om filen AOF vid tidpunkten för skalning är avsevärt stor förväntar du dig åtgärden tar längre tid än förväntat eftersom den kommer att ladda filen efter skalning har slutförts.

Läs mer om att skala [vad händer om jag har skalas till en annan storlek och en säkerhetskopia återställs som togs före skalning igen?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hur organiseras mina AOF data i lagring?

Data som lagras i AOF filer är indelade i flera sidblobbar per nod att öka prestandan för att spara data till lagring. Följande tabell visar hur många sidblobbar som ska användas för varje prisnivå:

| Premiumnivå | Blobar |
|--------------|-------|
| P1           | 4 per Fragmentera    |
| P2           | 8 per Fragmentera    |
| P3           | 16 per Fragmentera   |
| P4           | 20 per Fragmentera   |

När kluster aktiveras har varje Fragmentera i cacheminnet en egen uppsättning sidblobar, som anges i föregående tabell. Till exempel distribuerar en P2 cache med tre delar dess AOF fil över 24 sidblobbar (8 blobbar per Fragmentera med 3 shards).

Efter en omarbetning finns två uppsättningar av AOF filer i lagringen. Omskrivningar sker i bakgrunden och lägga till den första uppsättningen filer, medan uppsättning åtgärder som skickas till cachen under omarbetningen lägga till den andra uppsättningen. En säkerhetskopia lagras tillfälligt under omskrivningar om fel uppstår, men raderas omedelbart när en omarbetning har slutförts.


## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder flera funktioner som cache premium.

* [Introduktion till Azure Redis Cache Premium-nivån](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
