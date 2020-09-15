---
title: Våren data Azure Cosmos DB v3 för SQL API-viktig information och resurser
description: Lär dig mer om våren-data Azure Cosmos DB v3 för SQL API, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4b2d474f25209034034db092ca971bff6b78d73a
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068752"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Våren data Azure Cosmos DB v3 for Core (SQL) API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Våren data v2](sql-api-sdk-java-spring-v2.md)
> * [Våren data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-anslutningsprogram](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resursprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

Med vår data Azure Cosmos DB version 3 för Core (SQL) kan utvecklare använda Azure Cosmos DB i vår program. Våren data Azure Cosmos DB visar data gränssnittet våren för att manipulera databaser och samlingar, arbeta med dokument och skicka frågor. Både synkronisering och asynkron (reaktiv) API: er stöds i samma maven-artefakt. 

Våren data Azure Cosmos DB är beroende av våren data Framework. Azure Cosmos DB SDK-teamet släpper maven-artefakter för våren data version 2,2 och 2,3.

[Våren-ramverket](https://spring.io/projects/spring-framework) är en programmerings-och konfigurations modell som fören klar utveckling av Java-program. Våren effektiviserar "rör koppling" av program genom att använda beroende inmatning. Många utvecklare liknar våren eftersom det gör det enklare att skapa och testa program. [Våren Boot](https://spring.io/projects/spring-boot) utökar denna hantering av rör med ett öga mot webb program och utveckling av mikrotjänster. [Våren data](https://spring.io/projects/spring-data) är en programmerings modell och ett ramverk för att komma åt data lager som Azure Cosmos dB från kontexten för ett våren-eller våren Boot-program. 

Du kan använda vår data Azure Cosmos DB i dina [Azure våren Cloud](https://azure.microsoft.com/services/spring-cloud/) -program.

> [!IMPORTANT]  
> Den här viktig information gäller för version 3 av vår data Azure Cosmos DB. [Viktig information om version 2 finns här](sql-api-sdk-java-spring-v2.md). 
>
> Våren data Azure Cosmos DB stöder bara SQL-API: et.
>
> I de här artiklarna finns information om våren-data på andra Azure Cosmos DB API: er:
> * [Våren-data för Apache Cassandra med Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Fjädra data MongoDB med Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Fjädra data Gremlin med Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Börja här

# <a name="explore"></a>[Utforska](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Dessa flikar innehåller exempel på grundläggande våren-data Azure Cosmos DB.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Konfigurera beroenden

Det finns två maven-artefakter för våren data Azure Cosmos DB version 3.

- Artefakt som är beroende av våren data Framework version 2,2:
  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-2-2-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

- Artefakt som är beroende av våren data Framework version 2,3:
  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-2-3-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Anslut](#tab/connect)

### <a name="connect"></a>Ansluta

Ange Azure Cosmos DB konto-och behållar information. Våren data Azure Cosmos DB skapar automatiskt klienten och ansluter till behållaren.

[Application. Properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Dok OPS](#tab/docs)

### <a name="document-operations"></a>Dokument åtgärder

[Skapa](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[Ta bort](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[Query](#tab/queries)

[Fråga](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="resources"></a>Resurser

| Resurs | Våren data Framework 2,2 | Våren data Framework 2,3 |
|---|---|
| **SDK-hämtning** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Bidra till SDK: n** | [Våren data Azure Cosmos DB lagrings platsen på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Våren data Azure Cosmos DB lagrings platsen på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Självstudier**| [Själv studie kurs om vår data Azure Cosmos DB GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Själv studie kurs om vår data Azure Cosmos DB GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>Versions historik

### <a name="300-beta2-unreleased"></a>3.0.0 – beta. 2 (frisläppt)

### <a name="300-beta1-august-17-2020"></a>3.0.0 – beta. 1 (17 augusti 2020)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterar grupp-ID till `com.azure` .
* Uppdaterar artefakt-ID till `azure-spring-data-2-3-cosmos` .
* Uppdaterar Azure-Cosmos SDK-beroendet till `4.3.2-beta.2` .
* Lägger till stöd för gransknings enheter: automatisk hantering av `createdBy` , `createdDate` , `lastModifiedBy` och `lastModifiedDate` kommenterade fält.
* Lägger till `@GeneratedValue` antecknings stöd för automatisk ID-generering för ID-fält av `String` typen.
* Lägger till konfigurations stöd för flera databaser för enskilda Azure Cosmos DB-konton med flera databaser och flera Azure Cosmos DB-konton med flera databaser.
* Lägger till stöd för `@Version` anteckning i valfritt sträng fält.
* Uppdaterar Sync API retur typer till `Iterable` typer i stället för `List` .
* Exponerar `CosmosClientBuilder` från Azure Cosmos DB SDK som fjädrande bönor till `@Configuration` klassen.
* Uppdateringar `CosmosConfig` som innehåller frågans mått och implementering av Response Diagnostics processor.
* Lägger till stöd för att returnera `Optional` data typen för enskilda resultat frågor.
#### <a name="renames"></a>Byter namn på
* `CosmosDbFactory` till `CosmosFactory` .
* `CosmosDBConfig` till `CosmosConfig` .
* `CosmosDBAccessException` till `CosmosAccessException` .
* `Document` anteckning till `Container` anteckning.
* `DocumentIndexingPolicy` anteckning till `CosmosIndexingPolicy` anteckning.
* `DocumentQuery` till `CosmosQuery` .
* flaggan Application. Properties `populateQueryMetrics` till `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Schemaläggning av diagnostisk loggnings uppgift till `Parallel` trådar för att undvika att blockera I/O-trådar i Netnettning.
* Korrigerar optimistisk låsning vid borttagnings åtgärd.
* Korrigerar problem med undantags frågor för `IN` satsen.
* Åtgärdar problem genom att tillåta `long` data typen för `@Id` .
* Löser problemet genom att tillåta `boolean` , `long` , och `int` `double` som data typer för `@PartitionKey` anteckningen.
* Korrigeringar `IgnoreCase` och `AllIgnoreCase` nyckelord för ignorera ärende frågor.
* Tar bort standardvärdet för enhets begär ande 4 000 när behållare skapas automatiskt.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Lär dig mer om [våren-ramverket](https://spring.io/projects/spring-framework).

Läs mer om [våren Boot](https://spring.io/projects/spring-boot).

Lär dig mer om [fjädrande data](https://spring.io/projects/spring-data).