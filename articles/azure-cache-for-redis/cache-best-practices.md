---
title: Bästa praxis för Azure Cache for Redis
description: Lär dig hur du använder din Azure Cache för Redis effektivt genom att följa dessa metodtips.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 105a3996753a1d1c2d71846cc8bad574e4498acf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478619"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Bästa praxis för Azure Cache for Redis 
Genom att följa dessa metodtips kan du maximera prestanda och kostnadseffektiv användning av din Azure-cache för Redis-instans.

## <a name="configuration-and-concepts"></a>Konfiguration och koncept
 * **Använd standard- eller Premium-nivå för produktionssystem.**  Basic-nivån är ett enda nodsystem utan datareplikering och inget serviceavtal. Använd också minst ett C1-cacheminne.  C0-cacheminnen är avsedda för enkla dev/test-scenarier eftersom de har en delad CPU-kärna, lite minne och är benägna att "bullriga granne" problem.

 * **Kom ihåg att Redis är ett datalager i minnet.**  [I den här artikeln](cache-troubleshoot-data-loss.md) beskrivs några scenarier där dataförlust kan uppstå.

 * **Utveckla ditt system så att det kan hantera anslutning blips** [på grund av patchning och redundans](cache-failover.md).

 * **Konfigurera din [maxmemory-reserverade inställning](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) för att förbättra systemets svarstider** under minnestrycksförhållanden.  En tillräcklig reservationsinställning är särskilt viktig för skrivtunna arbetsbelastningar eller om du lagrar större värden (100 kB eller mer) i Redis. Du bör börja med 10% av storleken på din cache och öka denna procentsats om du har skrivtunga laster.

 * **Redis fungerar bäst med mindre värden,** så överväg att hacka upp större data i flera nycklar.  I [den här Redis-diskussionen](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)visas vissa överväganden som du bör överväga noga.  Läs [den här artikeln](cache-troubleshoot-client.md#large-request-or-response-size) för ett exempel problem som kan orsakas av stora värden.

 * **Leta reda på cacheinstansen och programmet i samma region.**  Om du ansluter till en cache i en annan region kan latensen öka och tillförlitligheten minska avsevärt.  Även om du kan ansluta från utanför Azure, rekommenderas det inte *särskilt när du använder Redis som en cache*.  Om du använder Redis som bara en nyckel/ värde butik, latens kanske inte är det primära problemet. 

 * **Återanvända anslutningar.**  Att skapa nya anslutningar är dyrt och ökar svarstiden, så återanvända anslutningar så mycket som möjligt. Om du väljer att skapa nya anslutningar måste du stänga de gamla anslutningarna innan du släpper dem (även på hanterade minnesspråk som .NET eller Java).

 * **Konfigurera klientbiblioteket så att det använder en *anslutningstidsutskrift* på minst 15 sekunder,** vilket ger systemet tid att ansluta även under högre CPU-förhållanden.  Ett litet timeout-värde för anslutningen garanterar inte att anslutningen upprättas inom den tidsramen.  Om något går fel (hög klient CPU, hög server CPU, och så vidare), då en kort anslutning timeout värde kommer att orsaka anslutningen försöket att misslyckas. Detta beteende gör ofta en dålig situation värre.  I stället för att hjälpa, kortare timeout förvärra problemet genom att tvinga systemet att starta om processen för att försöka återansluta, vilket kan leda till en *ansluta -> misslyckas -> försök* loop. Vi rekommenderar i allmänhet att du lämnar din anslutning Timeout på 15 sekunder eller högre. Det är bättre att låta anslutningsförsöket lyckas efter 15 eller 20 sekunder än att det misslyckas snabbt bara för att försöka igen. En sådan retry loop kan orsaka ditt avbrott att pågå längre än om du låter systemet bara ta längre tid från början.  
     > [!NOTE]
     > Den här vägledningen är specifik för *anslutningsförsöket* och inte relaterad till den tid du är villig att vänta på att en *åtgärd* som GET eller SET ska slutföras.
 
 * **Undvik dyra operationer** - Vissa Redis-åtgärder, som [keys-kommandot,](https://redis.io/commands/keys) är *mycket* dyra och bör undvikas.  Mer information finns i några överväganden kring [långvariga kommandon](cache-troubleshoot-server.md#long-running-commands)

 * **Använd TLS-kryptering** - Azure Cache för Redis kräver TLS-krypterad kommunikation som standard.  TLS-versionerna 1.0, 1.1 och 1.2 stöds för närvarande.  TLS 1.0 och 1.1 är dock på väg att utfasa branschomfattande, så använd TLS 1.2 om det alls är möjligt.  Om klientbiblioteket eller verktyget inte stöder TLS kan du aktivera okrypterade anslutningar [via Azure-portalen](cache-configure.md#access-ports) eller [hanterings-API:erna](https://docs.microsoft.com/rest/api/redis/redis/update).  I sådana fall där krypterade anslutningar inte är möjliga rekommenderas att cache- och klientprogrammet placeras i ett virtuellt nätverk.  Mer information om vilka portar som används i scenariot för virtuell nätverkscache finns i den här [tabellen](cache-how-to-premium-vnet.md#outbound-port-requirements).
 
## <a name="memory-management"></a>Minneshantering
Det finns flera saker som rör minnesanvändning i Redis-serverinstansen som du kanske vill ta hänsyn till.  Här är några:

 * **Välj en [vräkningsprincip](https://redis.io/topics/lru-cache) som fungerar för ditt program.**  Standardprincipen för Azure Redis är *flyktig-lru*, vilket innebär att endast nycklar som har en TTL-värdeuppsättning kan v startas.  Om inga nycklar har ett TTL-värde kommer systemet inte att ta bort några nycklar.  Om du vill att systemet ska tillåta att alla nycklar ska vräkas om de är under minnestryck, kanske du vill överväga *allkeys-lru-policyn.*

 * **Ange ett utgångsvärde på dina nycklar.**  En förfallodatum tar bort nycklar proaktivt i stället för att vänta tills minnestrycket finns.  När vräkning startar på grund av minnestrycket kan det orsaka ytterligare belastning på servern.  Mer information finns i dokumentationen för [kommandona EXPIRE](https://redis.io/commands/expire) [OCH EXPIREAT.](https://redis.io/commands/expireat)
 
## <a name="client-library-specific-guidance"></a>Specifik vägledning för klientbibliotek
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - Vilken klient ska jag använda?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Sallad (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net leverantör av sessionstillstånd](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>När är det säkert att försöka igen?
Tyvärr finns det inget enkelt svar.  Varje program måste bestämma vilka åtgärder som kan göras om och vilka som inte kan göras.  Varje åtgärd har olika krav och internyckelberoenden.  Här är några saker du kan tänka dig:

 * Du kan få fel på klientsidan även om Redis körde kommandot du bad det att köra.  Ett exempel:
     - Timeout är ett klientkoncept.  Om åtgärden nådde servern kör servern kommandot även om klienten ger upp att vänta.  
     - När ett fel uppstår på socketanslutningen går det inte att veta om åtgärden verkligen kördes på servern.  Anslutningsfelet kan till exempel inträffa efter att servern har bearbetat begäran men innan klienten tar emot svaret.
 *  Hur reagerar mitt program om jag råkar köra samma åtgärd två gånger?  Vad händer till exempel om jag ökar ett heltal två gånger i stället för en gång?  Är min ansökan skriver till samma nyckel från flera ställen?  Vad händer om logiken för återförsök skriver över ett värde som angetts av någon annan del av min app?

Om du vill testa hur koden fungerar under felförhållanden kan du överväga att använda [funktionen Starta om](cache-administration.md#reboot). Omstart kan du se hur anslutningen blips påverkar ditt program.

## <a name="performance-testing"></a>Prestandatestning
 * **Börja med `redis-benchmark.exe` ** att använda för att få en känsla för eventuellt dataflöde / latens innan du skriver din egen perf tester.  Redis-benchmark dokumentation [finns här](https://redis.io/topics/benchmarks).  Observera att redis-benchmark inte stöder TLS, så du måste [aktivera icke-TLS-porten via portalen](cache-configure.md#access-ports) innan du kör testet.  [En windows-kompatibel version av redis-benchmark.exe finns här](https://github.com/MSOpenTech/redis/releases)
 * Klientens virtuella dator som används för testning bör vara **i samma region** som din Redis-cacheinstans.
 * **Vi rekommenderar att du använder Dv2 VM-serien** för din klient eftersom de har bättre hårdvara och ger bästa resultat.
 * Kontrollera att klientens virtuella dator du använder har **minst lika mycket beräkning och bandbredd* som cachen som testas. 
 * **Aktivera VRSS** på klientmaskinen om du använder Windows.  [Se här för mer information](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Exempel powershell-skript:
     >PowerShell -ExecutionPolicy Obegränsad Enable-NetAdapterRSS -Name ( Get-NetAdapter). Namn 
     
 * **Överväg att använda Premium-nivå Redis-instanser**.  Dessa cachestorlekar har bättre nätverksfördröjning och dataflöde eftersom de körs på bättre maskinvara för både CPU och Nätverk.
 
     > [!NOTE]
     > Våra observerade resultat [publiceras här](cache-faq.md#azure-cache-for-redis-performance) för din referens.   Tänk också på att SSL/TLS lägger till vissa omkostnader, så att du kan få olika svarstider och/eller dataflöde om du använder transportkryptering.
 
### <a name="redis-benchmark-examples"></a>Exempel på Redis-Benchmark
**Förtestinställning:** Förbered cacheinstansen med data som krävs för svarstids- och dataflödestestkommandona nedan.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**Så här testar du svarstid:** Testa GET-begäranden med en 1k-nyttolast.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Så här testar du dataflödet:** Pipelined GET-begäranden med 1k nyttolast.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50
