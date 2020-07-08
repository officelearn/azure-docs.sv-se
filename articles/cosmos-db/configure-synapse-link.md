---
title: Konfigurera och använda Azure Synapse-länken för Azure Cosmos DB (förhands granskning)
description: Lär dig hur du aktiverar Synapse-länken för Azure Cosmos-konton, skapar en behållare med analytiskt Arkiv aktiverat, ansluter Azure Cosmos-databasen till Synapse-arbetsytan och kör frågor.
author: SriChintala
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: d2a10d064bed3e2e2e798d16ce72ccf55c965f8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262046"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Konfigurera och använda Azure Synapse-länken för Azure Cosmos DB (förhands granskning)

Synapse-länken för Azure Cosmos DB är en molnbaserad hybrid transaktions-och analys bearbetnings funktion (HTAP) som gör att du kan köra nära real tids analys över drifts data i Azure Cosmos DB. Synapse-länken skapar en tätt sömlös integrering mellan Azure Cosmos DB och Azure Synapse Analytics.


> [!IMPORTANT]
> För att använda Azure Synapse-länken, se till att du etablerar ditt Azure Cosmos-konto & Azure Synapse Analytics-arbetsyta i någon av de regioner som stöds ovan. En lista över regioner som stöds finns i [Azure service updates](https://azure.microsoft.com/updates/). 

Använd följande steg för att köra analytiska frågor med Synapse-länken för Azure Cosmos DB:

* [Aktivera Synapse-länk för dina Azure Cosmos-konton](#enable-synapse-link)
* [Skapa en Azure Cosmos-behållare med analytiskt lager](#create-analytical-ttl)
* [Ansluta din Azure Cosmos-databas till en Synapse-arbetsyta](#connect-to-cosmos-database)
* [Fråga analys lagret med hjälp av Synapse Spark](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Aktivera Azure Synapse-länk för Azure Cosmos-konton

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure-konto](create-sql-api-dotnet.md#create-account)eller Välj ett befintligt Azure Cosmos-konto.

1. Gå till ditt Azure Cosmos-konto och öppna fönstret **funktioner** .

1. Välj **Synapse länk** från listan funktioner.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Hitta Synapse Link Preview-funktion":::

1. Härnäst kommer du att bli ombedd att aktivera Synapse-länken på ditt konto. Välj Aktivera.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Aktivera Synapse länk funktion":::

1. Ditt konto har nu Aktiver ATS för att använda Synapse-länken. Härnäst lär du dig hur du skapar ett analytiskt Arkiv med aktiverade behållare för att automatiskt starta replikeringen av dina drift data från transaktions arkivet till analys lagret.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

[Azure Resource Manager-mallen](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) skapar ett Synapse-länk aktiverat Azure Cosmos-konto för SQL-API. Den här mallen skapar ett huvud-API-konto i en region med en behållare som kon figurer ATS med analytiskt TTL aktiverat och ett alternativ för att använda manuellt eller autoskalning av data flöde. Om du vill distribuera den här mallen klickar **du på distribuera till Azure** på sidan viktigt.

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a>Skapa en Azure Cosmos-behållare med analys lager

Du kan aktivera analys lager på en Azure Cosmos-behållare när du skapar behållaren. Du kan använda Azure Portal eller konfigurera `analyticalTTL` egenskapen vid skapande av behållare med hjälp av Azure Cosmos DB SDK: er.

> [!NOTE]
> För närvarande kan du aktivera analys lagring för **nya** behållare (både i nya och befintliga konton).

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com/) eller [Azure Cosmos Explorer](https://cosmos.azure.com/).

1. Gå till ditt Azure Cosmos-konto och öppna fliken **datautforskaren** .

1. Välj **ny behållare** och ange ett namn för din databas, behållare, partitionsnyckel och data flödes information. Aktivera alternativet **analys Arkiv** . När du har aktiverat analys lagret skapas en behållare med `AnalyicalTTL` egenskapen inställd på standardvärdet-1 (oändlig kvarhållning). Detta analys lager behåller alla tidigare versioner av poster.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Aktivera analys lager för Azure Cosmos-behållare":::

1. Om du tidigare inte har aktiverat Synapse-länken för det här kontot kommer du att uppmanas att göra det eftersom det är ett krav för att skapa en aktive rad behållare för analys lager. Om du uppmanas väljer du **Aktivera Synapse länk**.

1. Välj **OK**för att skapa en Azure Cosmos-behållare som är analytiskt lagrad.

### <a name="net-sdk"></a>.NET SDK

Följande kod skapar en behållare med analytisk lagring med hjälp av .NET SDK. Ställ in egenskapen analytisk TTL på det obligatoriska värdet. Listan över tillåtna värden finns i artikeln [analytiska TTL-värden som stöds](analytical-store-introduction.md#analytical-ttl) :

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Java v4 SDK

Följande kod skapar en behållare med analytisk lagring med hjälp av Java v4 SDK. Ställ in `AnalyticalStoreTimeToLiveInSeconds` egenskapen på det obligatoriska värdet:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v3-sdk"></a>Python v3 SDK

Följande kod skapar en behållare med analys lager med hjälp av python SDK:

```python
import azure.cosmos.cosmos_client as cosmos_client
def create_collection_if_not_exists(cosmosEndpoint, cosmosKey, databaseName, collectionName):
    client = cosmos_client.CosmosClient(url_connection=cosmosEndpoint, auth={'masterKey': cosmosKey})

db = client.QueryDatabases("select * from c where c.id = '" + databaseName + "'").fetch_next_block()[0]
options = {
    'offerThroughput': 1000
}

container_definition = {
    'id': collectionName,
    "partitionKey": {  
        "paths": [  
        "/id"  
        ],  
        "kind": "Hash" 
    },
    "indexingPolicy": {  
    "indexingMode": "consistent",  
    "automatic": True,  
    "includedPaths": [],  
    "excludedPaths": [{
        "path": "/*"
    }]  
    },
    "defaultTtl": -1,
    "analyticalStorageTtl": -1
}

container = client.CreateContainer(db['_self'], container_definition, options)
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a>Uppdatera tiden för analys av analys arkivet till Live

När analysarkivet har aktiverats med ett visst TTL-värde kan du uppdatera det till ett annat giltigt värde senare. Du kan uppdatera värdet med hjälp av Azure Portal eller SDK:er. Information om de olika alternativen för konfiguration av analytiska TTL-alternativ finns i artikeln [stöd för analytiska TTL-värden](analytical-store-introduction.md#analytical-ttl) .

#### <a name="azure-portal"></a>Azure Portal

Om du har skapat en aktive rad behållare för analys lager via Azure Portal, innehåller den ett standard-analytiskt TTL-värde på-1. Använd följande steg för att uppdatera det här värdet:

1. Logga in på [Azure Portal](https://portal.azure.com/) eller [Azure Cosmos Explorer](https://cosmos.azure.com/).

1. Gå till ditt Azure Cosmos-konto och öppna fliken **datautforskaren** .

1. Välj en befintlig behållare som har analytiskt Arkiv aktiverat. Expandera den och ändra följande värden:

  * Öppna fönstret **Scale & Settings** (Skalning och inställningar).
  * Under **Ange** Sök, * * analys lagring Time to Live * *.
  * Välj **På (inte standard)** eller välj **På** och ange ett TTL-värde
  * Klicka på **Spara** för att spara ändringarna.

#### <a name="net-sdk"></a>.NET SDK

Följande kod visar hur du uppdaterar TTL för analytisk lagring med hjälp av .NET SDK:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Java v4 SDK

Följande kod visar hur du uppdaterar TTL för analytisk lagring med hjälp av Java v4 SDK:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a>Ansluta till en Synapse-arbetsyta

Använd anvisningarna i [ansluta till Azure Synapse-länken](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) om hur du kommer åt en Azure Cosmos DB databas från Azure Synapse Analytics Studio med Azure Synapse-länken.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a>Fråga med Synapse Spark

Följ anvisningarna i artikeln [fråga Azure Cosmos DB analys lager](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) om hur du frågar med Synapse Spark. Artikeln innehåller några exempel på hur du kan interagera med analys lagret från Synapse-gester. Dessa gester visas när du högerklickar på en behållare. Med gester kan du snabbt skapa kod och anpassa den efter dina behov. De är också perfekta för att identifiera data med ett enda klick.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a>Komma igång med Azure Synpase Link-samples

Du hittar exempel för att komma igång med Azure Synapse-länk på [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Dessa demonstrerar lösningar från slut punkt till slut punkt med IoT och detalj handels scenarier.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande dokument:

* [Azure Synapse-länk för Azure Cosmos DB.](synapse-link.md)

* [Översikt över Azure Cosmos DB-analysarkiv.](analytical-store-introduction.md)

* [Vanliga frågor om Synapse-länk för Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Apache Spark i Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [SQL Server utan/på begäran i Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
