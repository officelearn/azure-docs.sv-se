---
title: Vad är Azure Cache for Redis?
description: Lär dig om Azure cache för Redis för att aktivera cachelagring av cacheminnen, cachelagring av innehåll, cachelagring av användarsessioner, jobb och meddelande köer och distribuerade transaktioner.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 26f6c8e3aceddc6f766bb43a1e384d761dee32bf
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631391"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure cache för Redis tillhandahåller ett InMemory-datalager baserat på [Redis](https://redis.io/)med öppen källkod. Redis förbättrar prestanda och skalbarhet för ett program som använder på backend-data lagrar mycket. Det går att bearbeta stora mängder programbegäran genom att lagra data som används ofta i Server minnet som kan skrivas till och läsas från snabbt. Redis ger en kritisk data lagrings lösning med låg latens och hög data flöde till moderna program.

Azure cache för Redis erbjuder Redis som en hanterad tjänst. Det ger säkra och dedikerade Redis-Server instanser och fullständig Redis API-kompatibilitet. Tjänsten drivs av Microsoft, som finns i Azure och är tillgänglig för alla program inom eller utanför Azure.

Azure cache för Redis kan användas som distribuerade data eller innehålls-cache, ett migreringsarkiv, en meddelande koordinator med mera. Den kan distribueras som fristående eller tillsammans med andra Azure Database-tjänster, till exempel Azure SQL eller Cosmos DB.

## <a name="key-scenarios"></a>Viktiga scenarier
Azure cache för Redis förbättrar program prestandan genom att stödja vanliga mönster för program arkitektur. Här följer exempel på några av de vanligaste:

