---
title: Vad är Azure Cache for Redis?
description: Lär dig om Azure cache för Redis för att aktivera cachelagring av cacheminnen, cachelagring av innehåll, cachelagring av användarsessioner, jobb och meddelande köer och distribuerade transaktioner.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 09a7a28716e437bab71c7386bd332712a4d192dd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196376"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis

Azure cache för Redis tillhandahåller ett InMemory-datalager baserat på [Redis](https://redis.io/)med öppen källkod. När Redis används som cache förbättrar prestanda och skalbarhet för system som förlitar sig på Server dels data lager. Prestanda förbättras genom kopiering av data som används ofta till snabb lagring som finns nära programmet. Med Azure cache för Redis finns den här snabba lagringen i minnet i stället för att läsas in från disk av en databas.

Azure cache för Redis kan användas som distribuerad datacache, ett migreringsarkiv och en meddelande koordinator. Genom att utnyttja Redis-motorns korta svarstider och höga dataflöde förbättras programprestanda.

Azure cache för Redis ger åtkomst till en säker, dedikerad Redis-cache. Den hanteras av Microsoft, som finns i Azure och är tillgänglig för alla program inom eller utanför Azure.

## <a name="using-azure-cache-for-redis"></a>Använda Azure cache för Redis

Azure cache för Redis förbättrar program prestandan genom att stödja vanliga mönster för program arkitektur. Här följer exempel på några av de vanligaste:

| Mönster      | Beskrivning                                        |
| ------------ | -------------------------------------------------- |
| [Cachereservera](cache-web-app-cache-aside-leaderboard.md) | Databaserna är ofta för stora för att läsas in direkt i en cache. Det är vanligt att använda [cache-undan-](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) mönstret för att endast läsa in data i cachen vid behov. När systemet gör ändringar i data kan systemet också uppdatera cachen, som sedan distribueras till andra klienter. Dessutom kan systemet ange förfallo datum för data eller använda en princip för att utlösa data uppdateringar i cacheminnet.|
| [Cachelagring av innehåll](cache-aspnet-output-cache-provider.md) | Många webb sidor genereras från mallar som använder statiskt innehåll, till exempel sidhuvuden, sid fötter, banderoller. Dessa statiska objekt bör inte ändras ofta. Om du använder en minnes intern cache får du snabb åtkomst till statiskt innehåll jämfört med backend-datalager. Det här mönstret minskar bearbetnings tiden och Server belastningen, vilket gör att webb servrar svarar mer. Det kan göra att du kan minska antalet servrar som behövs för att hantera belastningar. Azure cache för Redis tillhandahåller providern för Redis för utdata för att stödja det här mönstret med ASP.NET.|
| [Cachelagring av användarsessioner](cache-aspnet-session-state-provider.md) | Det här mönstret används ofta med shopping vagnar och andra användar historik data som ett webb program kanske vill associera med användarens cookies. Att lagra för stora datamängder i en cookie kan påverka prestanda negativt då storleken på cookie-filerna ökar och de skickas och verifieras med varje begäran. En typisk lösning använder cookien som en nyckel för att fråga data i en databas. Ett mycket snabbare sätt än att interagera med en hel relationsdatabas är att använda en minnesintern cache, som Azure Cache for Redis, till att associera information med en användare. |
| Jobb- och meddelandeköer | Program lägger ofta till uppgifter i en kö när de åtgärder som är kopplade till begäran tar tid att köra. Längre kör åtgärder placeras i kö för att bearbetas i följd, ofta av en annan server.  Den här metoden att skjuta upp arbete kallas för att placera uppgifter i kö. Azure cache för Redis tillhandahåller en distribuerad kö för att aktivera det här mönstret i ditt program.|
| Distribuerade transaktioner | Program kräver ibland en serie kommandon mot en backend-datalagringsplats för att köras som en enskild atomisk åtgärd. Alla kommandon måste slutföras, eller så måste alla återställas till det ursprungliga tillståndet. Azure cache för Redis stöder körning av en batch med-kommandon som en enskild [transaktion](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Erbjudanden för Azure Cache for Redis

Azure Cache for Redis är tillgänglig på följande nivåer:

| Nivå | Beskrivning |
|---|---|
Basic | Cache för en enda nod. Den här nivån stöder flera minnes storlekar (250 MB-53 GB) och är perfekt för utveckling/testning och icke-kritiska arbets belastningar. Basic-nivån har inget serviceavtal (SLA) |
| Standard | En replikerad cache i en primär/sekundär konfiguration med två noder som hanteras av Azure med ett service avtal med hög tillgänglighet (99,9%) |
| Premium | Premium nivån är den företags färdiga nivån. Cacheminnen på Premium-nivå stöder fler funktioner och har högre datagenomflöde med kortare svarstider. Cacheminnen på Premium-nivå distribueras på kraftfullare maskinvara som ger bättre prestanda jämfört med Basic- och Standard-nivån. Den här fördelen innebär att data flödet för en cache med samma storlek blir högre i Premium jämfört med standard nivån. |

> [!TIP]
> Mer information om storlek, dataflöde och bandbredd för cacheminnen på Premium-nivå finns i [Vanliga frågor och svar om Azure Cache for Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Du kan skala upp cacheminnet till en högre nivå när det har skapats. Det går inte att skala ned till en lägre nivå. Stegvisa skalningsanvisningar finns i [Skala Azure Cache for Redis](cache-how-to-scale.md) och [Automatisera en skalningsåtgärd](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Jämför funktioner

En detaljerad jämförelse av varje nivå finns på sidan med [prissättning för Azure Cache for Redis](https://azure.microsoft.com/pricing/details/cache/). Följande tabell beskriver några av de funktioner som stöds på respektive nivå:

| Funktionsbeskrivning | Premium | Standard | Basic |
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

## <a name="next-steps"></a>Nästa steg

* [Snabbstart för ASP.NET-webbapp](cache-web-app-howto.md) Skapa en enkel ASP.NET-webbapp som använder en Azure Cache for Redis.
* [Snabbstart för .NET](cache-dotnet-how-to-use-azure-redis-cache.md) Skapa en .NET-app som använder en Azure Cache for Redis.
* [Snabbstart för .NET Core](cache-dotnet-core-quickstart.md) Skapa en .NET Core-app som använder en Azure Cache for Redis.
* [Snabbstart för Node.js](cache-nodejs-get-started.md) Skapa en enkel Node.js-app som använder en Azure Cache for Redis.
* [Snabbstart för Java](cache-java-get-started.md) Skapa en enkel Java-app som använder en Azure Cache for Redis.
* [Snabbstart för Python](cache-python-get-started.md) Skapa en Python-app som använder en Azure Cache for Redis.
