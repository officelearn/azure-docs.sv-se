---
title: Snabbstart - Använd Java för att skapa en dokumentdatabas med Azure Cosmos DB
description: Den här snabbstarten visar ett Java-kodexempel som du kan använda för att ansluta till och fråga Azure Cosmos DB SQL API
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 489f241453436190213b99cb4e7be0688a8b6237
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240179"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Snabbstart: Skapa en Java-app för att hantera Azure Cosmos DB SQL API-data


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB SQL API-konto från Azure-portalen och genom att använda en Java-app som klonats från GitHub. Först skapar du ett Azure Cosmos DB SQL API-konto med Azure-portalen och skapar sedan en Java-app med SQL Java SDK och lägger sedan till resurser i ditt Cosmos DB-konto med hjälp av Java-programmet. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärde- och diagramdatabaser med globala distributions- och vågräta skalfunktioner.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) med en URI av `https://localhost:8081` och nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Peka `JAVA_HOME` din miljövariabel till mappen där JDK är installerat.
- En [Maven binära arkiv](https://maven.apache.org/download.cgi). På Ubuntu, `apt-get install maven` kör för att installera Maven.
- [Git](https://www.git-scm.com/downloads). På Ubuntu `sudo apt-get install git` kör du för att installera Git.

## <a name="introductory-notes"></a>Inledande anteckningar

*Strukturen för ett Cosmos DB-konto.* Oavsett API eller programmeringsspråk innehåller ett Cosmos *DB-konto* noll eller fler *databaser*, en *databas* (DB) innehåller noll eller fler *behållare*och en *behållare* innehåller noll eller fler objekt, som visas i diagrammet nedan:

![Azure Cosmos-kontoentiteter](./media/databases-containers-items/cosmos-entities.png)

Du kan läsa mer om databaser, behållare och objekt [här.](databases-containers-items.md) Några viktiga egenskaper definieras på nivån för behållaren, bland dem etablerade dataflöde och *partitionsnyckel* . *partition key* 

Det avstämmda genomströmningen mäts i Request Units *(RU)* som har ett monetärt pris och är en väsentlig avgörande faktor för kontots driftskostnader. Etablerat dataflöde kan väljas per behållare granularitet eller per databas granularitet, men behållare nivå dataflöde specifikation är vanligtvis att föredra. Du kan läsa mer om dataflödesetablering [här.](set-throughput.md)

När objekt infogas i en Cosmos DB-behållare växer databasen vågrätt genom att lägga till mer lagringsutrymme och beräkning för att hantera begäranden. Lagrings- och beräkningskapacitet läggs till i diskreta enheter som kallas *partitioner*och du måste välja ett fält i dina dokument för att vara den partitionsnyckel som mappar varje dokument till en partition. Det sätt på vilket partitioner hanteras är att varje partition tilldelas en ungefär lika stor del av intervallet för partitionsnyckelvärden. Därför rekommenderas du att välja en partitionsnyckel som är relativt slumpmässig eller jämnt fördelad. Annars kommer vissa partitioner att se betydligt fler begäranden *(het partition)* medan andra partitioner ser betydligt färre begäranden *(kall partition),* och detta ska undvikas. Du kan läsa mer om partitionering [här](partitioning-overview.md).

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en dokumentdatabas måste du skapa ett SQL-API-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Lägga till en container

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Lägga till exempeldata

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Fråga dina data

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Vi ska klona en SQL API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Köra appen](#run-the-app). 

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Hantera databasresurser med hjälp av api:et för synkron (synkronisering)

* `CosmosClient`-initiering. Den `CosmosClient` ger logiska representation på klientsidan för Azure Cosmos-databastjänsten. Den här klienten används för att konfigurera och köra förfrågningar till tjänsten.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase`Skapandet.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer`Skapandet.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Skapa objekt med `createItem` hjälp av metoden.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Punktläsningar utförs `readItem` med hjälp av metoden.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* SQL-frågor över JSON utförs `queryItems` med metoden.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Hantera databasresurser med hjälp av asynkrona (async) API

* Async API-anrop returneras omedelbart, utan att vänta på ett svar från servern. Mot bakgrund av detta visar följande kodavsnitt korrekta designmönster för att utföra alla föregående hanteringsuppgifter med hjälp av async API.

* `CosmosAsyncClient`-initiering. Den `CosmosAsyncClient` ger logiska representation på klientsidan för Azure Cosmos-databastjänsten. Den här klienten används för att konfigurera och köra asynkrona begäranden mot tjänsten.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase`Skapandet.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer`Skapandet.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Precis som med synkroniserings-API:et `createItem` sker objektskapande med hjälp av metoden. Det här exemplet visar hur `createItem` du effektivt utfärdar många asynkronbegäranden genom att prenumerera på en reaktiv ström som utfärdar meddelanden och skriver ut meddelanden. Eftersom det här enkla exemplet körs `CountDownLatch` till slutförande och avslutas används instanser för att säkerställa att programmet inte avslutas när artikeln skapas. **Rätt asynkron programmeringspraxis är inte att blockera asynkrona anrop - i realistiska användningsfallsbegäranden genereras från en main()-loop som körs på obestämd tid, vilket eliminerar behovet av att haka på asynkronanrop.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Precis som med synkroniserings-API:et utförs punktläsningar med hjälp av `readItem` metoden.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Precis som med synkroniserings-API:et utförs SQL-frågor via JSON med `queryItems` metoden.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

## <a name="run-the-app"></a>Kör appen

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och starta appen med din slutpunktsinformation. På så vis kan appen kommunicera med den värdbaserade databasen.

1. I git-terminalfönstret `cd` till exempelkodsmappen.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. I git-terminalfönstret använder du följande kommando för att installera de Java-paket som krävs.

    ```bash
    mvn package
    ```

3. I git-terminalfönstret använder du följande kommando för att starta Java-programmet (ersätt SYNCASYNCMODE med `sync` eller `async` beroende på vilken exempelkod du vill köra, ersätta YOUR_COSMOS_DB_HOSTNAME med det citerade URI-värdet från portalen och ersätta YOUR_COSMOS_DB_MASTER_KEY med den citerade primärnyckeln från portalen)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Terminalfönstret visar ett meddelande om att FamilyDB-databasen har skapats. 
    
4. Appen skapar databas med namn`AzureSampleFamilyDB`
5. Appen skapar behållare med namn`FamilyContainer`
6. Appen utför punktläsningar med objekt-ID och partitionsnyckelvärde (som är efternamn i vårt exempel). 
7. Appen kommer att fråga objekt för att hämta alla familjer med efternamn i ("Andersen", "Wakefield", "Johnson")

7. Appen tar inte bort de skapade resurserna. Växla tillbaka till portalen för att [rensa resurserna](#clean-up-resources).  från ditt konto så att du inte debiteras.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB SQL API-konto, skapar en dokumentdatabas och behållare med datautforskaren och kör en Java-app för att göra samma sak programmässigt. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera data till Azure Cosmos DB](import-data.md)