| Mönster      | Beskrivning                                        |
| ------------ | -------------------------------------------------- |
| [Datacache](cache-web-app-cache-aside-leaderboard.md) | Databaserna är ofta för stora för att läsas in direkt i en cache. Det är vanligt att använda [cache-undan-](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) mönstret för att endast läsa in data i cachen vid behov. När systemet gör ändringar i data kan systemet också uppdatera cachen, som sedan distribueras till andra klienter. Dessutom kan systemet ange förfallo datum för data eller använda en princip för att utlösa data uppdateringar i cacheminnet.|
| [Innehålls-cache](cache-aspnet-output-cache-provider.md) | Många webb sidor genereras från mallar som använder statiskt innehåll, till exempel sidhuvuden, sid fötter, banderoller. Dessa statiska objekt bör inte ändras ofta. Om du använder en minnes intern cache får du snabb åtkomst till statiskt innehåll jämfört med backend-datalager. Det här mönstret minskar bearbetnings tiden och Server belastningen, vilket gör att webb servrar svarar mer. Det kan göra att du kan minska antalet servrar som behövs för att hantera belastningar. Azure cache för Redis tillhandahåller providern för Redis för utdata för att stödja det här mönstret med ASP.NET.|
| [Arkiv lag ring](cache-aspnet-session-state-provider.md) | Det här mönstret används ofta med shopping vagnar och andra användar historik data som ett webb program kanske vill associera med användarens cookies. Att lagra för stora datamängder i en cookie kan påverka prestanda negativt då storleken på cookie-filerna ökar och de skickas och verifieras med varje begäran. En typisk lösning använder cookien som en nyckel för att fråga data i en databas. Ett mycket snabbare sätt än att interagera med en hel relationsdatabas är att använda en minnesintern cache, som Azure Cache for Redis, till att associera information med en användare. |
| Jobb- och meddelandeköer | Program lägger ofta till uppgifter i en kö när de åtgärder som är kopplade till begäran tar tid att köra. Längre kör åtgärder placeras i kö för att bearbetas i följd, ofta av en annan server.  Den här metoden att skjuta upp arbete kallas för att placera uppgifter i kö. Azure cache för Redis tillhandahåller en distribuerad kö för att aktivera det här mönstret i ditt program.|
| Distribuerade transaktioner | Program kräver ibland en serie kommandon mot en backend-datalagringsplats för att köras som en enskild atomisk åtgärd. Alla kommandon måste slutföras, eller så måste alla återställas till det ursprungliga tillståndet. Azure cache för Redis stöder körning av en batch med-kommandon som en enskild [transaktion](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Redis-versioner

Azure cache för Redis stöder Redis-version 4. x och, som en för hands version, 6,0. Vi har gjort beslutet att hoppa över Redis 5,0 för att ta dig till den senaste versionen. Tidigare bevarade Azure cache för Redis endast en enda Redis-version. Det ger en nyare uppgradering av större versioner och minst en äldre stabil version går framåt. Du kan [välja vilken version som](cache-how-to-version.md) passar bäst för ditt program.

> [!NOTE]
> Redis 6,0 är för närvarande i för hands version – [kontakta oss](mailto:azurecache@microsoft.com) om du är intresse rad av. Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="service-tiers"></a>Tjänstnivåer
Azure Cache for Redis är tillgänglig på följande nivåer:

| Nivå | Beskrivning |
|---|---|
| Grundläggande | Cache för en enda nod. Den här nivån stöder flera minnes storlekar (250 MB-53 GB) och är perfekt för utveckling/testning och icke-kritiska arbets belastningar. Basic-nivån har inget service nivå avtal (SLA). |
| Standard | En replikerad cache i en primär/replik med två noder och en konfiguration som hanteras av Azure med ett [service avtal](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)med hög tillgänglighet. |
| Premium | Premium nivån är den företags färdiga nivån. Cacheminnen på Premium-nivå stöder fler funktioner och har högre datagenomflöde med kortare svarstider. Cacheminnen på Premium-nivå distribueras på kraftfullare maskinvara som ger bättre prestanda jämfört med Basic- och Standard-nivån. Den här fördelen innebär att data flödet för en cache med samma storlek blir högre i Premium jämfört med standard nivån. |

### <a name="feature-comparison"></a>Jämför funktioner
[Azure cache för Redis-prissättning](https://azure.microsoft.com/pricing/details/cache/) ger en detaljerad jämförelse mellan varje nivå. Följande tabell beskriver några av de funktioner som stöds på respektive nivå:

| Funktionsbeskrivning | Premium | Standard | Grundläggande |
| ------------------- | :-----: | :------: | :---: |
| [Serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis-datapersistens](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis-kluster](cache-how-to-premium-clustering.md) |✔|-|-|
| [Säkerhet via brandväggsregler](cache-configure.md#firewall) |✔|✔|✔|
| Kryptering under överföring |✔|✔|✔|
| [Förbättrad säkerhet och isolering med VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Import/Export](cache-how-to-import-export-data.md) |✔|-|-|
| [Schemalagda uppdateringar](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Geo-replikering](cache-how-to-geo-replication.md) |✔|-|-|
| [Starta om](cache-administration.md#reboot) |✔|✔|✔|

### <a name="choosing-the-right-tier"></a>Välja rätt nivå
Tänk på följande när du väljer en Azure-cache för Redis-nivån.

* **Minne**: Basic-och standard-nivåerna erbjuder 250 MB – 53 GB. Premium-nivån erbjuder upp till 1,2 TB (som ett kluster) eller 120 GB (icke-grupperad). Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/).
* **Nätverks prestanda**: om du har en arbets belastning som kräver stora data flöden erbjuder Premium-nivån mer bandbredd jämfört med standard eller Basic. I varje nivå har större cache-minne också större bandbredd på grund av den underliggande virtuella datorn som är värd för cachen. Mer information finns i [Azure cache för Redis-prestanda](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Genom strömning**: Premium nivån erbjuder maximalt tillgängligt data flöde. Om cache-servern eller-klienten når bandbredds gränserna kan du få tids gränser på klient sidan. Se tabellen nedan för mer information.
* **Hög tillgänglighet**: Azure cache för Redis garanterar att en standard-/Premium-cache är tillgänglig enligt vårt [service avtal](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Service avtalet täcker endast anslutningen till cachens slut punkter. Service avtalet omfattar inte skydd mot data förlust. Vi rekommenderar att du använder funktionen Redis data persistion på Premium-nivån för att öka återhämtningen mot data förlust.
* **Redis data persist**: Premium-nivån gör att du kan spara cache-data i ett Azure Storage-konto. I en Basic/standard-cache lagras alla data i minnet. Bakomliggande infrastruktur problem kan leda till potentiell data förlust. Vi rekommenderar att du använder funktionen Redis data persistion på Premium-nivån för att öka återhämtningen mot data förlust. Azure cache för Redis erbjuder alternativ för RDB och AOF (förhands granskning) i Redis persistence. Mer information finns i [så här konfigurerar du persistence för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).
* **Redis-kluster**: om du vill skapa cacheminnen som är större än 120 GB, eller för att Shard data över flera Redis-noder, kan du använda Redis-klustring, som är tillgängligt på Premium-nivån. Varje nod består av ett primärt/replik-cache-par för hög tillgänglighet. Mer information finns i [så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).
* **Förbättrad säkerhet och nätverks isolering**: Azure Virtual Network (VNet)-distribution ger förbättrad säkerhet och isolering för Azure-cachen för Redis, samt undernät, åtkomst kontroll principer och andra funktioner för att ytterligare begränsa åtkomst. Mer information finns i [så här konfigurerar du Virtual Network stöd för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md).
* **Konfigurera Redis**: i både standard-och Premium-nivån kan du konfigurera Redis för meddelanden om disk utrymme.
* **Maximalt antal klient anslutningar**: Premium-nivån erbjuder det maximala antalet klienter som kan ansluta till Redis, med ett högre antal anslutningar för större cacheminnen för storlek. Klustring ökar inte antalet anslutningar som är tillgängliga för en klustrad cache. Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/).
* **Dedikerad kärna för redis-server**: i Premium-nivån har alla cache-storlekar en dedikerad kärna för Redis. På Basic-/standard-nivåerna har C1-storleken och över en dedikerad kärna för Redis-servern.
* **Enkel tråd bearbetning**: Redis, efter design, använder bara en tråd för kommando bearbetning. Azure cache för Redis använder också ytterligare kärnor för I/O-bearbetning. Att ha fler kärnor ger bättre data flödes prestanda även om den inte kan producera linjär skalning. Dessutom levereras större VM-storlekar vanligt vis med högre bandbredds begränsningar än mindre. Det hjälper dig att undvika överbelastning i nätverket, vilket leder till timeout i ditt program.
* **Prestanda förbättringar**: cacheminnen på Premium-nivån distribueras på maskin vara som har snabbare processorer och ger bättre prestanda jämfört med Basic-eller standard-nivån. Cacheminnen på Premium-nivån har högre genomflöde och lägre fördröjning. Mer information finns i [Azure cache för Redis-prestanda](cache-planning-faq.md#azure-cache-for-redis-performance)

Du kan skala upp cacheminnet till en högre nivå när det har skapats. Det går inte att skala ned till en lägre nivå. Stegvisa skalningsanvisningar finns i [Skala Azure Cache for Redis](cache-how-to-scale.md) och [Automatisera en skalningsåtgärd](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Nästa steg
* [Snabbstart för ASP.NET-webbapp](cache-web-app-howto.md) Skapa en enkel ASP.NET-webbapp som använder en Azure Cache for Redis.
* [Snabbstart för .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Skapa en .NET-app som använder en Azure Cache for Redis.
* [Snabbstart för .NET Core](cache-dotnet-core-quickstart.md) Skapa en .NET Core-app som använder en Azure Cache for Redis.
* [Snabbstart för Node.js](cache-nodejs-get-started.md) Skapa en enkel Node.js-app som använder en Azure Cache for Redis.
* [Snabbstart för Java](cache-java-get-started.md) Skapa en enkel Java-app som använder en Azure Cache for Redis.
* [Snabbstart för Python](cache-python-get-started.md) Skapa en Python-app som använder en Azure Cache for Redis.
