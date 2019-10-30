---
title: Felsöka Azure cache för Redis data förlust | Microsoft Docs
description: Lär dig hur du löser problem med data förlust med Azure cache för Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 4fee7c84b394e84369b28d2a4191d0e581f3beba
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044351"
---
# <a name="troubleshoot-azure-cache-for-redis-data-loss"></a>Felsöka Azure cache för Redis data förlust

I det här avsnittet beskrivs hur du diagnostiserar faktiska eller uppfattade data förluster som kan uppstå i Azure cache för Redis.

- [Partiell förlust av nycklar](#partial-loss-of-keys)
- [Huvud eller fullständig förlust av nycklar](#major-or-complete-loss-of-keys)

> [!NOTE]
> Flera av fel söknings stegen i den här hand boken innehåller instruktioner för att köra Redis-kommandon och övervaka olika prestanda mått. Mer information och instruktioner finns i artiklarna i avsnittet [Ytterligare information](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Partiell förlust av nycklar

Redis tar inte bort nycklar slumpmässigt när de har lagrats i minnet. Nycklarna tas dock bort, som svar på principer för förfallo datum eller borttagning och även explicita nyckel borttagnings kommandon. Dessutom kanske nycklar som har skrivits till huvudnoden i en Premium-eller standard-Azure-cache för Redis inte är tillgängliga på en replik direkt. Data replikeras från huvud servern till repliken i ett asynkront och icke-blockerande sätt.

Om du upptäcker att nycklarna har försvunnit från cacheminnet kan du kontrol lera följande för att se vilka som kan vara orsaken:

| Orsak | Beskrivning |
|---|---|
| [Nyckel förfallo datum](#key-expiration) | Nycklarna tas bort på grund av att tids gränsen har angetts |
| [Nyckel borttagning](#key-eviction) | Nycklar tas bort under minnes tryck |
| [Nyckel borttagning](#key-deletion) | Nycklar tas bort av explicita borttagnings kommandon |
| [Asynkron replikering](#async-replication) | Nycklar är inte tillgängliga på en replik på grund av fördröjningar vid datareplikering |

### <a name="key-expiration"></a>Nyckel förfallo datum

Redis tar bort en nyckel automatiskt om den tilldelas en tids gräns och den perioden har passerat. Du hittar mer information om Redis-nyckeln upphör att gälla i den [förfallna](http://redis.io/commands/expire) kommando dokumentationen. Timeout-värden kan också ställas in via [set](http://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset)och andra \*STORE-kommandon.

Du kan använda kommandot [info](http://redis.io/commands/info) för att få statistik om hur många nycklar som har upphört att gälla. Avsnittet *statistik* visar det totala antalet utgångna nycklar. Avsnittet för nyckel *utrymme* innehåller ytterligare information om antalet nycklar med timeout och genomsnittlig tids gräns.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Dessutom kan du titta på diagnostiska mått för cacheminnet för att se om det finns en korrelation mellan när nyckeln gick förlorad och en insamling i utgångna nycklar. I [bilagan](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) finns information om hur du använder meddelanden om disk utrymme eller Övervakare för att felsöka dessa typer av problem.

### <a name="key-eviction"></a>Nyckel borttagning

Redis kräver minnes utrymme för att lagra data. Den tar bort nycklar för att frigöra tillgängligt minne vid behov. När **used_memory** -eller **used_memory_rss** -värdena i [informations](http://redis.io/commands/info) kommandot närmar sig den konfigurerade **maxmemory** -inställningen kommer Redis att börja avlägsna nycklar från minnet baserat på en [princip för cachelagring](http://redis.io/topics/lru-cache).

Du kan övervaka antalet nycklar som avlägsnats med hjälp av kommandot [info](http://redis.io/commands/info) .

```
# Stats

evicted_keys:13224
```

Dessutom kan du titta på diagnostiska mått för cacheminnet för att se om det finns en korrelation mellan när nyckeln gick förlorad och en insamling i avlägsnade nycklar. I [bilagan](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) finns information om hur du använder meddelanden om disk utrymme eller Övervakare för att felsöka dessa typer av problem.

### <a name="key-deletion"></a>Nyckel borttagning

Redis-klienter kan utfärda kommandot [del](http://redis.io/commands/del) eller [HDEL](http://redis.io/commands/hdel) för att explicit ta bort nycklar från Redis. Du kan spåra antalet borttagnings åtgärder med hjälp av kommandot [info](http://redis.io/commands/info) . Om DEL-eller HDEL-kommandon har anropats visas de i avsnittet *Commandstats* .

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Asynkron replikering

Alla Azure-cache för Redis på standard-eller Premium-nivån konfigureras med en huvud nod och minst en replik. Data kopieras från huvud servern till en replik asynkront med en bakgrunds process. [Redis.io](http://redis.io/topics/replication) -webbplatsen beskriver hur Redis-datareplikering fungerar i allmänhet. För scenarier där klienter skriver till Redis ofta kan partiell data förlust uppstå på grund av att den här replikeringen garanterat är omedelbar. Om huvud servern till exempel slutar fungera _när_ en klient skriver en nyckel till den, men _innan_ bakgrunds processen har möjlighet att skicka den här nyckeln till repliken, går nyckeln förlorad när repliken tar över som den nya huvud servern.

## <a name="major-or-complete-loss-of-keys"></a>Huvud eller fullständig förlust av nycklar

Om du upptäcker att de flesta av eller alla nycklar har försvunnit från cacheminnet kan du kontrol lera följande för att se vilka som kan vara orsaken:

| Orsak | Beskrivning |
|---|---|
| [Nyckel tömning](#key-flushing) | Nycklar har rensats manuellt |
| [Felaktigt databas val](#incorrect-database-selection) | Redis är inställd på att använda en databas som inte är standard |
| [Redis instans-problem](#redis-instance-failure) | Redis-servern är inte tillgänglig |

### <a name="key-flushing"></a>Nyckel tömning

Klienter kan anropa kommandot [FLUSHDB](http://redis.io/commands/flushdb) för att ta bort alla nycklar i en **enskild** databas eller [FLUSHALL](http://redis.io/commands/flushall) för att ta bort alla nycklar från **alla** databaser i en Redis-cache. Du kan ta reda på om nycklarna har tömts med hjälp av kommandot [info](http://redis.io/commands/info) . Den visas om antingen FLUSH-kommandot har anropats i *Commandstats* -avsnittet.

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Felaktigt databas val

Azure cache för Redis använder **DB0** -databasen som standard. Om du växlar till en annan databas (t. ex. DB1) och försöker läsa nycklar från den, hittar Redis inte dem där, eftersom varje databas är en logiskt separat enhet och innehåller en annan data uppsättning. Använd kommandot [Välj](http://redis.io/commands/select) för att använda andra tillgängliga databaser och leta efter nycklar i var och en av dem.

### <a name="redis-instance-failure"></a>Redis instans-problem

Redis är ett minnes intern data lager. Data lagras på fysiska eller virtuella datorer som är värdar för Redis. En Azure cache för Redis-instans på Basic-nivån körs bara på en enskild virtuell dator (VM). Om den virtuella datorn är nere går alla data som du har lagrat i cacheminnet förlorade. Cacheminnen på standard-och Premium-nivåerna ger mycket högre återhämtning mot data förlust genom att använda två virtuella datorer i en replikerad konfiguration. När huvudnoden i en sådan cache Miss lyckas, tar replik noden över för att betjäna data automatiskt. De här virtuella datorerna finns på olika fel-och uppdaterings domäner för att minimera risken för att båda blir otillgängliga samtidigt. I händelse av ett allvarligt Data Center avbrott kan de virtuella datorerna fortfarande gå ned tillsammans. Dina data kommer att gå förlorade i dessa sällsynta fall.

Du bör överväga att använda [Redis data beständighet](http://redis.io/topics/persistence) och [geo-replikering](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) för att förbättra skyddet av dina data mot de här infrastruktur felen.

## <a name="additional-information"></a>Ytterligare information

- [Felsöka Azure cache för Redis-problem på Server Sidan](cache-troubleshoot-server.md)
- [Vad ska jag använda Azure cache för Redis-erbjudande och storlek?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Så här övervakar du Azure cache för Redis](cache-how-to-monitor.md)
- [Hur kan jag köra Redis-kommandon?](cache-faq.md#how-can-i-run-redis-commands)
