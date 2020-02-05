---
title: Kopiera och transformera data i Azure Cosmos DB (SQL API)
description: Lär dig hur du kopierar data till och från Azure Cosmos DB (SQL API) och transformera data i Azure Cosmos DB (SQL API) med hjälp av Data Factory.
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
ms.openlocfilehash: 6e9e1d54599ab88092638762ccd7974e44c82cbf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025817"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiera och transformera data i Azure Cosmos DB (SQL API) med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuell version](connector-azure-cosmos-db.md)

I den här artikeln beskrivs hur du använder kopieringsaktivitet i Azure Data Factory till att kopiera data från och till Azure Cosmos DB (SQL API) samt hur du använder dataflöden till att transformera data i Azure Cosmos DB (SQL API). Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

>[!NOTE]
>Den här anslutningen har endast stöd för Cosmos DB SQL API. För MongoDB-API, se [Connector för Azure Cosmos DB s API för MongoDB](connector-azure-cosmos-db-mongodb-api.md). Andra API-typer stöds inte nu.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Cosmos DB-anslutningen (SQL API) stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

För kopierings aktivitet stöder den här Azure Cosmos DB-anslutaren (SQL API):

- Kopiera data från och till Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Skriva till Azure Cosmos DB som **infoga** eller **upsert**.
- Importera och exportera JSON-dokument som – är, eller kopiera data från eller till en datauppsättning i tabellformat. Exempel: en SQL-databas och en CSV-fil. Om du vill kopiera dokument som-är till eller från JSON-filer eller till eller från en annan Azure Cosmos DB samling, se [Importera och exportera JSON-dokument](#import-and-export-json-documents).

Data Factory kan integreras med den [Azure Cosmos DB bulk executor biblioteket](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) att ge bästa möjliga prestanda när du skriver till Azure Cosmos DB.

> [!TIP]
> Den [datamigrering video](https://youtu.be/5-SRNiC_qOU) vägleder dig genom stegen för att kopiera data från Azure Blob storage till Azure Cosmos DB. Videon beskriver också prestandajustering överväganden för att föra in data till Azure Cosmos DB i allmänhet.

## <a name="get-started"></a>Kom i gång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten Azure Cosmos DB (SQL API):

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen måste anges till **CosmosDb**. | Ja |
| connectionString |Ange information som krävs för att ansluta till Azure Cosmos DB-databasen.<br />**Obs**: du måste ange databasinformation i anslutningssträngen som visas i följande exempel. <br/> Du kan också ange konto nyckeln i Azure Key Vault och hämta `accountKey`-konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal integration runtime (om ditt datalager finns i ett privat nätverk). Om egenskapen inte anges används standard Azure Integration Runtime. |Inga |

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

**Exempel: lagrings konto nyckel i Azure Key Vault**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md).

Följande egenskaper stöds för data uppsättningen Azure Cosmos DB (SQL API): 

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **CosmosDbSqlApiCollection**. |Ja |
| collectionName |Namnet på Azure Cosmos DB-dokumentsamling. |Ja |

Om du använder data uppsättningen "DocumentDbCollection" stöds den fortfarande för bakåtkompatibilitet för kopierings-och söknings aktiviteter, men det stöds inte för data flöden. Du rekommenderas att använda den nya modellen som går framåt.

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

Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cosmos DB-källan (SQL API) och mottagar tjänsten. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) som källa

Om du vill kopiera data från Azure Cosmos DB (SQL API) anger du **käll** typen i kopierings aktivitet till **DocumentDbCollectionSource**. 

Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **CosmosDbSqlApiSource**. |Ja |
| DocumentDB |Ange Azure Cosmos DB-fråga för att läsa data.<br/><br/>Exempel:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Inga <br/><br/>Om inte anges, körs den här SQL-instruktionen: `select <columns defined in structure> from mycollection` |
| preferredRegions | Den önskade listan över regioner att ansluta till när data hämtas från Cosmos DB. | Inga |
| pageSize | Antalet dokument per sida i frågeresultatet. Standardvärdet är "-1", vilket innebär att den dynamiska sid storleken på tjänst sidan är upp till 1000. | Inga |

