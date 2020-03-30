---
title: Elastisk skala med Cassandra API i Azure Cosmos DB
description: Lär dig mer om de alternativ som finns för att skala ett Azure Cosmos DB Cassandra API-konto och deras fördelar/nackdelar
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474687"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Skala ett Azure Cosmos DB Cassandra API-konto elastiskt

Det finns en mängd olika alternativ för att utforska den elastiska karaktären hos Azure Cosmos DB API för Cassandra. För att förstå hur du skalar effektivt i Azure Cosmos DB är det viktigt att förstå hur du etablerar rätt mängd begärandeenheter (RU/s) för att ta hänsyn till prestandakraven i ditt system. Mer information om begäranheter finns i artikeln [för begäranheter.](request-units.md) 

För Cassandra API kan du hämta avgiften för begäranhet för enskilda frågor med hjälp av [.NET- och Java-SDK:er](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api). Detta är användbart för att bestämma hur mycket RU /s du behöver etablera i tjänsten.

![Databasåtgärder förbrukar begärandeenheter](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Begränsning av hanteringshastighet (429 fel)

Azure Cosmos DB returnerar hastighetsbegränsade (429) fel om klienter förbrukar mer resurser (RU/s) än det belopp som du har etablerat. Cassandra-API:et i Azure Cosmos DB översätter dessa undantag till överbelastade fel i Cassandra-inbyggda protokollet. 

Om systemet inte är känsligt för svarstid kan det vara tillräckligt att hantera hastighetsbegränsningen för dataflöde med hjälp av återförsök. Se [java-kodexemplet](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) för hur du hanterar hastighetsbegränsning transparent med hjälp av [Azure Cosmos DB-tillägget](https://github.com/Azure/azure-cosmos-cassandra-extensions) för [Cassandra-återförsök](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) i Java. Du kan också använda [Spark-tillägget](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) för att hantera hastighetsbegränsning.

## <a name="manage-scaling"></a>Hantera skalning

Om du behöver minimera svarstiden finns det ett spektrum av alternativ för att hantera skalning och etablering av dataflöde (Ru: er) i Cassandra API:

* [Manuellt med hjälp av Azure-portalen](#use-azure-portal)
* [Programmässigt med hjälp av kontrollplansfunktionerna](#use-control-plane)
* [Programmässigt med hjälp av CQL-kommandon med en specifik SDK](#use-cql-queries)
* [Dynamiskt genom att använda Autopilot](#use-autopilot)

I följande avsnitt förklaras för- och nackdelarna med varje tillvägagångssätt. Du kan sedan bestämma dig för den bästa strategin för att balansera systemets skalningsbehov, den totala kostnaden och effektivitetsbehoven för din lösning.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Använda Azure Portal

Du kan skala resurserna i Azure Cosmos DB Cassandra API-konto med hjälp av Azure-portalen. Mer information finns i artikeln om [Etableringsdataflöde på behållare och databaser](set-throughput.md). I den här artikeln beskrivs de relativa fördelarna med att ange dataflöde på antingen [databas-](set-throughput.md#set-throughput-on-a-database) eller [behållarnivå](set-throughput.md#set-throughput-on-a-container) i Azure-portalen. Termerna "databas" och "behållare" som nämns i dessa artiklar mappas till "keyspace" respektive "tabell" för Cassandra API.

Fördelen med den här metoden är att det är ett enkelt nyckelfärdigt sätt att hantera dataflödeskapacitet i databasen. Nackdelen är dock att din metod för skalning i många fall kan kräva att vissa automatiseringsnivåer är både kostnadseffektiva och högpresterande. I nästa avsnitt beskrivs relevanta scenarier och metoder.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Använd kontrollplanet

Azure Cosmos DB:s API för Cassandra ger möjlighet att justera dataflödet programmässigt med hjälp av våra olika kontrollplansfunktioner. Mer information och information finns i artiklarna [Azure Resource Manager,](manage-cassandra-with-resource-manager.md) [Powershell](powershell-samples-cassandra.md)och [Azure CLI.](cli-samples-cassandra.md)

Fördelen med den här metoden är att du kan automatisera uppskalning eller nedförd av resurser baserat på en timer för att ta hänsyn till toppaktivitet eller perioder med låg aktivitet. Ta en titt på vårt exempel [här](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) för hur du utför detta med Hjälp av Azure Functions och Powershell.

En nackdel med den här metoden kan vara att du inte kan svara på oförutsägbara föränderliga skalningsbehov i realtid. I stället kan du behöva utnyttja programkontexten i ditt system, på klient/SDK-nivå eller använda [Autopilot](provision-throughput-autopilot.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Använda CQL-frågor med en specifik SDK

Du kan skala systemet dynamiskt med kod genom att köra [CQL ALTER-kommandona](cassandra-support.md#keyspace-and-table-options) för den angivna databasen eller behållaren.

Fördelen med den här metoden är att den gör att du kan svara på skalbehov dynamiskt och på ett anpassat sätt som passar ditt program. Med den här metoden kan du fortfarande utnyttja standard-RU/s-avgifter och priser. Om systemets skalbehov oftast är förutsägbara (cirka 70 % eller mer) kan det vara en mer kostnadseffektiv metod för automatisk skalning att använda Autopilot. Nackdelen med detta tillvägagångssätt är att det kan vara ganska komplicerat att genomföra återförsök medan hastighetsbegränsning kan öka latensen.

## <a name="use-autopilot"></a><a id="use-autopilot"></a>Använd autopilot

Förutom manuellt eller programmatiskt sätt att etablera dataflöde kan du också konfigurera Azure cosmos-behållare i autopilotläge. Autopilotläget skalas automatiskt och omedelbart till dina förbrukningsbehov inom angivna RU-intervall utan att kompromissa med SLA.Autopilot mode will automatically and instantly scale to your consumption needs within specified RU ranges without compromising SLAs. Mer information finns i artikeln [Skapa Azure Cosmos-behållare och databaser i autopilotläge.](provision-throughput-autopilot.md)

Fördelen med den här metoden är att det är det enklaste sättet att hantera skalningsbehoven i ditt system. Det garanterar att inte tillämpa hastighetsbegränsning **inom de konfigurerade RU-intervallen.** Nackdelen är att om skalningsbehoven i ditt system är förutsägbara kan Autopilot vara ett mindre kostnadseffektivt sätt att hantera dina skalningsbehov än att använda de skräddarsydda kontrollplan eller SDK-nivåmetoder som nämns ovan.

## <a name="next-steps"></a>Nästa steg

- Kom igång med [skapa ett Cassandra API-konto, databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program
