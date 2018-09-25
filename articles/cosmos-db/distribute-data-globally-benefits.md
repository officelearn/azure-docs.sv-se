---
title: Viktiga fördelar med global distribution i Azure Cosmos DB
description: Läs mer om Azure Cosmos DB multimaster, key fördelarna som erbjuds av geo-replikering, multimaster och Använd fall där det är till hjälp.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968361"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>Distribuera data globalt med Azure Cosmos DB

Den här artikeln beskriver de främsta fördelarna med att distribuera data globalt och vissa realtidsscenarion där globala Datadistribution behövs.

## <a name="key-benefits"></a>Viktiga fördelar

Följande är de främsta fördelarna som är tillgängligt för konton som utnyttjar funktioner för distribution av Azure Cosmos DB globala data:

* **Ensiffrig svarstid** – Azure Cosmos DB levererar < 10 ms läsa och skriva svarstid på 99: e percentilen, garanterat av [med ekonomisk uppbackning serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **5 – 9 tillgänglighet** – Azure Cosmos DB erbjuder 99,999% läsa och skriva tillgängliga, ges av [med ekonomisk uppbackning serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Flexibla konfliktlösning** – för kunder som utnyttjar flera huvudservrar funktioner, Azure Cosmos DB erbjuder tre lägen för att undvika konflikter för att säkerställa att globala dataintegritet och konsekvens.

* **Finjusterbar konsekvens** – Azure Cosmos DB stöder 5 olika [konsekvensnivåer](consistency-levels.md) med global distribution i åtanke med stöd för allt utom stark konsekvens för konton med hantering av flera funktioner.

* **Implicit feltolerans** -med data som replikeras över flera regioner, kan program dra nytta av hög feltolerans mot regionala avbrott.

## <a name="use-cases"></a>Användningsfall

Här är bara ett litet antal scenarier som kan dra nytta av de globalt distribution funktioner för Azure Cosmos DB.

* **Appar för sociala medier** – sociala media-program som kräver låg latens, hög tillgänglighet och skalbarhet för att tillhandahålla en bra upplevelse för användare finns i hela världen.

* **IoT** -Geo-distribuerad edge-distributioner som ofta behöver spåra time series-data från flera platser. Varje enhet kan vara homed till den närmaste regionen. När enheter flyttar till olika platser, kan du rehomed enheterna för att skriva till den närmast tillgängliga regionen.

* **E-handel** -E-handel kräver mycket låg fördröjning samt hög tillgänglighet. GEO-replikering av data med läsningar och skrivningar placerar data som är närmast användarna, ökar svarstiden för program. Tillgänglighet för både läsningar och skrivningar i flera regioner ger högre tillgänglighet.

* **Identifiering av bedrägerier/avvikelser** -ofta program som övervakar användaraktivitet eller kontoaktivitet distribueras globalt och måste hålla reda på flera händelser samtidigt om du vill uppdatera resultat för att hålla risken mått direkt.

* **Avläsning av** – inventering och reglerar användning (till exempel API-anrop, transaktioner/sekund, minut används) kan implementeras globalt enkelt med Azure Cosmos DB multimaster. Inbyggda konfliktlösning säkerställer båda noggrannhet för förordning i realtid.

## <a name="next-steps"></a>Nästa steg  

I den här artikeln har du lärt dig om viktiga fördelar och användningsområden för globala data distribution funktionerna i Azure Cosmos DB. Nu ska titta på följande resurser:

* [Hur Azure Cosmos DB tillhandahåller nyckelfärdig global distribution](distribute-data-globally-turnkey.md)

* [Så här konfigurerar du global databasreplikering för Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [Så här aktiverar du multimaster för Azure Cosmos DB-konton](enable-multi-master.md)

* [Så här automatisk och manuell redundans fungerar i Azure Cosmos DB](regional-failover.md)

* [Förstå konfliktlösning i Azure Cosmos DB](multi-master-conflict-resolution.md)

* [med hjälp av multimaster med öppen källkod NoSQL-databaser](multi-master-oss-nosql.md)
