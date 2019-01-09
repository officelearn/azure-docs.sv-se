---
title: Så här konfigurerar du datapersistence för Premium Azure Cache för Redis
description: Lär dig hur du konfigurerar och hanterar datapersistensen din Premium-nivån Azure Cache för Redis-instanser
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: wesmc
ms.openlocfilehash: 60f9baf7fb54706dc9d31c6920c0df24173d7b35
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105856"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Så här konfigurerar du datapersistence för Premium Azure Cache för Redis
Azure Redis-Cache har olika cachefunktioner som ger flexibilitet i valet av cachestorlek och funktioner, inklusive funktioner på Premiumnivå som klustring, persistence och stöd för virtuella nätverk. Den här artikeln beskriver hur du konfigurerar du persistence i en Azure Cache på premiumnivå för Redis-instans.

Information om andra premiumfunktioner för cache finns i [introduktion till Azure Cache för Premium-nivån Redis](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Vad är datapersistence?
[Redis-persistens](https://redis.io/topics/persistence) gör att du kan bevara data lagrade i Redis. Du kan också ta ögonblicksbilder och säkerhetskopiera data som du kan läsa in händelse av ett maskinvarufel. Det här är en enorm fördel jämfört med Basic eller Standard-nivån där alla data lagras i minnet och det kan finnas potentiell dataförlust om ett fel uppstår där cachenoder är nere. 

Azure Cache för Redis erbjuder Redis-persistens följande modeller:

* **RDB persistence** -persistence när RDB (Redis-databas) har konfigurerats, Azure Cache för Redis kvarstår en ögonblicksbild av Azure Cache för Redis i en Redis binärt format till disk utifrån en konfigurerbar säkerhetskopieringsfrekvens. Om ett allvarligt fel inträffar som inaktiverar både den primära servern och repliken cache är cachen rekonstruerad med hjälp av den senaste ögonblicksbilden. Läs mer om den [fördelar](https://redis.io/topics/persistence#rdb-advantages) och [nackdelar](https://redis.io/topics/persistence#rdb-disadvantages) RDB ihållande.
* **AOF persistence** -persistence när AOF (Lägg till fil) har konfigurerats, Azure Cache för Redis sparar varje skrivåtgärd till en logg som sparas för minst en gång per sekund till ett Azure Storage-konto. Om ett allvarligt fel inträffar som inaktiverar både den primära servern och repliken cache är cachen rekonstruerad med hjälp av de lagrade skrivåtgärder. Läs mer om den [fördelar](https://redis.io/topics/persistence#aof-advantages) och [nackdelar](https://redis.io/topics/persistence#aof-disadvantages) AOF ihållande.

Persistence har konfigurerats från den **nya Azure-Cache för Redis** blad när cache skapas och på den **resursmenyn** befintliga premium cachelagrar.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

När en premium-prisnivån är markerad, klickar du på **Redis persistence**.

![Redis-persistens][redis-cache-persistence]

Stegen i nästa avsnitt beskriver hur du konfigurerar Redis persistence på din nya premium-cache. När Redis persistence har konfigurerats klickar du på **skapa** att skapa din nya premium-cache med Redis persistence.

## <a name="enable-redis-persistence"></a>Aktivera persistence för Redis

Redis persistence är aktiverat på den **Redis-datapersistens** bladet genom att välja antingen **RDB** eller **AOF** persistence. För nya cacheminnen är det här bladet används under skapandeprocessen cache enligt beskrivningen i föregående avsnitt. För befintliga cacheminnen på **Redis-datapersistens** bladet nås från den **resursmenyn** för cacheminnet.

![Redis-inställningar][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurera RDB persistence

Om du vill aktivera persistence RDB, klickar du på **RDB**. Om du vill inaktivera RDB persistence på tidigare aktiverade premium-cache, klickar du på **inaktiverad**.

![Redis RDB persistence][redis-cache-rdb-persistence]

Om du vill konfigurera säkerhetskopieringsintervallet, Välj en **säkerhetskopieringsfrekvens** från den nedrullningsbara listan. Alternativen är **15 minuter**, **30 minuter**, **60 minuter**, **6 timmar**, **12 timmar**, och **24 timmar**. Det här intervallet börjar räkna efter den föregående Säkerhetskopieringsåtgärden har slutförts och när det har gått ut måste en ny säkerhetskopia har initierats.

Klickar du på **Lagringskonto** att välja storage-konto du använder och välj antingen den **primärnyckel** eller **sekundärnyckel** ska användas från den **Lagringsnyckeln** listrutan. Du måste välja ett lagringskonto i samma region som cache, och en **Premiumlagring** konto rekommenderas eftersom premium-lagring har högre dataflöde. 

> [!IMPORTANT]
> Om lagringsnyckeln för ditt konto för persistence har återskapats måste du konfigurera om önskad nyckel i **Lagringsnyckeln** listrutan.
> 
> 

Klicka på **OK** sparar du konfigurationen för persistence.

Nästa säkerhetskopiering (eller första säkerhetskopieringen för nya cacheminnen) initieras när säkerhetskopieringsfrekvens intervallet nätverksförbindelse.

## <a name="configure-aof-persistence"></a>Konfigurera AOF persistence

Om du vill aktivera AOF beständighet, klickar du på **AOF**. Om du vill inaktivera AOF persistence på tidigare aktiverade premium-cache, klickar du på **inaktiverad**.

![Redis-persistens AOF][redis-cache-aof-persistence]

Om du vill konfigurera AOF beständighet, ange en **första Lagringskonto**. Det här lagringskontot måste vara i samma region som cache, och en **Premiumlagring** konto rekommenderas eftersom premium-lagring har högre dataflöde. Du kan också konfigurera ett ytterligare lagringskonto med namnet **andra Lagringskontot**. Om ett andra storage-konto har konfigurerats, skrivs skrivningar till cachen repliken till den här andra storage-konto. För varje konfigurerade lagringskontot väljer du antingen den **primärnyckel** eller **sekundärnyckel** ska användas från den **Lagringsnyckeln** listrutan. 

> [!IMPORTANT]
> Om lagringsnyckeln för ditt konto för persistence har återskapats måste du konfigurera om önskad nyckel i **Lagringsnyckeln** listrutan.
> 
> 

När AOF persistence är aktiverat kan du skriva åtgärder till cachen sparas i avsedda storage-konto (eller om du har konfigurerat ett andra konto för storage-konton). I händelse av ett oåterkalleligt fel som tar ned både den primära servern och repliken cache används lagrade AOF-loggen för att återskapa cachen.

## <a name="persistence-faq"></a>Persistence vanliga frågor och svar
I följande lista innehåller svar på vanliga frågor om Azure Cache för Redis persistence.

* [Kan jag aktivera persistence på en tidigare skapad cache?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Kan jag aktivera AOF och RDB persistence på samma gång?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Vilken persistence-modell ska jag välja?](#which-persistence-model-should-i-choose)
* [Vad händer om jag har skalats till en annan storlek och återställs en säkerhetskopia som togs före skalning igen?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB persistence
* [Kan jag ändra frekvensen för RDB-säkerhetskopiering när du har skapat cacheminnet?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Varför är det mer än 60 minuter mellan säkerhetskopior om jag har en RDB säkerhetskopieringsfrekvens 60 minuter?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Vad händer med de gamla RDB-säkerhetskopieringarna när en ny säkerhetskopia görs?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF persistence
* [När ska jag använda ett andra storage-konto?](#when-should-i-use-a-second-storage-account)
* [Fungerar AOF persistence påverkar under hela, svarstid eller prestanda Mina cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hur tar jag bort andra storage-konto?](#how-can-i-remove-the-second-storage-account)
* [Vad är en omarbetning och hur påverkar det Mina cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Vad kan jag förvänta mig när skala en cache med AOF aktiverat?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hur organiseras mina AOF-data i lagring?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Kan jag aktivera persistence på en tidigare skapad cache?
Ja, Redis persistence kan konfigureras på Skapa cache såväl på befintliga premium-cacheminnen.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Kan jag aktivera AOF och RDB persistence på samma gång?

Nej, du kan aktivera endast RDB eller AOF, men inte båda på samma gång.

### <a name="which-persistence-model-should-i-choose"></a>Vilken persistence-modell ska jag välja?

AOF persistence sparar varje skrivning till en logg som har viss inverkan på dataflöde, jämfört med RDB-persistence vilket sparar säkerhetskopior baserat på det konfigurerade intervallet för säkerhetskopiering, med minimal påverkan på prestanda. Välj AOF persistence om din primära målet är att minimera dataförlust och du kan hantera en minskning i dataflöde för cacheminnet. Välj RDB persistence om du vill behålla optimala dataflöde för cacheminnet, men fortfarande vill att en mekanism för återställning av data.

* Läs mer om den [fördelar](https://redis.io/topics/persistence#rdb-advantages) och [nackdelar](https://redis.io/topics/persistence#rdb-disadvantages) RDB ihållande.
* Läs mer om den [fördelar](https://redis.io/topics/persistence#aof-advantages) och [nackdelar](https://redis.io/topics/persistence#aof-disadvantages) AOF ihållande.

Läs mer på prestanda när du använder AOF persistence [har AOF persistence påverkar under hela, svarstid eller prestanda Mina cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Vad händer om jag har skalats till en annan storlek och återställs en säkerhetskopia som togs före skalning igen?

För både RDB och AOF persistence:

* Om du har skalat till en större storlek, påverkas inte.
* Om du har skalat till en mindre storlek och du har en anpassad [databaser](cache-configure.md#databases) inställning som är större än den [databaser gränsen](cache-configure.md#databases) för din nya storleken är inte återställa data i dessa databaser. Mer information finns i [är Mina anpassade databaser konfigurera berörda under skalning?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Om du har skalat till en mindre storlek, och det finns inte tillräckligt med utrymme i den mindre storleken att innehålla alla data från den senaste säkerhetskopieringen, nycklar kommer att avlägsnas under återställningsprocessen, vanligtvis med hjälp av den [allkeys lru](https://redis.io/topics/lru-cache) avlägsningsprincipen.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Kan jag ändra frekvensen för RDB-säkerhetskopiering när du har skapat cacheminnet?
Ja, du kan ändra frekvensen för säkerhetskopiering för RDB persistence på den **Redis-datapersistens** bladet. Anvisningar finns i [konfigurera Redis persistence](#configure-redis-persistence).

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Varför är det mer än 60 minuter mellan säkerhetskopior om jag har en RDB säkerhetskopieringsfrekvens 60 minuter?
RDB persistence säkerhetskopieringsfrekvens intervallet startar inte förrän den tidigare säkerhetskopieringen har slutförts. Om frekvensen för säkerhetskopiering är 60 minuter och det tar en säkerhetskopieringsprocess för 15 minuter för att slutföra, startar inte nästa säkerhetskopiering till 75 minuter efter starttiden för föregående säkerhetskopia.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Vad händer med de gamla RDB-säkerhetskopieringarna när en ny säkerhetskopia görs?
Alla RDB persistence säkerhetskopieringar utom den senaste tas bort automatiskt. Borttagningen kan inte ske omedelbart, men äldre säkerhetskopieringar har inte sparats på obestämd tid.


### <a name="when-should-i-use-a-second-storage-account"></a>När ska jag använda ett andra storage-konto?

Du bör använda ett andra storage-konto för AOF persistence när du tror att du har högre än förväntade set-åtgärder för cachen.  Konfigurera kontot för sekundär lagring hjälper till att säkerställa ditt cacheminne inte når lagringsgränser för bandbredd.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Fungerar AOF persistence påverkar under hela, svarstid eller prestanda Mina cache?

AOF persistence påverkar dataflöde med cirka 15 – 20% när cachen är nedan maximala belastningen (processor- och läsa in både under 90%). Det får inte vara problem med nätverkssvarstiden när cachen är inom gränserna. Dock når cachen gränserna snabbare med AOF aktiverat.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hur tar jag bort andra storage-konto?

Du kan ta bort kontot AOF persistence sekundär lagring genom att ange andra lagringskontot för att vara samma som det första lagringskontot. Anvisningar finns i [konfigurera AOF persistence](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Vad är en omarbetning och hur påverkar det Mina cache?

När filen AOF blir tillräckligt stor i kö på en omarbetning automatiskt på cacheminnet. Omskrivning ändrar storlek AOF-fil med en mindre uppsättning åtgärder som behövs för att skapa den aktuella datauppsättningen. Under omskrivningar, förväntar du dig att uppnå prestandabegränsningar tidigare särskilt vid hantering av stora datauppsättningar. Omskrivningar sker mindre ofta eftersom filen AOF blir större, men tar lång tid när det händer.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Vad kan jag förvänta mig när skala en cache med AOF aktiverat?

Om filen AOF vid tidpunkten för skalning är avsevärt stor förväntar du dig åtgärden tar längre tid än förväntat eftersom den kommer att uppdatera filen efter skalning har slutförts.

Läs mer om att skala [vad händer om jag har skalats till en annan storlek och återställs en säkerhetskopia som togs före skalning igen?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hur organiseras mina AOF-data i lagring?

Data som lagras i AOF-filer är uppdelad i flera sidblobar per nod att öka prestandan för att spara data till lagring. I följande tabell visar hur många sidblobbar används för varje prisnivå:

| Premiumnivå | Blobar |
|--------------|-------|
| P1           | 4 per shard    |
| P2           | 8 per shard    |
| P3           | 16 per shard   |
| P4           | 20 per shard   |

När klustring har aktiverats kan har varje fragment i cacheminnet en egen uppsättning sidblobar, som anges i föregående tabell. Till exempel distribuerar en P2-cache med tre delar sin AOF-fil mellan 24 sidblobar (8 blobar per shard, med 3 fragment).

När du har en omarbetning finns två uppsättningar AOF-filer i lagring. Omskrivningar sker i bakgrunden och lägga till i den första uppsättningen filer, medan uppsättning åtgärder som skickas till cachen under omskrivning lägga till i den andra uppsättningen. En säkerhetskopia lagras tillfälligt under omskrivningar om fel uppstår, men raderas omedelbart efter att en omarbetning har slutförts.


## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder mer premiumfunktioner för cache.

* [Introduktion till Azure Cache Redis Premium-nivån](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
