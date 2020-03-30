---
title: Konfigurera databeständighet – Premium Azure Cache för Redis
description: Lär dig hur du konfigurerar och hanterar data persistens din Azure-nivå azure-cache för Redis-instanser på Premium-nivå
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: 84a5b4784a36fb22ae50a7a1ec4fcb7e5ef5b7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245284"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Konfigurera databeständighet för en Premium Azure-cache för Redis
Azure Cache för Redis har olika cacheerbjudanden som ger flexibilitet i valet av cachestorlek och funktioner, inklusive Premium-nivåfunktioner som klustring, persistens och stöd för virtuella nätverk. I den här artikeln beskrivs hur du konfigurerar persistens i en premium Azure-cache för Redis-instans.

Information om andra premiumcachefunktioner finns i [Introduktion till Azure Cache för Redis Premium-nivå](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Vad är databeständighet?
[Redis persistens](https://redis.io/topics/persistence) kan du beständiga data som lagras i Redis. Du kan också ta ögonblicksbilder och säkerhetskopiera data, som du kan läsa in i händelse av ett maskinvarufel. Detta är en stor fördel jämfört med basic- eller standardnivå där alla data lagras i minnet och det kan finnas potentiell dataförlust vid ett fel där cachenoder är nere. 

Azure Cache för Redis erbjuder Redis persistens med hjälp av följande modeller:

* **RDB persistens** - När RDB (Redis-databas) persistens är konfigurerad, Azure Cache för Redis beständig en ögonblicksbild av Azure Cache för Redis i en Redis binärt format till disk baserat på en konfigurerbar säkerhetskopiering frekvens. Om en oåterkallelig händelse inträffar som inaktiverar både den primära och replikcachen rekonstrueras cacheminnet med den senaste ögonblicksbilden. Läs mer om [fördelarna](https://redis.io/topics/persistence#rdb-advantages) och [nackdelarna med](https://redis.io/topics/persistence#rdb-disadvantages) RDB uthållighet.
* **AOF persistens** - När AOF (Lägg till endast fil) persistens är konfigurerad, sparar Azure Cache för Redis varje skrivåtgärd till en logg som sparas minst en gång per sekund i ett Azure Storage-konto. Om en oåterkallelig händelse inträffar som inaktiverar både den primära och replikcachen rekonstrueras cachen med hjälp av de lagrade skrivåtgärderna. Läs mer om [fördelarna](https://redis.io/topics/persistence#aof-advantages) och [nackdelarna med](https://redis.io/topics/persistence#aof-disadvantages) AOF uthållighet.

Persistens skriver Redis-data till ett Azure Storage-konto som du äger och hanterar. Du kan konfigurera från bladet **Ny Azure-cache för Redis** när **cacheminnet** skapas och på resursmenyn för befintliga premiumcachen.

> [!NOTE]
> 
> Azure Storage krypterar automatiskt data när den sparas. Du kan använda dina egna nycklar för krypteringen. Mer information finns i [Kundhanterade nycklar med Azure Key Vault](/azure/storage/common/storage-service-encryption).
> 
> 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

När en premiumprisnivå har valts klickar du på **Redis persistens**.

![Redis uthållighet][redis-cache-persistence]

Stegen i nästa avsnitt beskriver hur du konfigurerar Redis persistens på den nya premiumcachen. När Redis persistens har konfigurerats klickar du på **Skapa** för att skapa din nya premiumcache med Redis persistens.

## <a name="enable-redis-persistence"></a>Aktivera Redis persistens

Redis persistens är aktiverat på **bladet Data persistens** genom att välja antingen **RDB** eller **AOF** persistens. För nya cacheminnen används det här bladet under processen för att skapa cacheminnet, enligt beskrivningen i föregående avsnitt. För befintliga cacheminnen nås **bladet Data persistens** från **resursmenyn** för cacheminnet.

![Inställningar för Redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurera RDB-persistens

Om du vill aktivera RDB-persistens klickar du på **RDB**. Om du vill inaktivera RDB-persistens på en tidigare aktiverad premiumcache klickar du på **Inaktiverad**.

![Redis RDB uthållighet][redis-cache-rdb-persistence]

Om du vill konfigurera säkerhetskopieringsintervallet väljer du en **säkerhetskopieringsfrekvens** i listrutan. Alternativen inkluderar **15 minuter,** **30 minuter,** **60 minuter,** **6 timmar,** **12 timmar**och **24 timmar.** Det här intervallet börjar räkna ned när den tidigare säkerhetskopieringen har slutförts och när den förflyter en ny säkerhetskopia initieras.

Klicka på **Lagringskonto** för att välja det lagringskonto som ska användas och välj antingen **primärnyckeln** eller **sekundärnyckeln** som ska användas i listrutan **Lagringsnyckel.** Du måste välja ett lagringskonto i samma region som cacheminnet, och ett **Premium Storage-konto** rekommenderas eftersom premiumlagring har högre dataflöde. 

> [!IMPORTANT]
> Om lagringsnyckeln för ditt persistenskonto återskapas måste du konfigurera om önskad nyckel från listrutan **Lagringsnyckel.**
> 
> 

Klicka på **OK** för att spara beständighetskonfigurationen.

Nästa säkerhetskopia (eller första säkerhetskopiering för nya cacheminnen) initieras när frekvensintervallet för säkerhetskopiering förflutit.

## <a name="configure-aof-persistence"></a>Konfigurera AOF-persistens

Om du vill aktivera AOF-persistens klickar du på **AOF**. Om du vill inaktivera AOF-persistens på en tidigare aktiverad premiumcache klickar du på **Inaktiverad**.

![Redis AOF uthållighet][redis-cache-aof-persistence]

Om du vill konfigurera AOF-persistens anger du ett **första lagringskonto**. Det här lagringskontot måste finnas i samma region som cacheminnet och ett **Premium Storage-konto** rekommenderas eftersom premiumlagring har högre dataflöde. Du kan också konfigurera ytterligare ett lagringskonto med namnet **Second Storage Account**. Om ett andra lagringskonto har konfigurerats skrivs skrivs skrivna till replikcachen till det andra lagringskontot. För varje konfigurerat lagringskonto väljer du antingen **primärnyckeln** eller **sekundärnyckeln** som ska användas i listrutan **Lagringsnyckel.** 

> [!IMPORTANT]
> Om lagringsnyckeln för ditt persistenskonto återskapas måste du konfigurera om önskad nyckel från listrutan **Lagringsnyckel.**
> 
> 

När AOF-persistens är aktiverat sparas skrivåtgärder till cachen i det angivna lagringskontot (eller konton om du har konfigurerat ett andra lagringskonto). I händelse av ett oåterkalleligt fel som tar ner både den primära och replikcachen används den lagrade AOF-loggen för att återskapa cacheminnet.

## <a name="persistence-faq"></a>Vanliga frågor om uthållighet
Följande lista innehåller svar på vanliga frågor om Azure Cache för Redis persistens.

* [Kan jag aktivera persistens på en tidigare skapad cache?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Kan jag aktivera AOF och RDB uthållighet på samma gång?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Vilken persistensmodell ska jag välja?](#which-persistence-model-should-i-choose)
* [Vad händer om jag har skalats till en annan storlek och en säkerhetskopia återställs som gjordes före skalningsåtgärden?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB uthållighet
* [Kan jag ändra RDB-säkerhetskopieringsfrekvensen när jag har skapat cacheminnet?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Varför om jag har en RDB backup frekvens på 60 minuter finns det mer än 60 minuter mellan säkerhetskopior?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Vad händer med de gamla RDB-säkerhetskopiorna när en ny säkerhetskopia görs?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF uthållighet
* [När ska jag använda ett andra lagringskonto?](#when-should-i-use-a-second-storage-account)
* [Påverkar AOF-beständighet hela, latens eller prestanda för min cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hur tar jag bort det andra lagringskontot?](#how-can-i-remove-the-second-storage-account)
* [Vad är en omskrivning och hur påverkar det min cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Vad kan jag förvänta mig när jag skalar en cache med AOF aktiverat?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hur är mina AOF-data organiserade i lagring?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Kan jag aktivera persistens på en tidigare skapad cache?
Ja, Redis persistens kan konfigureras både när cache skapas och på befintliga premiumcachar.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Kan jag aktivera AOF och RDB uthållighet på samma gång?

Nej, du kan bara aktivera RDB eller AOF, men inte båda samtidigt.

### <a name="which-persistence-model-should-i-choose"></a>Vilken persistensmodell ska jag välja?

AOF persistens sparar varje skriv till en logg, vilket har viss inverkan på dataflödet, jämfört med RDB persistens som sparar säkerhetskopior baserat på det konfigurerade säkerhetskopieringsintervallet, med minimal inverkan på prestanda. Välj AOF-persistens om ditt primära mål är att minimera dataförlust och du kan hantera en minskning av dataflödet för cacheminnet. Välj RDB-persistens om du vill behålla optimalt dataflöde på cacheminnet, men ändå vill ha en mekanism för dataåterställning.

* Läs mer om [fördelarna](https://redis.io/topics/persistence#rdb-advantages) och [nackdelarna med](https://redis.io/topics/persistence#rdb-disadvantages) RDB uthållighet.
* Läs mer om [fördelarna](https://redis.io/topics/persistence#aof-advantages) och [nackdelarna med](https://redis.io/topics/persistence#aof-disadvantages) AOF uthållighet.

Mer information om prestanda när du använder AOF-persistens finns i [Påverkar AOF-persistens hela, svarstiden eller prestandan i cacheminnet?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Vad händer om jag har skalats till en annan storlek och en säkerhetskopia återställs som gjordes före skalningsåtgärden?

För både RDB och AOF persistens:

* Om du har skalat till en större storlek, finns det ingen inverkan.
* Om du har skalat till en mindre storlek och du har en anpassad [databasinställning](cache-configure.md#databases) som är större än [databasgränsen](cache-configure.md#databases) för den nya storleken, återställs inte data i dessa databaser. Mer information finns i [Påverkas inställningen för anpassade databaser under skalningen?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Om du har skalat till en mindre storlek, och det inte finns tillräckligt med utrymme i den mindre storleken för att hålla alla data från den senaste säkerhetskopian, kommer nycklarna att vräkas under återställningsprocessen, vanligtvis med hjälp av [allkeys-lru](https://redis.io/topics/lru-cache) vräkningsprincipen.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Kan jag ändra RDB-säkerhetskopieringsfrekvensen när jag har skapat cacheminnet?
Ja, du kan ändra säkerhetskopieringsfrekvensen för RDB-persistens på **bladet Data persistens.** Instruktioner finns i Konfigurera Redis persistens.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Varför om jag har en RDB backup frekvens på 60 minuter finns det mer än 60 minuter mellan säkerhetskopior?
Frekvensintervallet för säkerhetskopiering av RDB startar inte förrän den tidigare säkerhetskopieringsprocessen har slutförts. Om säkerhetskopieringsfrekvensen är 60 minuter och det tar en säkerhetskopieringsprocess 15 minuter att slutföra, startar inte nästa säkerhetskopia förrän 75 minuter efter starttiden för den tidigare säkerhetskopieringen.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Vad händer med de gamla RDB-säkerhetskopiorna när en ny säkerhetskopia görs?
Alla RDB-säkerhetskopior med undantag för den senaste tas bort automatiskt. Den här borttagningen kanske inte sker omedelbart, men äldre säkerhetskopior sparas inte på obestämd tid.


### <a name="when-should-i-use-a-second-storage-account"></a>När ska jag använda ett andra lagringskonto?

Du bör använda ett andra lagringskonto för AOF-persistens när du tror att du har högre uppsättning åtgärder än förväntat på cachen.  Genom att konfigurera det sekundära lagringskontot kan du se till att cachen inte når begränsningar för lagringsbandbredd.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Påverkar AOF-beständighet hela, latens eller prestanda för min cache?

AOF persistens påverkar dataflödet med ca 15% - 20% när cachen är under maximal belastning (CPU och Server Load både under 90%). Det bör inte finnas svarstidsproblem när cachen ligger inom dessa gränser. Cacheminnet når dock dessa gränser tidigare med AOF aktiverat.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hur tar jag bort det andra lagringskontot?

Du kan ta bort det sekundära lagringskontot för AOF-persistens genom att ange att det andra lagringskontot ska vara detsamma som det första lagringskontot. Instruktioner finns i [Konfigurera AOF-persistens](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Vad är en omskrivning och hur påverkar det min cache?

När AOF-filen blir tillräckligt stor ställs en omskrivning automatiskt i kö på cachen. Omskrivningen ändrar storlek på AOF-filen med den minimala uppsättning åtgärder som krävs för att skapa den aktuella datauppsättningen. Vid omskrivningar, räkna med att nå prestandagränser tidigare, särskilt när det handlar om stora datauppsättningar. Omskrivningar sker mindre ofta när AOF-filen blir större, men tar en betydande tid när det händer.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Vad kan jag förvänta mig när jag skalar en cache med AOF aktiverat?

Om AOF-filen vid tidpunkten för skalningen är betydligt stor, förvänta dig sedan att skalningsåtgärden tar längre tid än förväntat eftersom den kommer att läsa in filen igen när skalningen är klar.

Mer information om skalning finns i [Vad händer om jag har skalats till en annan storlek och en säkerhetskopia återställs som gjordes före skalningsåtgärden?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hur är mina AOF-data organiserade i lagring?

Data som lagras i AOF-filer är indelade i flera sidblobar per nod för att öka prestanda för att spara data i lagring. I följande tabell visas hur många sidblobar som används för varje prisnivå:

| Premiumnivå | Blobar |
|--------------|-------|
| P1           | 4 per skärva    |
| P2           | 8 per skärva    |
| P3           | 16 per skärva   |
| P4           | 20 per skärva   |

När klustring är aktiverat har varje fragment i cacheminnet en egen uppsättning sidblobar, vilket anges i föregående tabell. En P2-cache med tre shards distribuerar till exempel sin AOF-fil över 24 sidblobar (8 blobbar per shard, med 3 shards).

Efter en omskrivning finns det två uppsättningar AOF-filer i lagring. Omskrivningar sker i bakgrunden och läggs till den första uppsättningen filer, medan du anger åtgärder som skickas till cacheminnet under omskrivningen läggs till den andra uppsättningen. En säkerhetskopia lagras tillfälligt under omskrivningar i händelse av fel, men tas omedelbart bort när en omskrivning har slutförts.


## <a name="next-steps"></a>Nästa steg
Läs om hur du använder fler premiumcachefunktioner.

* [Introduktion till Azure Cache för Redis Premium-nivå](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
