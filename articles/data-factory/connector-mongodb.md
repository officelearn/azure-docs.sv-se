---
title: Kopiera data från MongoDB med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Mongo DB till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 1e0d6fe79d23f3d3d0679445f5120a3ec17ddd67
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013782"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiera data från MongoDB med Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en MongoDB-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

>[!IMPORTANT]
>ADF släpp den här nya versionen av MongoDB-anslutningsappen som ger bättre inbyggt stöd för MongoDB. Om du använder tidigare MongoDB-anslutningsappen i din lösning som stöds som – är för bakåtkompatibilitet, referera till [MongoDB-anslutningsappen (äldre)](connector-mongodb-legacy.md) artikeln.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från MongoDB-databas till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder MongoDB-anslutningsappen **versioner upp till 3.4**.

## <a name="prerequisites"></a>Förutsättningar

Om du vill kopiera data från en MongoDB-databas som inte är allmänt tillgänglig, måste du konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) artikeln om du vill få mer detaljerad information.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för MongoDB-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för MongoDB-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Type-egenskapen måste anges till: **MongoDbV2** |Ja |
| connectionString |Ange t.ex. MongoDB-anslutningssträng `mongodb://[username:password@]host[:port][/[database][?options]]`. Referera till [MongoDB manuell på anslutningssträngen](https://docs.mongodb.com/manual/reference/connection-string/) för mer information. <br/><br />Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| databas | Namnet på databasen som du vill komma åt. | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
            },
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper har stöd för MongoDB-datauppsättning:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **MongoDbV2Collection** | Ja |
| Samlingsnamn |Namnet på samlingen i MongoDB-databas. |Ja |

**Exempel:**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av MongoDB-källa.

### <a name="mongodb-as-source"></a>MongoDB som källa

Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **MongoDbV2Source** | Ja |
| filter | Anger val av filter som använder frågeoperatorer. Om du vill returnera alla dokument i en samling, utelämnar den här parametern eller skicka ett tomt dokument ({}). | Nej |
| cursorMethods.project | Anger fälten att returnera i dokumenten för projektion. Om du vill returnera alla fält i matchande dokument, utelämnar du den här parametern. | Nej |
| cursorMethods.sort | Anger den ordning som frågan returnerar matchande dokument. Referera till [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nej |
| cursorMethods.limit | Anger det maximala antalet dokument som returnerar servern. Referera till [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nej | 
| cursorMethods.skip | Anger hur många av dokument att hoppa över och från där MongoDB börjar att returnera resultat. Referera till [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nej |
| batchSize | Anger antalet dokument att returnera i varje batch för svaret från MongoDB-instans. I de flesta fall påverkar ändra batchstorleken inte användaren eller programmet. Cosmos DB-gränser som varje batch inte får överskrida 40MB i storlek, vilket är summan av batchSize antalet dokumentens storlek, så minska det här värdet om dokumentstorleken på din är stor. | Nej<br/>(standardvärdet är **100**) |

>[!TIP]
>ADM-stödet förbrukar BSON dokument i **strikt läge**. Kontrollera att din filterfråga är i strikt läge i stället för Shell-läge. Mer beskrivning finns på [MongoDB manuell](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

## <a name="export-json-documents-as-is"></a>Exportera JSON-dokument som – är

Du kan använda den här MongoDB-anslutningsappen för att exportera JSON-dokument som – kommer från en MongoDB-samling till olika filbaserade lager eller till Azure Cosmos DB. Hoppa över ”strukturen” för att uppnå kopian schemaoberoende (kallas även *schemat*) i avsnittet datauppsättning och schemamappning i kopieringsaktiviteten.

## <a name="schema-mapping"></a>Schemamappning

För att kopiera data från MongoDB till tabular mottagare, referera till [schemamappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
