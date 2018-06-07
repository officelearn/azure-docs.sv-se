---
title: Kopiera data till sökindex med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig mer om hur du push eller kopiera data till ett Azure search-index med hjälp av aktiviteten kopiera i ett Azure Data Factory-pipelinen.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 280c91d6a871984959d587e9895166853e89cc45
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615690"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Kopiera data till en Azure Search-index med Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-azure-search-connector.md)
> * [Version 2 – förhandsversion](connector-azure-search.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data till Azure Search index. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Azure Search-kopplingen i V1](v1/data-factory-azure-search-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla datalager stöds källa till Azure Search-index. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera entiteter i Data Factory för Azure Search-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Azure Search länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AzureSearch** | Ja |
| url | URL för Azure Search-tjänsten. | Ja |
| key | Admin-nyckel för Azure Search-tjänsten. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

> [!IMPORTANT]
> När du kopierar data från ett dataarkiv som molnet till Azure Search index i Azure Search länkade tjänsten, måste du referera ett Azure-integrering för körning med explicit region i connactVia. Ange regionen som Azure Search finns. Mer information från [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Search dataset.

Om du vill kopiera data till Azure Search, ange egenskapen type för datauppsättningen till **RelationalTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **AzureSearchIndex** | Ja |
| Indexnamn | Namnet på det Azure Search-indexet. Data Factory kan inte skapa indexet. Index måste finnas i Azure Search. | Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Search-källa.

### <a name="azure-search-as-sink"></a>Azure Search som mottagare

Om du vill kopiera data till Azure Search, anger du datakällan i kopieringsaktiviteten till **AzureSearchIndexSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **AzureSearchIndexSink** | Ja |
| WriteBehavior | Anger om du vill slå samman eller ersätta ett dokument som redan finns i indexet. Finns det [WriteBehavior egenskapen](#writebehavior-property).<br/><br/>Tillåtna värden är: **sammanfoga** (standard), och **överför**. | Nej |
| writeBatchSize | Överför data till Azure Search index när buffertstorleken når writeBatchSize. Finns det [WriteBatchSize egenskapen](#writebatchsize-property) mer information.<br/><br/>Tillåtna värden är: heltal 1 till 1 000; Standardvärdet är 1000. | Nej |

### <a name="writebehavior-property"></a>Egenskapen WriteBehavior

AzureSearchSink upserts när data skrivs. När du skriver ett dokument, om dokumentnyckeln finns redan i Azure Search-index kan uppdateras med andra ord Azure Search befintligt dokument i stället för att en konflikt undantag.

AzureSearchSink innehåller följande två upsert beteenden (med hjälp av AzureSearch SDK):

- **Sammanfoga**: kombinera alla kolumner i det nya dokumentet med den befintliga. Värdet i den befintliga versionen bevaras i kolumner med null-värde i det nya dokumentet.
- **Överför**: nytt dokument ersätter den befintliga versionen. För kolumner som inte har angetts i det nya dokumentet har värdet null om det finns ett icke-null-värde i det befintliga dokumentet eller inte.

Standardbeteendet är **sammanfoga**.

### <a name="writebatchsize-property"></a>Egenskapen WriteBatchSize

Azure Search-tjänsten stöder skrivning dokument som en batch. En grupp kan innehålla 1 till 1 000 åtgärder. En åtgärd som hanterar ett dokument för att genomföra överföringen/merge-operation.

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

| Azure Search-datatyp | Stöds i Azure Search Sink |
| ---------------------- | ------------------------------ |
| Sträng | Y |
| Int32 | Y |
| Int64 | Y |
| dubbla | Y |
| Boolesk | Y |
| DataTimeOffset | Y |
| Strängmatris | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).
