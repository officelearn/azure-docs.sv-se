---
title: Kopiera data till Sök index med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig mer om att skicka eller kopiera data till ett Azure Search-index med hjälp av kopierings aktiviteten i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: jingwang
ms.openlocfilehash: 703dcb5fee0c31328eee91c9673e62653d7aa698
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286339"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Kopiera data till ett Azure Search-index med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-search-connector.md)
> * [Aktuell version](connector-azure-search.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data till Azure Search index. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla käll data lager som stöds till Azure Search index. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Search koppling.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för Azure Search länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **AzureSearch** | Ja |
| url | URL för Azure Searchs tjänsten. | Ja |
| key | Administratörs nyckel för Azure Searchs tjänsten. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Nej |

> [!IMPORTANT]
> När du kopierar data från ett moln data lager till Azure Search index, i Azure Search länkade tjänster, måste du referera till en Azure Integration Runtime med en explicit region i connactVia. Ange regionen som en Azure Search finns. Läs mer från [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime).

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

## <a name="dataset-properties"></a>Egenskaper för data mängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Search Data uppsättning.

Följande egenskaper stöds för att kopiera data till Azure Search:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **AzureSearchIndex** | Ja |
| indexName | Namnet på Azure Search indexet. Data Factory skapar inte indexet. Indexet måste finnas i Azure Search. | Ja |

**Exempel:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Search källa.

### <a name="azure-search-as-sink"></a>Azure Search som mottagare

Om du vill kopiera data till Azure Search anger du käll typen i kopierings aktiviteten till **AzureSearchIndexSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **AzureSearchIndexSink** | Ja |
| WriteBehavior | Anger om du vill sammanfoga eller ersätta när ett dokument redan finns i indexet. Se [egenskapen WriteBehavior](#writebehavior-property).<br/><br/>Tillåtna värden är: **sammanfoga** (standard) och **Ladda upp**. | Nej |
| writeBatchSize | Överför data till Azure Search index när buffertstorleken når writeBatchSize. Mer information finns i [WriteBatchSize-egenskapen](#writebatchsize-property) .<br/><br/>Tillåtna värden är: heltal 1 till 1 000; Standardvärdet är 1000. | Nej |

### <a name="writebehavior-property"></a>WriteBehavior-egenskap

AzureSearchSink upsertar när data skrivs. Med andra ord, om dokument nyckeln redan finns i Azure Search-index, Azure Search uppdaterar det befintliga dokumentet i stället för att ett konflikt undantag utlöses.

AzureSearchSink innehåller följande två upsert-beteenden (med hjälp av AzureSearch SDK):

- **Sammanslagning**: kombinera alla kolumner i det nya dokumentet med det befintliga. För kolumner med null-värde i det nya dokumentet bevaras värdet i det befintliga.
- **Ladda upp**: det nya dokumentet ersätter det befintliga. För kolumner som inte anges i det nya dokumentet anges värdet null om det inte finns något värde som inte är null i det befintliga dokumentet eller inte.

Standard beteendet **slås samman**.

### <a name="writebatchsize-property"></a>WriteBatchSize-egenskap

Azure Search-tjänsten stöder skrivning av dokument som en batch. En batch kan innehålla 1 till 1 000-åtgärder. En åtgärd hanterar ett dokument för att utföra uppladdnings-/sammanslagnings åtgärden.

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

## <a name="data-type-support"></a>Data typs stöd

I följande tabell anges om en Azure Search data typ stöds eller inte.

| Azure Search data typ | Stöds i Azure Search-mottagare |
| ---------------------- | ------------------------------ |
| Sträng | Y |
| Int32 | Y |
| Int64 | Y |
| Dubbelklicka | Y |
| Boolesk | Y |
| DataTimeOffset | Y |
| Sträng mat ris | N |
| GeographyPoint | N |

För närvarande stöds inte andra data typer, t. ex. ComplexType. En fullständig lista över Azure Search data typ som stöds finns i [data typer som stöds (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
