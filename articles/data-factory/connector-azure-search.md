---
title: Kopiera data till Search-index med hjälp av Azure Data Factory | Microsoft Docs
description: Läs mer om hur du push- eller kopiera data till ett Azure search-index med hjälp av Kopieringsaktiviteten i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: aa6c6a35a66569d5db182e1871012b9697c2802c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023353"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Kopiera data till ett Azure Search-index med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-search-connector.md)
> * [Aktuell version](connector-azure-search.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till Azure Search-index. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla dataarkiv i Azure Search-index. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Search connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Azure Search länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **AzureSearch** | Ja |
| url | URL för Azure Search-tjänsten. | Ja |
| key | Admin-nyckel för Azure Search-tjänsten. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

> [!IMPORTANT]
> När du kopierar data från ett molndatalager till Azure Search-index i Azure Search länkad tjänst, måste du referera en Azure Integration Runtime med explicita region i connactVia. Ange regionen som din Azure Search finns. Läs mer i [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Exempel:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Search-datauppsättningen.

Om du vill kopiera data till Azure Search, ange typegenskapen på datauppsättningen till **RelationalTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **AzureSearchIndex** | Ja |
| Indexnamn | Namnet på Azure Search-index. Data Factory skapar inte indexet. Indexet måste finnas i Azure Search. | Ja |

**Exempel:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Search-källa.

### <a name="azure-search-as-sink"></a>Azure Search som mottagare

Om du vill kopiera data till Azure Search, ange typ av datakälla i kopieringsaktiviteten till **AzureSearchIndexSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **AzureSearchIndexSink** | Ja |
| WriteBehavior | Anger om du vill slå samman eller Ersätt när det finns redan ett dokument i indexet. Se den [WriteBehavior egenskapen](#writebehavior-property).<br/><br/>Tillåtna värden är: **Sammanfoga** (standard), och **överför**. | Nej |
| WriteBatchSize | Överför data till Azure Search-indexet när buffertstorleken når writeBatchSize. Se den [WriteBatchSize egenskapen](#writebatchsize-property) mer information.<br/><br/>Tillåtna värden är: heltal 1 och 1 000; Standardvärdet är 1000. | Nej |

### <a name="writebehavior-property"></a>WriteBehavior egenskap

AzureSearchSink upsertar när du skriver data. När du skriver ett dokument om dokumentnyckeln som redan finns i Azure Search-index, uppdaterar Azure Search med andra ord befintligt dokument i stället för att utlöste ett undantag i konflikt.

AzureSearchSink innehåller följande två upsert beteenden (med hjälp av AzureSearch SDK):

- **Sammanfoga**: kombinera alla kolumner i det nya dokumentet med den befintliga. Värdet i den befintliga påverkas bevaras för kolumner med null-värde i det nya dokumentet.
- **Ladda upp**: Det nya dokumentet ersätter den befintliga påverkas. För kolumner som inte har angetts i det nya dokumentet har värdet null om det finns ett icke-null-värde i det befintliga dokumentet eller inte.

Standardbeteendet är **sammanfoga**.

### <a name="writebatchsize-property"></a>WriteBatchSize egenskap

Azure Search-tjänsten stöder skrivning dokument som en batch. En grupp kan innehålla 1 och 1 000 åtgärder. En åtgärd hanterar ett dokument för att utföra uppladdning/merge-operation.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>Stöd för datatypen

I följande tabell anger om en Azure Search-datatyp stöds eller inte.

| Azure Search-datatyp | Stöds i Azure Search-mottagare |
| ---------------------- | ------------------------------ |
| Sträng | Y |
| Int32 | Y |
| Int64 | Y |
| Double-värde | Y |
| Boolesk | Y |
| DataTimeOffset | Y |
| Strängmatris | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
