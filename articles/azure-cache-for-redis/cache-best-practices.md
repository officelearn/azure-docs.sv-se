---
title: Metodtips för Azure Cache för Redis
description: Lär dig hur du använder Azure Cache för Redis effektivt genom att följa dessa bästa metoder.
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452467"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Metodtips för Azure Cache för Redis 
Genom att följa dessa bästa metoder kan du maximera prestandan och den kostnadseffektiva användningen av Azure Cache för Redis-instans.

## <a name="configuration-and-concepts"></a>Konfiguration och begrepp
 * **Använd Standard eller Premium-nivån för produktionssystem.**  Basic-nivån är en enskild nod system med ingen replikering av data och inget serviceavtal. Kan också använda minst en C1 cache.  C0 cacheminnen är avsedda för enkel utveckling och testning eftersom de har en delad CPU-kärna, lite minne och är felbenägna ”störningar från angränsande” problem.

 * **Kom ihåg att Redis är en InMemory-datalagret.**  [Den här artikeln](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) beskrivs vissa scenarier där data gå förlorade.

 * **Utveckla dina system så att den kan hantera anslutning signaler** [på grund av korrigeringar och redundans](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Konfigurera din [maxmemory-reserverade inställningen](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) att förbättra systemets svarstid** under hög belastning minne.  Den här inställningen är särskilt viktigt för skrivning arbetsbelastningar eller om du lagrar större värden (100 KB eller mer) i Redis.  Jag skulle rekommenderar att du börjar med 10% av storleken på cacheminnet och sedan öka om du har hög belastning. Se [några överväganden](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) när du väljer ett värde.

 * **Redis fungerar bäst med mindre värden**, så fundera över hackning upp större data i flera nycklar.  I [diskussionen Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), vissa förutsättningar visas att bör du noggrant.  Läs [i den här artikeln](cache-how-to-troubleshoot.md#large-requestresponse-size) för en exempel-problem som kan orsakas av stora värden.

 * **Leta upp din cache-instans och ditt program i samma region.**  Ansluta till en cache i en annan region kan avsevärt öka svarstiden och minska tillförlitlighet.  Du kan ansluta från utanför Azure, men rekommenderas inte *särskilt när du använder Redis som ett cacheminne*.  Om du använder Redis som bara ett nyckel/värde-lager, kanske inte det viktigaste här svarstid. 

 * **Återanvända anslutningar** -skapa nya anslutningar är dyr och ökar svarstiden, så återanvända anslutningar så mycket som möjligt. Om du väljer att skapa nya anslutningar, se till att Stäng gamla anslutningar innan du släpper dem (även i hanterade minne språk som .NET och Java).

 * **Konfigurera din klientbibliotek för att använda en *timeout för anslutning* på minst 15 sekunder**, ge systemtid att ansluta till och med högre CPU-villkor.  En liten timeout-värdet garanterar inte att anslutningen har upprättats i den aktuella tidsperioden.  Om något genereras går fel (hög klienten CPU, hög server CPU och så vidare) och sedan en kort timeout-värdet anslutningsförsöket misslyckas. Detta gör en felaktig situationen sämre.  I stället för att hjälpa, kortare tidsgränser förvärra problemet genom att tvinga systemet att starta om processen för försöker återansluta, vilket kan leda till en *Anslut -> misslyckas försök ->* loop. I allmänhet rekommenderar vi att du lämnar anslutningens tidsgräns på 15 sekunder eller högre. Det är bättre att låta din anslutningsförsök lyckas efter 15 och 20 sekunder än att den misslyckas snabbt endast för att försöka igen. En omförsöksslinga kan orsaka din avbrott på räcker längre än om du låter systemet bara ta längre från början.  
     > [!NOTE]
     > Den här vägledningen är specifik för den *anslutningsförsöket* och inte relaterade till dess att du är beredd att vänta tills en *åtgärden* som GET eller ange att slutföra.
 

 * **Undvika expansiva kommandon** -vissa redis åtgärder, som den [KEYS-kommandot](https://redis.io/commands/keys), är *mycket* dyrt och bör inte användas.  Mer information finns i [några överväganden kring expansiva kommandon](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>Minneshantering
Det finns flera saker som är relaterade till minnesanvändning i din Redis server-instans som du kan använda.  Här är några:

 * **Välj en [avlägsningsprincipen](https://redis.io/topics/lru-cache) som passar ditt program.**  Standardprincipen för Azure Redis är *beräkningsbara lru*, vilket innebär att endast de nycklar som har en TTL-värde set vara berättigade till borttagning.  Om inga nycklar har ett TTL-värde kan avlägsna inte systemet några nycklar.  Om du vill att tillåta valfri tangent för att tas bort om minnet är hårt belastat så kan du överväga de *allkeys lru* princip.

 * **Ange värdet för förfallotid för dina nycklar.**  Detta tar bort nycklar proaktivt i stället för att vänta tills det finns minnesbelastning.  När borttagning startar på grund av minnestryck, kan det orsaka ytterligare belastning på servern.  Mer information finns i dokumentationen för den [upphör att gälla](https://redis.io/commands/expire) och [ExpireAt](https://redis.io/commands/expireat) kommandon.
 
## <a name="client-library-specific-guidance"></a>Specifika anvisningar för klient-biblioteket
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - vilken klient ska jag använda?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Sallat (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Provider av Asp.Net-sessionstillstånd](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>När är det bra att försöka igen?
Det finns tyvärr inget enkelt svar.  Varje program måste bestämma vilka åtgärder som kan omprövas och som kan inte.  Varje åtgärd har olika krav och kommunikation mellan viktiga beroenden.  Här följer några saker som du kan överväga att:

 * Du kan hämta klientsidan fel trots att Redis har kört kommandot du ombedd att den kan köras.  Exempel:
     - Tidsgränser är ett koncept för klientsidan.  Om åtgärden nådde servern, körs servern kommandot även om klienten inte längre att vänta.  
     - När ett fel uppstår i socket-anslutningen, går det inte att veta om åtgärden faktiskt kördes på servern.  Till exempel kan anslutning felet inträffa när begäran har bearbetats av servern men svaret tas emot av klienten.
 *  Hur appen reagerar om jag råkar köra två gånger på samma gång?  Exempelvis vad händer om jag öka ett heltal två gånger i stället för bara en gång?  Mitt program skriver till samma nyckel från flera platser?  Vad händer om min logik för omprövning skriver över ett värde som anges av några andra delen av min app?

Om du vill testa hur din kod fungerar under felvillkor kan du använda den [starta om funktionen](cache-administration.md#reboot). På så sätt kan du se hur anslutningen signaler påverkar ditt program.

## <a name="performance-testing"></a>Prestandatestning
 * **Starta med hjälp av `redis-benchmark.exe`**  få en bild för möjliga dataflöde och fördröjning innan du skriver din egen perf testar.  Redis-benchmark-dokumentationen kan vara [finns här](http://redis.io/topics/benchmarks).  Observera att redis-benchmark inte stöder SSL, så du behöver [aktivera icke-SSL-porten via portalen](cache-configure.md#access-ports) innan du kör testet.  [En kompatibel windows-version av redis-benchmark.exe finns här](https://github.com/MSOpenTech/redis/releases)
 * Klienten VM används för testning bör vara **i samma region** som din Redis cache-instans.
 * **Vi rekommenderar att du använder Virtuella Dv2-serien** för klienten som de har bättre maskinvara och ger bäst resultat.
 * Kontrollera att klienten har VM som du använder **minst lika mycket beräknings- och bandbredd* som cache testas. 
 * **Aktivera VRSS** på klientdatorn om du använder Windows.  [Mer information finns här](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Powershell-exempelskript:
     >PowerShell - ExecutionPolicy obegränsad Enable-NetAdapterRSS-namn (Get-NetAdapter). Namn 
     
 * **Överväg att använda Redis-instanser för Premium-nivån**.  Dessa cache-storlekar har bättre nätverksfördröjning och dataflöde eftersom de körs på bättre maskinvara för både CPU och nätverk.
 
     > [!NOTE]
     > Vår observerade prestandaresultat är [publiceras här](cache-faq.md#azure-cache-for-redis-performance) referens.   Tänk också på att SSL/TLS lägger till vissa omkostnader, så att du kan få olika svarstider och/eller dataflöde om du använder transportkryptering.
 
### <a name="redis-benchmark-examples"></a>Redis-Benchmark-exempel
**Förtest installationsprogrammet**: Detta förbereder cache-instans med data som krävs för svarstid och dataflöde som testar kommandon som visas nedan.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**Att testa svarstid**: Det här kommandot testar GET-begäranden med en nyttolast på 1 kB.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Så här testar dataflöde:** Här används Pipelined GET-begäranden med 1 k nyttolast.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
