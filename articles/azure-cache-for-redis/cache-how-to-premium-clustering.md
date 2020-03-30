---
title: Konfigurera Redis-kluster – Premium Azure-cache för Redis
description: Lär dig hur du skapar och hanterar Redis-kluster för Azure-azure-cache för Redis-instanser på Premium-nivå
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: 761c464730096eba36bc7c04227745cf362e5cc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278043"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Konfigurera Redis-kluster för en Premium Azure-cache för Redis
Azure Cache för Redis har olika cacheerbjudanden, vilket ger flexibilitet i valet av cachestorlek och funktioner, inklusive premium-nivåfunktioner som klustring, persistens och stöd för virtuella nätverk. I den här artikeln beskrivs hur du konfigurerar klustring i en premium Azure-cache för Redis-instans.

Information om andra premiumcachefunktioner finns i [Introduktion till Azure Cache för Redis Premium-nivå](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Vad är Redis Cluster?
Azure Cache för Redis erbjuder Redis-kluster som [implementerats i Redis](https://redis.io/topics/cluster-tutorial). Med Redis Cluster får du följande fördelar: 

* Möjligheten att automatiskt dela upp datauppsättningen mellan flera noder. 
* Möjligheten att fortsätta åtgärder när en delmängd av noderna har fel eller inte kan kommunicera med resten av klustret. 
* Mer dataflöde: Dataflödet ökar linjärt när du ökar antalet shards. 
* Mer minnesstorlek: Ökar linjärt när du ökar antalet shards.  

Klustring ökar inte antalet tillgängliga anslutningar för en klustercachen. Mer information om storlek, dataflöde och bandbredd med premiumcachen finns i [Vad Azure Cache för Redis erbjuder och storlek ska jag använda?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

I Azure erbjuds Redis-klustret som en primär/replikmodell där varje shard har ett primärt/replikpar med replikering där replikeringen hanteras av Azure Cache for Redis-tjänsten. 

## <a name="clustering"></a>Klustring
Klustring är aktiverat på bladet **Ny Azure-cache för Redis** när cacheminnet skapas. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Klustring är konfigurerat på **Redis-klusterbladet.**

![Klustring][redis-cache-clustering]

Du kan ha upp till 10 shards i klustret. Klicka på **Aktiverad** och skjut skjutreglaget eller skriv ett tal mellan 1 och 10 för **fragmenträkning** och klicka på **OK**.

Varje shard är ett primärt/replikcachepar som hanteras av Azure och den totala storleken på cacheminnet beräknas genom att multiplicera antalet shards med den cachestorlek som valts i prisnivån. 

![Klustring][redis-cache-clustering-selected]

När cacheminnet har skapats ansluter du till den och använder den precis som en cache som inte är grupperad, och Redis distribuerar data i cacheminnets fragment. Om diagnostik är [aktiverat](cache-how-to-monitor.md#enable-cache-diagnostics)fångas mått separat för varje shard och kan [visas](cache-how-to-monitor.md) i Azure Cache for Redis-bladet. 

> [!NOTE]
> 
> Det finns några mindre skillnader som krävs i klientprogrammet när klustring är konfigurerad. Mer information finns i [Måste jag göra några ändringar i klientprogrammet för att använda klustring?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Exempel på kod för att arbeta med klustring med StackExchange.Redis-klienten finns i [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) delen av [Hello World-exemplet.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Ändra klusterstorleken på en premiumcache som körs
Om du vill ändra klusterstorleken på en premiumcache som körs med klusteraktiverad klickar du på **Klusterstorlek** på **resursmenyn**.

![Redis-klusterstorlek][redis-cache-redis-cluster-size]

Om du vill ändra klusterstorleken använder du skjutreglaget eller skriver ett tal mellan 1 och 10 i textrutan **Shard count** och klickar på **OK** för att spara.

Om du ökar klusterstorleken ökar maxdataflöde och cachestorlek. Öka klusterstorleken ökar inte max. anslutningar som är tillgängliga för klienter.

> [!NOTE]
> Skala ett kluster kör [migrate-kommandot,](https://redis.io/commands/migrate) vilket är ett dyrt kommando, så för minimal påverkan bör du överväga att köra den här åtgärden under icke-rusningstid. Under migreringsprocessen ser du en ökning av serverbelastningen. Att skala ett kluster är en tidskrävande process och hur lång tid det tar beror på antalet nycklar och storleken på de värden som är associerade med dessa nycklar.
> 
> 

## <a name="clustering-faq"></a>Vanliga frågor och svar om kluster
Följande lista innehåller svar på vanliga frågor om Azure Cache för Redis-kluster.

* [Måste jag göra några ändringar i mitt klientprogram för att använda klustring?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hur distribueras nycklar i ett kluster?](#how-are-keys-distributed-in-a-cluster)
* [Vilken är den största cachestorleken jag kan skapa?](#what-is-the-largest-cache-size-i-can-create)
* [Stöder alla Redis-klienter klustring?](#do-all-redis-clients-support-clustering)
* [Hur ansluter jag till min cache när klustring är aktiverat?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Kan jag ansluta direkt till de enskilda shardsna i cacheminnet?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Kan jag konfigurera klustring för en tidigare skapad cache?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Kan jag konfigurera klustring för en grundläggande cache eller standardcache?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Kan jag använda klustring med Redis ASP.NET-cachelagringsleverantörerna för sessionstillstånd och utdata?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Jag får MOVE undantag när du använder StackExchange.Redis och klustring, vad ska jag göra?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Måste jag göra några ändringar i mitt klientprogram för att använda klustring?
* När klustring är aktiverat är endast databas 0 tillgängligt. Om klientprogrammet använder flera databaser och försöker läsa eller skriva till en annan databas än 0, genereras följande undantag. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Mer information finns i [Redis Cluster Specification - Implementerad delmängd](https://redis.io/topics/cluster-spec#implemented-subset).
* Om du använder [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)måste du använda 1.0.481 eller senare. Du ansluter till cacheminnet med samma [slutpunkter, portar och nycklar](cache-configure.md#properties) som du använder när du ansluter till en cache som inte har kluster aktiverat. Den enda skillnaden är att alla läsningar och skrivningar måste göras till databas 0.
  
  * Andra klienter kan ha olika krav. Se [Stöder alla Redis-klienter klustring?](#do-all-redis-clients-support-clustering)
* Om programmet använder flera nyckelåtgärder som har grupperats till ett enda kommando måste alla nycklar finnas i samma fragment. Mer om du vill hitta nycklar i samma fragment finns i [Hur distribueras nycklar i ett kluster?](#how-are-keys-distributed-in-a-cluster)
* Om du använder Redis ASP.NET Sessionstillståndsprovidern måste du använda 2.0.1 eller senare. Se [Kan jag använda klustring med Redis ASP.NET-cachelagringsleverantörerna för sessionstillstånd och utdata?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hur distribueras nycklar i ett kluster?
Dokumentation för distributionsmodell för Redis [Keys:](https://redis.io/topics/cluster-spec#keys-distribution-model) Nyckelutrymmet är uppdelat i 16384 platser. Varje nyckel hasheras och tilldelas till en av dessa platser, som distribueras över noderna i klustret. Du kan konfigurera vilken del av nyckeln som hasheras för att säkerställa att flera nycklar finns i samma fragment med hash-taggar.

* Nycklar med en hash-tagg - om någon `{` del `}`av nyckeln är innesluten i och endast den delen av nyckeln är hashed för att bestämma hash-kortplatsen för en nyckel. Följande 3 nycklar skulle till exempel finnas i `{key}1`samma `{key}2`fragment: , och `{key}3` eftersom endast den `key` del av namnet är hashed. En fullständig lista över specifikationer för hash-tagg för nycklar finns i [Keys hash-taggar](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Nycklar utan hash-tagg - hela nyckelnamnet används för hash.Keys without a hash tag - the whole key name is used for hash. Detta resulterar i en statistiskt jämn fördelning över fragmenten i cacheminnet.

För bästa prestanda och dataflöde rekommenderar vi att du fördelar nycklarna jämnt. Om du använder nycklar med en hash-tagg är det programmets ansvar att se till att nycklarna fördelas jämnt.

Mer information finns i [Keys distributionsmodell,](https://redis.io/topics/cluster-spec#keys-distribution-model) [Redis Cluster-datasharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)och [Keys hash-taggar](https://redis.io/topics/cluster-spec#keys-hash-tags).

Exempelkod för att arbeta med klustring och lokalisering av nycklar i samma fragment med StackExchange.Redis-klienten finns i [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) delen av [Hello World-exemplet.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Vilken är den största cachestorleken jag kan skapa?
Den största premiumcachen är 120 GB. Du kan skapa upp till 10 shards vilket ger dig en maximal storlek på 1.2TB GB. Om du behöver en större storlek kan du [begära mer](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Mer information finns i [Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Stöder alla Redis-klienter klustring?
Alla klienter har inte stöd för Redis-klustring! Kontrollera dokumentationen för det bibliotek du använder för att kontrollera att du använder ett bibliotek och en version som stöder klustring. StackExchange.Redis är ett bibliotek som stöder klustring, i dess nyare versioner. Mer information om andra klienter finns i avsnittet [Spela med klustret](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) i [Redis-klusterklustersjälvstudien](https://redis.io/topics/cluster-tutorial). 

Redis-klusterprotokollet kräver att varje klient ansluter till varje shard direkt i klusterläge och definierar även nya felsvar som "FLYTTAD" na 'CROSSSLOTS'. Om du försöker använda en klient som inte stöder klustring med en klusterlägescache kan det resultera i många [undantag för omdirigering av flyttade,](https://redis.io/topics/cluster-spec#moved-redirection)eller bara bryta ditt program, om du gör flernyckelbegäranden med flera kortplats.

> [!NOTE]
> Om du använder StackExchange.Redis som klient kontrollerar du att du använder den senaste versionen av [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 eller senare för att kluster ska fungera korrekt. Om du har några problem med flyttundantag läser du [flytta undantag](#move-exceptions) för mer information.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hur ansluter jag till min cache när klustring är aktiverat?
Du kan ansluta till cacheminnet med samma [slutpunkter,](cache-configure.md#properties) [portar](cache-configure.md#properties)och [nycklar](cache-configure.md#access-keys) som du använder när du ansluter till en cache som inte har kluster aktiverat. Redis hanterar klustring på serverdelen så att du inte behöver hantera den från din klient.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Kan jag ansluta direkt till de enskilda shardsna i cacheminnet?
Klusterprotokollet kräver att klienten gör rätt fragmentanslutningar. Så kunden bör göra detta på rätt sätt för dig. Med det sagt består varje shard av ett primärt/replikcachepar, kollektivt känt som en cacheinstans. Du kan ansluta till dessa cacheinstanser med hjälp av verktyget redis-cli i den [instabila](https://redis.io/download) grenen av Redis-databasen på GitHub. Den här versionen implementerar grundläggande `-c` stöd när den startas med växeln. Mer information finns i Spela [https://redis.io](https://redis.io) med [klustret](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) i [Redis-klusterdialkursen](https://redis.io/topics/cluster-tutorial).

Använd följande kommandon för icke-ssl.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

För ssl, `1300N` `1500N`ersätt med .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Kan jag konfigurera klustring för en tidigare skapad cache?
Ja. Kontrollera först att cachen är premium, genom att skala om den inte är det. Därefter bör du kunna se klusterkonfigurationsalternativen, inklusive ett alternativ för att aktivera klustret. Du kan ändra klusterstorleken när cacheminnet har skapats eller efter att du har aktiverat klustring för första gången.

   >[!IMPORTANT]
   >Du kan inte ångra aktiveringen av kluster. Och en cache med kluster aktiverat och endast en fragment beter sig *annorlunda* än en cache av samma storlek *utan* klustring.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Kan jag konfigurera klustring för en grundläggande cache eller standardcache?
Klustring är endast tillgängligt för premiumcachen.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Kan jag använda klustring med Redis ASP.NET-cachelagringsleverantörerna för sessionstillstånd och utdata?
* **Redis Output Cache-provider** - inga ändringar krävs.
* **Redis Session State provider** - för att använda klustring, måste du använda [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 eller högre eller ett undantag genereras. Detta är en bryta förändring; Mer information finns i [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Jag får MOVE undantag när du använder StackExchange.Redis och klustring, vad ska jag göra?
Om du använder StackExchange.Redis `MOVE` och får undantag när du använder klustring kontrollerar du att du använder [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) eller senare. Instruktioner om hur du konfigurerar .NET-programmen så att stackexchange.Redis används finns i [Konfigurera cacheklienterna](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Nästa steg
Läs om hur du använder fler premiumcachefunktioner.

* [Introduktion till Azure Cache för Redis Premium-nivå](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
