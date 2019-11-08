---
title: Felsöka data förlust i Azure cache för Redis | Microsoft Docs
description: Lär dig hur du löser data förlust problem med Azure cache för Redis
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
ms.openlocfilehash: 8165ce3195c12af52cfee2fff598d9417697f4cb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806566"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Felsöka data förlust i Azure cache för Redis

Den här artikeln beskriver hur du diagnostiserar faktiska eller uppfattade data förluster som kan uppstå i Azure cache för Redis.

> [!NOTE]
> Flera av fel söknings stegen i den här hand boken innehåller instruktioner för att köra Redis-kommandon och övervaka olika prestanda mått. Mer information och instruktioner finns i artiklarna i avsnittet [Ytterligare information](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Partiell förlust av nycklar

Azure cache för Redis tar inte bort nycklar när de har lagrats i minnet. Den tar dock bort nycklar som svar på principer för förfallo datum eller borttagning och till explicita kommandon för nyckel borttagning. Nycklar som har skrivits till huvudnoden i en Premium-eller standard-Azure-cache för Redis-instansen kanske inte heller är tillgängliga på en replik omedelbart. Data replikeras från huvud servern till repliken i ett asynkront och icke-blockerande sätt.

Om du upptäcker att nycklarna har försvunnit från cacheminnet kontrollerar du följande möjliga orsaker:

| Orsak | Beskrivning |
|---|---|
| [Nyckel förfallo datum](#key-expiration) | Nycklarna tas bort på grund av timeout-inställningar. |
| [Nyckel borttagning](#key-eviction) | Nycklarna tas bort under minnes belastningen. |
| [Nyckel borttagning](#key-deletion) | Nycklar tas bort av explicita borttagnings kommandon. |
| [Asynkron replikering](#async-replication) | Nycklarna är inte tillgängliga på en replik på grund av fördröjningar vid replikering av data. |

### <a name="key-expiration"></a>Nyckel förfallo datum

Azure cache för Redis tar bort en nyckel automatiskt om nyckeln tilldelas en timeout och den perioden har passerat. Mer information om förfallo datum för Redis-nycklar finns i den [förfallna](http://redis.io/commands/expire) kommando dokumentationen. Timeout-värden kan också anges med hjälp av kommandona [set](http://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset)och andra **\*Store** .

Om du vill få statistik om hur många nycklar som har upphört att gälla använder du kommandot [info](http://redis.io/commands/info) . I avsnittet `Stats` visas det totala antalet utgångna nycklar. Avsnittet `Keyspace` innehåller mer information om antalet nycklar med timeout och genomsnittligt timeout-värde.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Du kan också titta på diagnostiska mått för cacheminnet för att se om det finns en korrelation mellan när nyckeln gick förlorad och en insamling i utgångna nycklar. Se tillägget för fel [sökning av Redis](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) för att få information om hur du använder meddelanden eller **övervakare** för att felsöka de här typerna av problem.

### <a name="key-eviction"></a>Nyckel borttagning

Azure cache för Redis kräver minnes utrymme för att lagra data. Den rensar nycklar för att frigöra tillgängligt minne vid behov. När **used_memory** -eller **used_memory_rss** -värden i [informations](http://redis.io/commands/info) kommandot närmar sig den konfigurerade **maxmemory** -inställningen börjar Azure cache för Redis bort nycklar från minnet baserat på en [princip för cachelagring](http://redis.io/topics/lru-cache).

Du kan övervaka antalet avlägsnade nycklar med hjälp av kommandot [info](http://redis.io/commands/info) :

```
# Stats

evicted_keys:13224
```

Du kan också titta på diagnostiska mått för cacheminnet för att se om det finns en korrelation mellan när nyckeln gick förlorad och en insamling i avlägsnade nycklar. Se tillägget för fel [sökning av Redis](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) för att få information om hur du använder meddelanden eller **övervakare** för att felsöka de här typerna av problem.

### <a name="key-deletion"></a>Nyckel borttagning

Redis-klienter kan utfärda kommandot [del](http://redis.io/commands/del) eller [HDEL](http://redis.io/commands/hdel) för att explicit ta bort nycklar från Azure cache för Redis. Du kan spåra antalet borttagnings åtgärder med hjälp av kommandot [info](http://redis.io/commands/info) . Om **del** -eller **HDEL** -kommandon har anropats visas de i avsnittet `Commandstats`.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Asynkron replikering

Alla Azure cache för Redis-instanser på standard-eller Premium-nivån konfigureras med en huvud nod och minst en replik. Data kopieras från huvud servern till en replik asynkront med hjälp av en bakgrunds process. [Redis.io](http://redis.io/topics/replication) -webbplatsen beskriver hur Redis-datareplikering fungerar i allmänhet. För scenarier där klienter skriver till Redis ofta kan partiell data förlust uppstå, eftersom replikeringen garanterat är omedelbar. Om huvud servern till exempel slutar fungera *när* en klient skriver en nyckel, men *innan* bakgrunds processen har möjlighet att skicka nyckeln till repliken, försvinner nyckeln när repliken tar över som den nya huvud servern.

## <a name="major-or-complete-loss-of-keys"></a>Huvud eller fullständig förlust av nycklar

Om de flesta eller alla nycklar har försvunnit från cacheminnet kontrollerar du följande möjliga orsaker:

| Orsak | Beskrivning |
|---|---|
| [Nyckel tömning](#key-flushing) | Nycklar har rensats manuellt. |
| [Felaktigt databas val](#incorrect-database-selection) | Azure cache för Redis är inställt på att använda en databas som inte är standard. |
| [Redis instans-problem](#redis-instance-failure) | Redis-servern är inte tillgänglig. |

### <a name="key-flushing"></a>Nyckel tömning

Klienter kan anropa kommandot [FLUSHDB](http://redis.io/commands/flushdb) för att ta bort alla nycklar i en *enskild* databas eller [FLUSHALL](http://redis.io/commands/flushall) för att ta bort alla nycklar från *alla* databaser i en Redis-cache. Om du vill ta reda på om nycklarna har tömts använder du kommandot [info](http://redis.io/commands/info) . I avsnittet `Commandstats` visas om antingen **Flush** -kommandot har anropats:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Felaktigt databas val

Azure cache för Redis använder **DB0** -databasen som standard. Om du växlar till en annan databas (till exempel **DB1**) och försöker läsa nycklar från den, hittar inte Azure cache för Redis dem där. Varje databas är en logiskt separat enhet och innehåller en annan data uppsättning. Använd kommandot [Välj](http://redis.io/commands/select) för att använda andra tillgängliga databaser och leta efter nycklar i var och en av dem.

### <a name="redis-instance-failure"></a>Redis instans-problem

Redis är ett minnes intern data lager. Data lagras på fysiska eller virtuella datorer som är värdar för Redis-cachen. En Azure cache för Redis-instans på Basic-nivån körs bara på en enskild virtuell dator (VM). Om den virtuella datorn är nere går alla data som du har lagrat i cacheminnet förlorade. 

Cacheminnen på standard-och Premium-nivåerna ger mycket högre återhämtning mot data förlust genom att använda två virtuella datorer i en replikerad konfiguration. När huvudnoden i en sådan cache Miss lyckas, tar replik noden över för att betjäna data automatiskt. De här virtuella datorerna finns i separata domäner för fel och uppdateringar för att minimera risken för att båda blir otillgängliga samtidigt. Om ett allvarligt Data Center avbrott inträffar kan de virtuella datorerna fortfarande gå ner tillsammans. Dina data kommer att gå förlorade i dessa sällsynta fall.

Överväg att använda [Redis data beständighet](http://redis.io/topics/persistence) och [geo-replikering](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) för att förbättra skyddet av dina data mot de här infrastruktur felen.

## <a name="additional-information"></a>Ytterligare information

- [Felsöka Azure cache för Redis-problem på Server Sidan](cache-troubleshoot-server.md)
- [Vad ska jag använda Azure cache för Redis-erbjudande och storlek?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Så här övervakar du Azure cache för Redis](cache-how-to-monitor.md)
- [Hur kan jag köra Redis-kommandon?](cache-faq.md#how-can-i-run-redis-commands)
