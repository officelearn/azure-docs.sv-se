---
title: Felsöka dataförlust i Azure Cache for Redis
description: Lär dig hur du löser problem med dataförlust med Azure Cache för Redis, till exempel partiell förlust av nycklar, nyckelförfallodatum eller fullständig förlust av nycklar.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530909"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Felsöka dataförlust i Azure Cache for Redis

I den här artikeln beskrivs hur du diagnostiserar faktiska eller upplevda dataförluster som kan uppstå i Azure Cache för Redis.

> [!NOTE]
> Flera av felsökningsstegen i den här guiden innehåller instruktioner för att köra Redis-kommandon och övervaka olika prestandamått. Mer information och instruktioner finns i artiklarna i avsnittet [Ytterligare information.](#additional-information)
>

## <a name="partial-loss-of-keys"></a>Partiell förlust av nycklar

Azure Cache för Redis tar inte slumpmässigt bort nycklar när de har lagrats i minnet. Det tar dock bort nycklar som svar på principer för förfallodatum eller vräkning och explicita kommandon för nyckelborttagning. Nycklar som har skrivits till huvudnoden i en Premium- eller Standard Azure-cache för Redis-instans kanske inte heller är tillgängliga på en replik direkt. Data replikeras från huvudmodellen till repliken på ett asynkront och icke-blockerande sätt.

Om du upptäcker att nycklarna har försvunnit från cacheminnet kontrollerar du följande möjliga orsaker:

| Orsak | Beskrivning |
|---|---|
| [Förfallodatum för nyckel](#key-expiration) | Nycklar tas bort på grund av time-outs som anges på dem. |
| [Nyckelvräkning](#key-eviction) | Nycklarna tas bort under minnestryck. |
| [Borttagning av nyckel](#key-deletion) | Nycklar tas bort med explicita borttagningskommandon. |
| [Async-replikering](#async-replication) | Nycklar är inte tillgängliga på en replik på grund av fördröjningar för datareplikering. |

### <a name="key-expiration"></a>Förfallodatum för nyckel

Azure Cache för Redis tar bort en nyckel automatiskt om nyckeln tilldelas en time-out och den perioden har passerat. Mer information om Redis-tangentens förfallodatum finns i [kommandodokumentationen EXPIRE.](https://redis.io/commands/expire) Time-out-värden kan också ställas in med hjälp av [kommandona SET,](https://redis.io/commands/set) [SETEX,](https://redis.io/commands/setex) [GETSET](https://redis.io/commands/getset)och andra ** \*STORE.**

Om du vill få statistik över hur många nycklar som har gått ut använder du kommandot [INFO.](https://redis.io/commands/info) Avsnittet `Stats` visar det totala antalet utgångna nycklar. Avsnittet `Keyspace` innehåller mer information om antalet nycklar med time-outs och det genomsnittliga time-out-värdet.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Du kan också titta på diagnostikmått för cacheminnet för att se om det finns en korrelation mellan när nyckeln försvann och en topp i utgångna nycklar. Se tillägget för [felsökning av Redis Keyspace Missar](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) för information om hur du använder keyspace-meddelanden eller **MONITOR** för att felsöka dessa typer av problem.

### <a name="key-eviction"></a>Nyckelvräkning

Azure Cache för Redis kräver minnesutrymme för att lagra data. Den rensar nycklar för att frigöra tillgängligt minne vid behov. När **used_memory** eller **used_memory_rss** värdena i kommandot [INFO](https://redis.io/commands/info) närmar sig den konfigurerade **maxmemory-inställningen,** börjar Azure Cache för Redis att vräkas nycklar från minnet baserat på [cacheprincip](https://redis.io/topics/lru-cache).

Du kan övervaka antalet bortkavade nycklar med kommandot [INFO:](https://redis.io/commands/info)

```
# Stats

evicted_keys:13224
```

Du kan också titta på diagnostikmått för cacheminnet för att se om det finns en korrelation mellan när nyckeln försvann och en topp i bortreverade nycklar. Se tillägget för [felsökning av Redis Keyspace Missar](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) för information om hur du använder keyspace-meddelanden eller **MONITOR** för att felsöka dessa typer av problem.

### <a name="key-deletion"></a>Borttagning av nyckel

Redis-klienter kan utfärda [kommandot DEL](https://redis.io/commands/del) eller [HDEL](https://redis.io/commands/hdel) för att uttryckligen ta bort nycklar från Azure Cache för Redis. Du kan spåra antalet borttagningsåtgärder med kommandot [INFO.](https://redis.io/commands/info) Om **KOMMANDONA DEL** eller **HDEL** har anropats visas `Commandstats` de i avsnittet .

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Async-replikering

Alla Azure-cache för Redis-instans på standard- eller Premium-nivån är konfigurerade med en huvudnod och minst en replik. Data kopieras från bakgrunden till en replik asynkront med hjälp av en bakgrundsprocess. Den [redis.io](https://redis.io/topics/replication) webbplatsen beskriver hur Redis datareplikering fungerar i allmänhet. För scenarier där klienter skriver till Redis ofta kan partiell dataförlust uppstå eftersom den här replikeringen garanteras vara omedelbar. Om till exempel bakgrunden går ned *när* en klient har skrivit en nyckel till den, men *innan* bakgrundsprocessen har en chans att skicka nyckeln till repliken, går nyckeln förlorad när repliken tar över som ny huvudsida.

## <a name="major-or-complete-loss-of-keys"></a>Större eller fullständig förlust av nycklar

Om de flesta eller alla nycklar har försvunnit från cacheminnet kontrollerar du följande möjliga orsaker:

| Orsak | Beskrivning |
|---|---|
| [Tömning av nyckel](#key-flushing) | Nycklarna har rensats manuellt. |
| [Felaktigt databasval](#incorrect-database-selection) | Azure Cache för Redis är inställd på att använda en databas som inte är standard. |
| [Redis-instansfel](#redis-instance-failure) | Redis-servern är inte tillgänglig. |

### <a name="key-flushing"></a>Tömning av nyckel

Klienter kan anropa [kommandot FLUSHDB](https://redis.io/commands/flushdb) för att ta bort alla nycklar i en *enda* databas eller [FLUSHALL](https://redis.io/commands/flushall) för att ta bort alla nycklar från *alla* databaser i en Redis-cache. Om du vill ta reda på om tangenterna har tömts använder du kommandot [INFO.](https://redis.io/commands/info) Avsnittet `Commandstats` visar om antingen **FLUSH-kommandot** har anropats:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Felaktigt databasval

Azure Cache för Redis använder **db0-databasen** som standard. Om du växlar till en annan databas (till exempel **db1**) och försöker läsa nycklar från den, hittar inte Azure Cache för Redis dem där. Varje databas är en logiskt separat enhet och innehåller en annan datauppsättning. Använd kommandot [SELECT](https://redis.io/commands/select) om du vill använda andra tillgängliga databaser och leta efter nycklar i var och en av dem.

### <a name="redis-instance-failure"></a>Redis-instansfel

Redis är ett datalager i minnet. Data lagras på de fysiska eller virtuella datorer som är värdar för Redis-cachen. En Azure Cache for Redis-instans på basic-nivån körs endast på en virtuell dator (VM). Om den virtuella datorn är nere går alla data som du har lagrat i cacheminnet förlorade. 

Cacheminnen på standard- och premiumnivåerna erbjuder mycket högre återhämtning mot dataförlust med hjälp av två virtuella datorer i en replikerad konfiguration. När huvudnoden i en sådan cache misslyckas tar repliknoden över för att tjäna data automatiskt. Dessa virtuella datorer finns på separata domäner för fel och uppdateringar, för att minimera risken för att båda blir otillgängliga samtidigt. Om ett större datacenter avbrott inträffar kan dock de virtuella datorerna fortfarande gå ner tillsammans. Dina data kommer att gå förlorade i dessa sällsynta fall.

Överväg att använda [Redis-data persistens](https://redis.io/topics/persistence) och [geo-replikering](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) för att förbättra skyddet av dina data mot dessa infrastrukturfel.

## <a name="additional-information"></a>Ytterligare information

- [Felsöka problem på Azure Cache for Redis-serversidan](cache-troubleshoot-server.md)
- [Vad Azure Cache för Redis erbjuder och storlek ska jag använda?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Övervaka Azure Cache för Redis](cache-how-to-monitor.md)
- [Hur kör jag Redis-kommandon?](cache-faq.md#how-can-i-run-redis-commands)
