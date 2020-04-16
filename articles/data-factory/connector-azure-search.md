---
title: Kopiera data till sökindex
description: Lär dig mer om hur du skickar eller kopierar data till ett Azure-sökindex med hjälp av kopieringsaktiviteten i en Azure Data Factory-pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: dfa1ad318ccc9e891b646ec050f6a0776e108206
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418243"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Kopiera data till ett Azure Cognitive Search-index med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-search-connector.md)
> * [Aktuell version](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data till Azure Cognitive Search index. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla källdatalager som stöds till sökindex. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Cognitive Search-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Azure Cognitive Search-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till: **AzureSearch** | Ja |
| url | URL för söktjänsten. | Ja |
| key | Administratörsnyckel för söktjänsten. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

> [!IMPORTANT]
> När du kopierar data från ett molndatalager till sökindex i Azure Cognitive Search-länkad tjänst måste du referera till en Azure Integration Runtime med explicit region i connactVia. Ange regionen som den region där söktjänsten finns. Läs mer från [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cognitive Search-datauppsättningen.

Så här kopierar du data till Azure Cognitive Search och följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för datauppsättningen måste anges till: **AzureSearchIndex** | Ja |
| indexNamn | Namn på sökindexet. Data Factory skapar inte indexet. Indexet måste finnas i Azure Cognitive Search. | Ja |

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
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cognitive Search-källa.

### <a name="azure-cognitive-search-as-sink"></a>Azure Cognitive Search som mottagare

Om du vill kopiera data till Azure Cognitive Search anger du källtypen i kopieringsaktiviteten till **AzureSearchIndexSink**. Följande egenskaper stöds i avsnittet kopiera **aktivitetsmottagare:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **AzureSearchIndexSink** | Ja |
| skriverBeteende | Anger om det ska slås samman eller ersätta när det redan finns ett dokument i indexet. Se [egenskapen WriteBehavior](#writebehavior-property).<br/><br/>Tillåtna värden är: **Sammanfoga** (standard) och **Ladda upp**. | Inga |
| skriverBatchSize | Överför data till sökindexet när buffertstorleken når writeBatchSize. Mer information finns i [egenskapen WriteBatchSize.](#writebatchsize-property)<br/><br/>Tillåtna värden är: heltal 1 till 1 000; standard är 1000. | Inga |

### <a name="writebehavior-property"></a>WriteBehavior egendom

AzureSearchSink upserts när du skriver data. Med andra ord, när du skriver ett dokument, om dokumentnyckeln redan finns i sökindexet, uppdaterar Azure Cognitive Search det befintliga dokumentet i stället för att utlösa ett konfliktundantag.

AzureSearchSink innehåller följande två upsert-beteenden (med hjälp av AzureSearch SDK):

- **Sammanfoga:** kombinera alla kolumner i det nya dokumentet med det befintliga dokumentet. För kolumner med null-värde i det nya dokumentet bevaras värdet i det befintliga.
- **Ladda upp**: Det nya dokumentet ersätter det befintliga dokumentet. För kolumner som inte anges i det nya dokumentet anges värdet till null om det finns ett värde som inte är null i det befintliga dokumentet eller inte.

Standardbeteendet är **Koppla**.

### <a name="writebatchsize-property"></a>Egenskapen WriteBatchSize

Azure Cognitive Search-tjänsten stöder att skriva dokument som en batch. En batch kan innehålla 1 till 1 000 åtgärder. En åtgärd hanterar ett dokument för att utföra överföringen/kopplingen.

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
                "referenceName": "<Azure Cognitive Search output dataset name>",
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

## <a name="data-type-support"></a>Stöd för datatyp

Följande tabell anger om en Azure Cognitive Search-datatyp stöds eller inte.

| Datatyp för Azure Cognitive Search | Stöds i Azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| Sträng | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolesk | Y |
| DataTimeOffset | Y |
| Strängmatris | N |
| GeografiPoint | N |

För närvarande stöds inte andra datatyper, t.ex. En fullständig lista över datatyper som stöds av Azure Cognitive Search finns [i Datatyper som stöds (Azure Cognitive Search).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
