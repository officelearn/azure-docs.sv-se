---
title: Kopiera data från MongoDB
description: Lär dig hur du kopierar data från Mongo DB till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: a7bb74c09b45429a160a3ec481c23073575cfe3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251718"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiera data från MongoDB med Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från en MongoDB-databas. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

>[!IMPORTANT]
>ADF släpper den här nya versionen av MongoDB-anslutning som ger bättre inbyggt MongoDB-stöd. Om du använder den tidigare MongoDB-anslutningen i din lösning som stöds som är för bakåtkompatibilitet, se [MongoDB-anslutningsartikel (äldre).](connector-mongodb-legacy.md)

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från MongoDB-databasen till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Specifikt stöder den här MongoDB-anslutningen **versioner upp till 3.4**.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för MongoDB-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för MongoDB-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste ställas in på: **MongoDbV2** |Ja |
| Connectionstring |Ange anslutningssträngen MongoDB `mongodb://[username:password@]host[:port][/[database][?options]]`t.ex. Se [MongoDB manual på anslutningssträngen](https://docs.mongodb.com/manual/reference/connection-string/) för mer information. <br/><br /> Du kan också placera ett lösenord i `password` Azure Key Vault och hämta konfigurationen ur anslutningssträngen. Se [Store-autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) med mer information. |Ja |
| databas | Namn på den databas som du vill komma åt. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

**Exempel:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper stöds för MongoDB-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **MongoDbV2Collection** | Ja |
| collectionName (samlingsnamn) |Namn på samlingen i MongoDB-databasen. |Ja |

**Exempel:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av MongoDB-källan.

### <a name="mongodb-as-source"></a>MongoDB som källa

Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **MongoDbV2Source** | Ja |
| filter | Anger markeringsfilter med frågeoperatorer. Om du vill returnera alla dokument i en samling{}utelämnar du den här parametern eller skickar ett tomt dokument ( ). | Inga |
| cursorMethods.project | Anger de fält som ska returneras i dokumenten för projektion. Om du vill returnera alla fält i de matchande dokumenten utelämnar du den här parametern. | Inga |
| cursorMethods.sort | Anger i vilken ordning frågan returnerar matchande dokument. Se [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Inga |
| cursorMethods.limit | Anger det maximala antalet dokument som servern returnerar. Se [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Inga |
| cursorMethods.skip | Anger antalet dokument som ska hoppa över och var MongoDB börjar returnera resultat. Se [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Inga |
| batchSize | Anger antalet dokument som ska returneras i varje batch av svaret från MongoDB-instansen. I de flesta fall påverkar inte ändringen av batchstorleken användaren eller programmet. Cosmos DB begränsar varje batch får inte överstiga 40 MB i storlek, vilket är summan av batchSize antal dokumentstorlek, så minska detta värde om dokumentstorleken är stor. | Inga<br/>(standard är **100**) |

>[!TIP]
>ADF stöder att använda BSON-dokument i **strikt läge**. Kontrollera att filterfrågan är i strikt läge i stället för Shell-läge. Mer beskrivning finns på [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

## <a name="export-json-documents-as-is"></a>Exportera JSON-dokument i dess

Du kan använda den här MongoDB-kopplingen för att exportera JSON-dokument som de är från en MongoDB-samling till olika filbaserade butiker eller till Azure Cosmos DB. Om du vill uppnå en sådan schemaa-agnostisk kopia hoppar du över avsnittet "struktur" (kallas även *schema)* i datauppsättning och schemamappning i kopieringsaktivitet.

## <a name="schema-mapping"></a>Schemamappning

Om du vill kopiera data från MongoDB till tabellmottagaren läser du [schemamappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
