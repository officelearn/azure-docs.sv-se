---
title: Kopiera och omvandla data i Azure Cosmos DB (SQL API)
description: Lär dig hur du kopierar data till och från Azure Cosmos DB (SQL API) och omvandlar data i Azure Cosmos DB (SQL API) med hjälp av Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: 7096b429145a54b5a09fe38eb8099c4ff24ac452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243619"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiera och omvandla data i Azure Cosmos DB (SQL API) med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuell version](connector-azure-cosmos-db.md)

I den här artikeln beskrivs hur du använder kopieringsaktivitet i Azure Data Factory till att kopiera data från och till Azure Cosmos DB (SQL API) samt hur du använder dataflöden till att transformera data i Azure Cosmos DB (SQL API). Mer information om Azure Data Factory finns i den [inledande artikeln](introduction.md).

>[!NOTE]
>Den här anslutningen stöder endast Cosmos DB SQL API. För MongoDB API, se [anslutning för Azure Cosmos DB:s API för MongoDB](connector-azure-cosmos-db-mongodb-api.md). Andra API-typer stöds inte nu.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Cosmos DB-kopplingen (SQL API) stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

För kopieringsaktivitet stöder den här AZURE Cosmos DB-kopplingen (SQL API):

- Kopiera data från och till Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Skriv till Azure Cosmos DB som **infogad** eller **upsert**.
- Importera och exportera JSON-dokument i sig eller kopiera data från eller till en tabelldatauppsättning. Exempel är en SQL-databas och en CSV-fil. Information om hur du kopierar dokument som de är till eller från JSON-filer eller till eller från en annan Azure Cosmos DB-samling finns i [Importera och exportera JSON-dokument](#import-and-export-json-documents).

Data Factory integreras med [Azure Cosmos DB bulk executor-biblioteket](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) för att ge bästa prestanda när du skriver till Azure Cosmos DB.

> [!TIP]
> [Datamigreringsvideon](https://youtu.be/5-SRNiC_qOU) hjälper dig att kopiera data från Azure Blob-lagring till Azure Cosmos DB. Videon beskriver också prestandajustering överväganden för intag av data till Azure Cosmos DB i allmänhet.

## <a name="get-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som du kan använda för att definiera datafabrikentiteter som är specifika för Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten Azure Cosmos DB (SQL API):

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** måste anges till **CosmosDb**. | Ja |
| Connectionstring |Ange information som krävs för att ansluta till Azure Cosmos DB-databasen.<br />**Obs:** Du måste ange databasinformation i anslutningssträngen som visas i exemplen som följer. <br/> Du kan också placera kontonyckeln i `accountKey` Azure Key Vault och hämta konfigurationen ur anslutningssträngen. Mer information finns i följande exempel och [Store-autentiseringsuppgifter i](store-credentials-in-key-vault.md) azure key vault-artikeln. |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en självvärd integreringskörning (om ditt datalager finns i ett privat nätverk). Om den här egenskapen inte anges används standardkörningen för Azure Integration Runtime. |Inga |

**Exempel**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: store-kontonyckel i Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md).

Följande egenskaper stöds för Azure Cosmos DB (SQL API) datauppsättning: 

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för datauppsättningen måste anges till **CosmosDbSqlApiCollection**. |Ja |
| collectionName (samlingsnamn) |Namnet på Azure Cosmos DB-dokumentsamlingen. |Ja |

Om du använder datauppsättningen "DocumentDbCollection" stöds den fortfarande som den är för bakåtkompatibilitet för kopierings- och uppslagsaktivitet, utan stöds inte för dataflöde. Du föreslås använda den nya modellen framöver.

**Exempel**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som Azure Cosmos DB (SQL API) källa och mottagare stöd. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) som källa

Om du vill kopiera data från Azure Cosmos DB (SQL API) anger du **källtypen** i Kopiera aktivitet till **DocumentDbCollectionSource**. 

Följande egenskaper stöds i **källavsnittet** Kopiera aktivitet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för kopians aktivitetskälla måste anges till **CosmosDbSqlApiSource**. |Ja |
| DocumentDB |Ange azure cosmos DB-frågan för att läsa data.<br/><br/>Exempel:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Inga <br/><br/>Om det inte anges körs det här SQL-uttrycket:`select <columns defined in structure> from mycollection` |
| preferredRegions | Den föredragna listan över regioner att ansluta till när du hämtar data från Cosmos DB. | Inga |
| sidaSize | Antalet dokument per sida i frågeresultatet. Standard är "-1" vilket innebär använder tjänsten sidan dynamisk sidstorlek upp till 1000. | Inga |