Om du använder typen "DocumentDbCollectionSource"-källa stöds den fortfarande för bakåtkompatibilitet. Du rekommenderas att använda den nya modellen som går framåt, vilket ger rikare funktioner för att kopiera data från Cosmos DB.

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

När du kopierar data från Cosmos DB, om du inte vill [Exportera JSON-dokument i befintligt skick](#import-and-export-json-documents), är det bästa sättet att ange mappningen i kopierings aktiviteten. Data Factory följer den mappning som du angav i aktiviteten – om en rad inte innehåller något värde för en kolumn, anges ett null-värde för kolumnvärdet. Om du inte anger någon mappning, Data Factory härleder schemat genom att använda den första raden i data. Om den första raden inte innehåller det fullständiga schemat, kommer vissa kolumner saknas i resultatet av aktivitets åtgärden.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) som mottagare

Om du vill kopiera data till Azure Cosmos DB (SQL API) anger du **mottagar** typen i kopierings aktivitet till **DocumentDbCollectionSink**. 

Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till **CosmosDbSqlApiSink**. |Ja |
| writeBehavior |Beskriver hur du skriver data till Azure Cosmos DB. Tillåtna värden: **infoga** och **upsert**.<br/><br/>Beteendet för **upsert** är att ersätta dokumentet om ett dokument med samma ID redan finns, annars Infoga dokumentet.<br /><br />**Obs**: Data Factory genererar automatiskt ett ID för ett dokument om ett ID inte har angetts i det ursprungliga dokumentet eller genom kolumnmappning. Det innebär att måste du se till att, för **upsert** för att fungera som förväntat, dokumentet har ett ID. |Inga<br />(standardvärdet är **infoga**) |
| writeBatchSize | Data Factory använder den [Azure Cosmos DB bulk executor biblioteket](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) att skriva data till Azure Cosmos DB. Egenskapen **writeBatchSize** styr storleken på dokument som ADF tillhandahåller till biblioteket. Du kan prova att öka värdet för **writeBatchSize** att förbättra prestanda och minska värdet om dokumentet storlek vara stora – se nedan tips. |Inga<br />(standardvärdet är **10 000**) |
| disableMetricsCollection | Data Factory samlar in mått som Cosmos DB ru: er för att kopiera prestanda optimering och rekommendationer. Om du är orolig för det här beteendet anger du `true` att inaktivera det. | Nej (standard är `false`) |

>[!TIP]
>Information om hur du importerar JSON-dokument finns i avsnittet [Importera eller exportera JSON-dokument](#import-and-export-json-documents) . Om du vill kopiera från tabellbaserade data, se [Migrera från Relations databas till Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB begränsar storleken på enskild begäran till 2MB. Formeln är begära storlek = enda dokumentstorlek * skriva batchstorlek. Om du stöter på fel som säger **”begär är för stor”.** , **minska den `writeBatchSize` värdet** i konfigurationen för mottagare av kopia.

Om du använder typen "DocumentDbCollectionSink"-källa stöds den fortfarande för bakåtkompatibilitet. Du rekommenderas att använda den nya modellen som går framåt, vilket ger rikare funktioner för att kopiera data från Cosmos DB.

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

### <a name="schema-mapping"></a>Schema mappning

Om du vill kopiera data från Azure Cosmos DB till tabell mottagare eller omvänd, se [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa och skriva till samlingar i Cosmos DB. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flöden.

### <a name="source-transformation"></a>Käll omvandling

Inställningar som är aktuella för Azure Cosmos DB finns tillgängliga på fliken **käll alternativ** i käll omvandlingen. 

**Inkludera system kolumner:** Om det här värdet är sant kommer ```id```, ```_ts```och andra system kolumner inkluderas i metadata för data flödet från CosmosDB. När du uppdaterar samlingar är det viktigt att ta med detta så att du kan ta tag i det befintliga rad-ID: t.

**Sid storlek:** Antalet dokument per sida i frågeresultatet. Standardvärdet är "-1" som använder den dynamiska sidan för tjänsten upp till 1000.

**Data flöde:** Ange ett valfritt värde för det antal ru: er som du vill använda för din CosmosDB-samling för varje körning av det här data flödet under Läs åtgärden. Minimum är 400.

**Prioriterade regioner:** Välj önskade Läs regioner för den här processen.

#### <a name="json-settings"></a>JSON-inställningar

**Enstaka dokument:** Välj det här alternativet om ADF ska behandla hela filen som ett enda JSON-dokument.

**Icke-citerade kolumn namn:** Välj det här alternativet om kolumn namn i JSON inte anges som citerade.

**Har kommentarer:** Använd det här alternativet om dina JSON-dokument har kommentarer i data.

**Enkelt citat tecken:** Välj det här alternativet om kolumnerna och värdena i dokumentet är citat tecken med enkla citat tecken.

**Omvänt snedstreck Escaped:** Välj det här alternativet om du använder omvända snedstreck för att kringgå tecken i JSON.

### <a name="sink-transformation"></a>Omvandling av mottagare

Inställningar som är tillgängliga för Azure Cosmos DB finns på fliken **Inställningar** i omvandlingen för mottagare.

**Uppdaterings metod:** Avgör vilka åtgärder som tillåts på databas målet. Standardvärdet är att endast tillåta infogningar. Om du vill uppdatera, upsert eller ta bort rader krävs en Alter-Row-omvandling för att tagga rader för dessa åtgärder. För uppdateringar, upsertar och borttagningar måste en nyckel kolumn eller kolumner anges för att avgöra vilken rad som ska ändras.

**Samlings åtgärd:** Bestämmer om du vill återskapa mål samlingen innan du skriver.
* Ingen: ingen åtgärd utförs för samlingen.
* Återskapa: samlingen tas bort och återskapas

**Batchstorlek**: styr hur många rader som skrivs i varje Bucket. Större batch-storlekar förbättrar komprimeringen och minnes optimeringen, men riskerar att ta bort minnes undantag när data cachelagras.

**Partitionsnyckel:** Ange en sträng som representerar partitionens partitionsnyckel för din samling. Exempel: ```/movies/title```

**Data flöde:** Ange ett valfritt värde för det antal ru: er som du vill använda för din CosmosDB-samling för varje körning av det här data flödet. Minimum är 400.

**Skriv data flödes budget:** Ett heltal som representerar antalet ru: er som du vill allokera till Spark-jobbet för Mass inmatning. Numret är utanför det totala data flödet som allokerats till samlingen.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importera och exportera JSON-dokument

Du kan enkelt använda den här Azure Cosmos DBs anslutningen (SQL API) för att:

* Kopiera mellan två Azure Cosmos DB-samlingar som – är.
* Importera JSON-dokument från olika källor till Azure Cosmos DB, såsom från Azure Blob storage, Azure Data Lake Store och andra filbaserade butiker som har stöd för Azure Data Factory.
* Exportera JSON-dokument från en Azure Cosmos DB-samling till olika filbaserade lager.

Få schemaoberoende kopia:

* När du använder verktyget kopieringsdata, Välj den **exportera som – är att JSON-filer eller Cosmos DB-samling** alternativet.
* När du använder redigering av aktiviteter väljer du JSON-format med motsvarande fil Arkiv för källa eller mottagare.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrera från Relations databas till Cosmos DB

När du migrerar från en Relations databas, t. ex. SQL Server Azure Cosmos DB, kan kopierings aktiviteten enkelt mappa tabell data från källa för att förenkla JSON-dokument i Cosmos DB. I vissa fall kanske du vill utforma om data modellen för att optimera den för NoSQL användnings fall enligt [data modellering i Azure Cosmos DB](../cosmos-db/modeling-data.md), till exempel för att avnormalisera data genom att bädda in alla relaterade under objekt i ett JSON-dokument. I det här fallet kan du läsa [det här blogg inlägget](https://medium.com/@ArsenVlad/denormalizing-via-embedding-when-copying-data-from-sql-to-cosmos-db-649a649ae0fb) med en genom gång av hur du uppnår det med hjälp av Azure Data Factory kopierings aktivitet.

## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
