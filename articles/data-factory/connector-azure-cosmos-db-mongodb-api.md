---
title: Kopiera data från Azure Cosmos DBs API för MongoDB
description: Lär dig hur du kopierar data från käll data lager som stöds till eller från Azure Cosmos DBs API för MongoDB till mottagar lager som stöds med hjälp av Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 9b13920252b3a5626fd192c6e899154efd31a3de
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893247"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopiera data till eller från Azure Cosmos DBs API för MongoDB med hjälp av Azure Data Factory

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Azure Cosmos DB s API för MongoDB. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

>[!NOTE]
>Den här kopplingen stöder endast kopiering av data till/från Azure Cosmos DB s API för MongoDB. SQL-API finns i [Cosmos DB SQL API-anslutning](connector-azure-cosmos-db.md). Andra API-typer stöds inte nu.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure Cosmos DBs API för MongoDB till alla mottagar data lager som stöds, eller kopiera data från alla käll data lager som stöds till Azure Cosmos DB s API för MongoDB. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Du kan använda Azure Cosmos DBs API för MongoDB-anslutning för att:

- Kopiera data från och till [Azure Cosmos DBS API för MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Skriva till Azure Cosmos DB som **infoga** eller **upsert**.
- Importera och exportera JSON-dokument som – är, eller kopiera data från eller till en datauppsättning i tabellformat. Exempel: en SQL-databas och en CSV-fil. Om du vill kopiera dokument som de är till eller från JSON-filer eller till eller från en annan Azure Cosmos DB samling, se Importera eller exportera JSON-dokument.

## <a name="get-started"></a>Kom i gång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för Azure Cosmos DBs API för MongoDB.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Azure Cosmos DB: s API för den länkade tjänsten MongoDB:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **CosmosDbMongoDbApi**. | Ja |
| connectionString |Ange anslutnings strängen för din Azure Cosmos DBs API för MongoDB. Du hittar det i Azure Portal-> Cosmos DB bladet > primär eller sekundär anslutnings sträng med mönstret för `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` -konfigurationen från anslutnings strängen. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) .|Ja |
| databas | Namnet på den databas som du vill få åtkomst till. | Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal integration runtime (om ditt datalager finns i ett privat nätverk). Om egenskapen inte anges används standard Azure Integration Runtime. |Inga |

**Exempel**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper stöds för Azure Cosmos DB s API för MongoDB-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **CosmosDbMongoDbApiCollection**. |Ja |
| collectionName |Namnet på den Azure Cosmos DB samlingen. |Ja |

**Exempel**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som Azure Cosmos DBs API för MongoDB-källa och mottagar stöd.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB s API för MongoDB som källa

Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **CosmosDbMongoDbApiSource**. |Ja |
| filter | Anger urvals filter med hjälp av fråge operatorer. Om du vill returnera alla dokument i en samling utelämnar du den här parametern eller skickar ett tomt dokument ({}). | Inga |
| cursorMethods.project | Anger de fält som ska returneras i dokument för projektion. Om du vill returnera alla fält i de matchande dokumenten utelämnar du den här parametern. | Inga |
| cursorMethods.sort | Anger i vilken ordning som frågan returnerar matchande dokument. Se [cursor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Inga |
| cursorMethods.limit | Anger det maximala antalet dokument som servern returnerar. Referera till [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Inga | 
| cursorMethods.skip | Anger antalet dokument som ska hoppas över och från där MongoDB börjar returnera resultat. Se [cursor. SKIP ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Inga |
| batchSize | Anger antalet dokument som ska returneras i varje batch av svaret från MongoDB-instansen. I de flesta fall kommer ändringar av batchstorleken inte att påverka användaren eller programmet. Cosmos DB gränser för varje batch får inte överstiga 40MB i storlek, vilket är summan av batchSize-antalet dokument storlek, så minska det här värdet om dokument storleken är stor. | Inga<br/>(Standardvärdet är **100**) |

>[!TIP]
>ADF-support använder BSON-dokument i **strikt läge**. Kontrol lera att filter frågan är i strikt läge i stället för Shell-läge. Du hittar mer information på [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Azure Cosmos DB s API för MongoDB som mottagare

Följande egenskaper stöds i Kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till **CosmosDbMongoDbApiSink**. |Ja |
| writeBehavior |Beskriver hur du skriver data till Azure Cosmos DB. Tillåtna värden: **infoga** och **upsert**.<br/><br/>Beteendet för **upsert** är att ersätta dokumentet om det redan finns ett dokument med samma `_id`. annars infogar du dokumentet.<br /><br />**Obs!** Data Factory skapar automatiskt ett `_id` för ett dokument om ett `_id` inte har angetts antingen i det ursprungliga dokumentet eller med kolumn mappning. Det innebär att måste du se till att, för **upsert** för att fungera som förväntat, dokumentet har ett ID. |Inga<br />(standardvärdet är **infoga**) |
| writeBatchSize | Egenskapen **writeBatchSize** styr storleken på dokument som ska skrivas i varje batch. Du kan prova att öka värdet för **writeBatchSize** för att förbättra prestandan och minska värdet om dokument storleken är stor. |Inga<br />(standardvärdet är **10 000**) |
| writeBatchTimeout | Vänte tiden för att infoga batch-operationen ska avslutas innan den nådde tids gränsen. Det tillåtna värdet är TimeSpan. | Inga<br/>(Standardvärdet är **00:30:00** – 30 minuter) |

>[!TIP]
>Information om hur du importerar JSON-dokument finns i avsnittet [Importera eller exportera JSON-dokument](#import-and-export-json-documents) . Om du vill kopiera från tabellbaserade data, se [schema mappning](#schema-mapping).

**Exempel**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
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
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>Importera och exportera JSON-dokument

Du kan använda den här Azure Cosmos DB-anslutningen för att enkelt:

* Kopiera mellan två Azure Cosmos DB-samlingar som – är.
* Importera JSON-dokument från olika källor till Azure Cosmos DB, inklusive från MongoDB, Azure Blob Storage, Azure Data Lake Store och andra filbaserade arkiv som Azure Data Factory stöder.
* Exportera JSON-dokument från en Azure Cosmos DB-samling till olika filbaserade lager.

Få schemaoberoende kopia:

* När du använder verktyget kopieringsdata, Välj den **exportera som – är att JSON-filer eller Cosmos DB-samling** alternativet.
* När du använder redigering av aktiviteter väljer du JSON-format med motsvarande fil Arkiv för källa eller mottagare.

## <a name="schema-mapping"></a>Schemamappning

Om du vill kopiera data från Azure Cosmos DBs API för MongoDB till tabell mottagare eller omvänd, referera till [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

För att kunna skriva till Cosmos DB, för att se till att du fyller Cosmos DB med rätt objekt-ID från dina källdata, till exempel har du en "ID"-kolumn i SQL Database-tabellen och vill använda värdet som dokument-ID i MongoDB för INSERT/upsert, måste du ange rätt schema mappning enligt MongoDB strikt läges definition (`_id.$oid`) enligt följande:

![Kart-ID i MongoDB-mottagare](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Efter körningen av kopierings aktiviteten skapas under BSON ObjectId i Sink:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