Om du använder "DocumentDbCollectionSource"-typkällan stöds den fortfarande som den är för bakåtkompatibilitet. Du föreslås använda den nya modellen framöver som ger rikare funktioner för att kopiera data från Cosmos DB.

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

När du kopierar data från Cosmos DB, om du inte vill [exportera JSON-dokument enligt detta,](#import-and-export-json-documents)är det bästa sättet att ange mappningen i kopieringsaktiviteten. Data Factory hedrar den mappning som du har angett för aktiviteten – om en rad inte innehåller ett värde för en kolumn anges ett null-värde för kolumnvärdet. Om du inte anger en mappning härleds schemat genom att använda den första raden i data. Om den första raden inte innehåller hela schemat saknas vissa kolumner i resultatet av aktivitetsåtgärden.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) som mottagare

Om du vill kopiera data till Azure Cosmos DB (SQL API) anger du **sink-typen** i Kopiera aktivitet till **DocumentDbCollectionSink**. 

Följande egenskaper stöds i **källavsnittet** Kopiera aktivitet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för copy activity sink måste anges till **CosmosDbSqlApiSink**. |Ja |
| skriverBeteende |Beskriver hur du skriver data till Azure Cosmos DB. Tillåtna värden: **infoga** och **upsert**.<br/><br/>Problemet **med upsert** är att ersätta dokumentet om det redan finns ett dokument med samma ID. Annars infogar du dokumentet.<br /><br />**Data**Factory genererar automatiskt ett ID för ett dokument om ett ID inte anges vare sig i det ursprungliga dokumentet eller efter kolumnmappning. Det innebär att du måste se till att dokumentet har ett ID för **att** det ska fungera som förväntat. |Inga<br />(standard är **infoga**) |
| skriverBatchSize | Data Factory använder [Azure Cosmos DB bulk executor-bibliotek](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) för att skriva data till Azure Cosmos DB. Egenskapen **writeBatchSize** styr storleken på de dokument som ADF tillhandahåller biblioteket. Du kan försöka öka värdet för **writeBatchSize** för att förbättra prestanda och minska värdet om dokumentstorleken är stor - se nedan tips. |Inga<br />(standardvärdet är **10 000**) |
| inaktiveraMetricsCollection | Data Factory samlar in mått som Cosmos DB RUs för kopiering prestandaoptimering och rekommendationer. Om du har något att `true` göra med det här beteendet anger du att den ska stängas av. | Nej (standard `false`är) |

>[!TIP]
>Om du vill importera JSON-dokument i sig läser du avsnittet [Importera eller exportera JSON-dokument.](#import-and-export-json-documents) om du vill kopiera från tabellformade data läser du [Migrera från relationsdatabas till Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB begränsar storleken på en begäran till 2 MB. Formeln är begäran storlek = enstaka dokumentstorlek * Skriv batchstorlek. Om du stöter på fel med texten **"Begärandens storlek är för stor.",** minskar du värdet i **konfigurationen `writeBatchSize` ** för kopiasänka.

Om du använder "DocumentDbCollectionSink" typ källa, det stöds fortfarande som-är för bakåtkompatibilitet. Du föreslås använda den nya modellen framöver som ger rikare funktioner för att kopiera data från Cosmos DB.

**Exempel**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Schemamappning

Om du vill kopiera data från Azure Cosmos DB till tabellmottagare eller omvända läser du [schemamappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

När du omvandlar data i mappning av dataflöde kan du läsa och skriva till samlingar i Cosmos DB. Mer information finns i [källomvandlingen](data-flow-source.md) och [sink-omvandlingen](data-flow-sink.md) vid mappning av dataflöden.

### <a name="source-transformation"></a>Omvandling av källa

Inställningar som är specifika för Azure Cosmos DB är tillgängliga på fliken **Källalternativ** i källomvandlingen. 

**Inkludera systemkolumner:** Om det ```id``` ```_ts```är sant inkluderas , , och andra systemkolumner i dataflödesmetadata från CosmosDB. När du uppdaterar samlingar är det viktigt att inkludera detta så att du kan ta tag i det befintliga rad-ID: t.

**Sidans storlek:** Antalet dokument per sida i frågeresultatet. Standard är "-1" som använder tjänsten dynamisk sida upp till 1000.

**Dataflöde:** Ange ett valfritt värde för antalet ru:er som du vill använda på CosmosDB-samlingen för varje körning av det här dataflödet under läsåtgärden. Minimum är 400.

**Önskade regioner:** Välj de läsområden som föredras för den här processen.

#### <a name="json-settings"></a>JSON-inställningar

**Ett enda dokument:** Välj det här alternativet om ADF ska behandla hela filen som ett enda JSON-dokument.

**Namn på ej citerad kolumn:** Markera det här alternativet om kolumnnamnen i JSON inte har citerats.

**Har kommentarer:** Använd det här valet om dina JSON-dokument har kommentarer i data.

**Singel citerad:** Detta bör väljas om kolumnerna och värdena i dokumentet citeras med enstaka citattecken.

**Omvänt snedstreck flydde:** Om du använder omvänt snedstreck för att undkomma tecken i din JSON väljer du det här alternativet.

### <a name="sink-transformation"></a>Sink omvandling

Inställningar som är specifika för Azure Cosmos DB är tillgängliga på fliken **Inställningar** i sink-omvandlingen.

**Uppdateringsmetod:** Bestämmer vilka åtgärder som är tillåtna på databasens mål. Standard är att endast tillåta infogningar. För att uppdatera, upsert eller ta bort rader krävs en omvandling på en rad för att tagga rader för dessa åtgärder. För uppdateringar, upserts och borttagningar måste en nyckelkolumn eller kolumner anges för att avgöra vilken rad som ska ändras.

**Insamlingsåtgärd:** Bestämmer om målsamlingen ska återskapas innan du skriver.
* Ingen: Ingen åtgärd kommer att göras i samlingen.
* Återskapa: Samlingen kommer att släppas och återskapas

**Batchstorlek:** Styr hur många rader som skrivs i varje bucket. Större batchstorlekar förbättrar komprimerings- och minnesoptimeringen, men risken är på minnesundantag när data cachelagrar.

**Partitionsnyckel:** Ange en sträng som representerar partitionsnyckeln för samlingen. Exempel: ```/movies/title```

**Dataflöde:** Ange ett valfritt värde för antalet ru:er som du vill använda på cosmosDB-samlingen för varje körning av det här dataflödet. Minimum är 400.

**Skriv budget för dataflöde:** Ett heltal som representerar antalet ru: er som du vill allokera till massinmatning Spark-jobbet. Det här numret är inte det totala dataflöde som tilldelats samlingen.

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importera och exportera JSON-dokument

Du kan enkelt använda den här Azure Cosmos DB-kopplingen (SQL API) för att enkelt:

* Kopiera dokument mellan två Azure Cosmos DB-samlingar som de är.
* Importera JSON-dokument från olika källor till Azure Cosmos DB, inklusive från Azure Blob storage, Azure Data Lake Store och andra filbaserade butiker som Azure Data Factory stöder.
* Exportera JSON-dokument från en Azure Cosmos DB-samling till olika filbaserade butiker.

Så här uppnår du schemaaoberoende kopia:

* När du använder verktyget Kopiera data väljer du alternativet **Exportera som det är till JSON-filer eller Cosmos DB-samlingsalternativ.**
* När du använder aktivitetsförfattare väljer du JSON-format med motsvarande filarkiv för käll- eller diskho.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrera från relationsdatabas till Cosmos DB

När du migrerar från en relationsdatabas, t.ex. I vissa fall kanske du vill omforma datamodellen för att optimera den för nosql-användningsfallen enligt [datamodellering i Azure Cosmos DB](../cosmos-db/modeling-data.md), till exempel för att denormalisera data genom att bädda in alla relaterade underposter i ett JSON-dokument. I sådana fall kan du läsa [den här artikeln](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) med en genomgång om hur du uppnår den med hjälp av Azure Data Factory-kopieringsaktivitet.

## <a name="next-steps"></a>Nästa steg

En lista över datalager som kopierar aktivitet stöder som källor och sänkor i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
