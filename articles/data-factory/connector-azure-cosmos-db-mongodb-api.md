---
title: Kopiera data från Azure Cosmos DB:s API för MongoDB
description: Lär dig hur du kopierar data från källdatalager som stöds till eller från Azure Cosmos DB:s API för MongoDB till sink-butiker som stöds med hjälp av Data Factory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893247"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopiera data till eller från Azure Cosmos DB API för MongoDB med Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory till att kopiera data från och till Azure Cosmos DB:s API för MongoDB. Artikeln bygger på [Kopiera aktivitet i Azure Data Factory](copy-activity-overview.md), som presenterar en allmän översikt över kopiera aktivitet.

>[!NOTE]
>Den här anslutningen stöder endast kopieringsdata till/från Azure Cosmos DB:s API för MongoDB. För SQL API, se [Cosmos DB SQL API-anslutning](connector-azure-cosmos-db.md). Andra API-typer stöds inte nu.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure Cosmos DB:s API för MongoDB till alla sink-datalager som stöds eller kopiera data från ett källdatalager som stöds till Azure Cosmos DB:s API för MongoDB. En lista över datalager som Kopierar aktivitet stöder som källor och sänkor finns i [Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Du kan använda Azure Cosmos DB:s API för MongoDB-anslutning till:

- Kopiera data från och till [Azure Cosmos DB:s API för MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Skriv till Azure Cosmos DB som **infogad** eller **upsert**.
- Importera och exportera JSON-dokument i sig eller kopiera data från eller till en tabelldatauppsättning. Exempel är en SQL-databas och en CSV-fil. Information om hur du kopierar dokument som de är till eller från JSON-filer eller till eller från en annan Azure Cosmos DB-samling finns i Importera eller exportera JSON-dokument.

## <a name="get-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera datafabrikentiteter som är specifika för Azure Cosmos DB:s API för MongoDB.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Azure Cosmos DB:s API för MongoDB-länkade tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** måste anges till **CosmosDbMongoDbApi**. | Ja |
| Connectionstring |Ange anslutningssträngen för Azure Cosmos DB:s API för MongoDB. Du hittar den i Azure-portalen -> ditt Cosmos DB-blad -> primär eller `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`sekundär anslutningssträng, med mönstret . <br/><br />Du kan också placera ett lösenord i `password` Azure Key Vault och hämta konfigurationen ur anslutningssträngen.Se [Store-autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) med mer information.|Ja |
| databas | Namn på den databas som du vill komma åt. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en självvärd integreringskörning (om ditt datalager finns i ett privat nätverk). Om den här egenskapen inte anges används standardkörningen för Azure Integration Runtime. |Inga |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper stöds för Azure Cosmos DB:s API för MongoDB-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för datauppsättningen måste anges till **CosmosDbMongoDbApiCollection**. |Ja |
| collectionName (samlingsnamn) |Namnet på Azure Cosmos DB-samlingen. |Ja |

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

Det här avsnittet innehåller en lista över egenskaper som Azure Cosmos DB:s API för MongoDB-käll- och handfats stöd.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB:s API för MongoDB som källa

Följande egenskaper stöds i **källavsnittet** Kopiera aktivitet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för kopians aktivitetskälla måste anges till **CosmosDbMongoDbApiSource**. |Ja |
| filter | Anger markeringsfilter med frågeoperatorer. Om du vill returnera alla dokument i en samling{}utelämnar du den här parametern eller skickar ett tomt dokument ( ). | Inga |
| cursorMethods.project | Anger de fält som ska returneras i dokumenten för projektion. Om du vill returnera alla fält i de matchande dokumenten utelämnar du den här parametern. | Inga |
| cursorMethods.sort | Anger i vilken ordning frågan returnerar matchande dokument. Se [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Inga |
| cursorMethods.limit | Anger det maximala antalet dokument som servern returnerar. Se [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Inga | 
| cursorMethods.skip | Anger antalet dokument som ska hoppa över och var MongoDB börjar returnera resultat. Se [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Inga |
| batchSize | Anger antalet dokument som ska returneras i varje batch av svaret från MongoDB-instansen. I de flesta fall påverkar inte ändringen av batchstorleken användaren eller programmet. Cosmos DB begränsar varje batch får inte överstiga 40 MB i storlek, vilket är summan av batchSize antal dokumentstorlek, så minska detta värde om dokumentstorleken är stor. | Inga<br/>(standard är **100**) |

>[!TIP]
>ADF stöder att använda BSON-dokument i **strikt läge**. Kontrollera att filterfrågan är i strikt läge i stället för Shell-läge. Mer beskrivning finns på [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Azure Cosmos DB:s API för MongoDB som diskbänk

Följande egenskaper stöds i avsnittet Kopiera **aktivitetsmottagare:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för copy activity sink måste anges till **CosmosDbMongoDbApiSink**. |Ja |
| skriverBeteende |Beskriver hur du skriver data till Azure Cosmos DB. Tillåtna värden: **infoga** och **upsert**.<br/><br/>Beteendet **för upsert** är att ersätta dokumentet `_id` om det redan finns ett dokument med samma. Annars infogar du dokumentet.<br /><br />**Data**Factory genererar automatiskt en `_id` för ett `_id` dokument om en inte anges antingen i det ursprungliga dokumentet eller efter kolumnmappning. Det innebär att du måste se till att dokumentet har ett ID för **att** det ska fungera som förväntat. |Inga<br />(standard är **infoga**) |
| skriverBatchSize | Egenskapen **writeBatchSize** styr storleken på de dokument som ska skrivas i varje batch. Du kan försöka öka värdet för **writeBatchSize** för att förbättra prestanda och minska värdet om dokumentstorleken är stor. |Inga<br />(standardvärdet är **10 000**) |
| skriverBatchTimeout | Väntetiden för batchinsatsen ska slutföras innan den time out. Det tillåtna värdet är tidsspann. | Inga<br/>(standard är **00:30:00** - 30 minuter) |

>[!TIP]
>Om du vill importera JSON-dokument i sig läser du avsnittet [Importera eller exportera JSON-dokument.](#import-and-export-json-documents) om du vill kopiera från tabellformade data, se [Schemamappning](#schema-mapping).

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

Du kan enkelt använda den här Azure Cosmos DB-kopplingen för att enkelt:

* Kopiera dokument mellan två Azure Cosmos DB-samlingar som de är.
* Importera JSON-dokument från olika källor till Azure Cosmos DB, inklusive från MongoDB, Azure Blob storage, Azure Data Lake Store och andra filbaserade butiker som Azure Data Factory stöder.
* Exportera JSON-dokument från en Azure Cosmos DB-samling till olika filbaserade butiker.

Så här uppnår du schemaaoberoende kopia:

* När du använder verktyget Kopiera data väljer du alternativet **Exportera som det är till JSON-filer eller Cosmos DB-samlingsalternativ.**
* När du använder aktivitetsförfattare väljer du JSON-format med motsvarande filarkiv för käll- eller diskho.

## <a name="schema-mapping"></a>Schemamappning

Om du vill kopiera data från Azure Cosmos DB:s API för MongoDB till tabellmottagare eller omvända finns [i schemamappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

Specifikt för att skriva i Cosmos DB, för att se till att du fyller Cosmos DB med rätt objekt-ID från källdata, till exempel har du en "id" kolumn i SQL-databastabellen och vill använda värdet för det som`_id.$oid`dokument-ID i MongoDB för att infoga/upsert, måste du ställa in rätt schemamappning enligt MongoDB strikt lägesdefinition ( ) som följande:

![Kart-ID i MongoDB-diskbänk](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Efter kopieringsaktivitetskörning genereras under BSON ObjectId i diskhon:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Nästa steg

En lista över datalager som kopierar aktivitet stöder som källor och sänkor i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
