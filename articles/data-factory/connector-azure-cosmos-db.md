---
title: "Kopiera data till/från Azure Cosmos-databasen med Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från stöds källa datalager till Azure Cosmos DB (eller) från Cosmos-DB stöds sink Arkiv med hjälp av Data Factory."
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 7d914684a0ee5598cee7972b78c3ec6296184466
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Kopiera data till och från Azure Cosmos-databasen med Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-azure-documentdb-connector.md)
> * [Version 2 – förhandsversion](connector-azure-cosmos-db.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från och till Azure Cosmos DB (DocumentDB-API). Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Azure Cosmos DB connnector i V1](v1/data-factory-azure-documentdb-connector.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data från Azure Cosmos DB till alla stöds sink-datalagret eller kopiera data från alla datalager stöds källa till Azure Cosmos DB. En lista över datakällor som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Azure DB som Cosmos-anslutningen:

- Cosmos DB [DocumentDB API](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-introduction).
- JSON-dokument som import/export-är eller kopiera data från/till tabular dataset t.ex. SQL-databas, CSV-filer, osv.

Dokument som att kopiera-är till/från JSON-filer eller en annan Cosmos DB samling finns [Import/Export JSON-dokument](#importexport-json-documents).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Azure Cosmos DB.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Azure Cosmos DB länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **CosmosDb**. | Ja |
| connectionString |Ange information som behövs för att ansluta till Azure DB som Cosmos-databasen. Observera att du måste ange Databasinfo i anslutningssträngen som under exempel. Markera det här fältet som en SecureString. |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cosmos DB dataset.

Ange typegenskapen för dataset för att kopiera data från/till Azure Cosmos DB, **DocumentDbCollection**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **DocumentDbCollection** |Ja |
| Samlingsnamn |Namnet på samlingen Cosmos DB dokumentet. |Ja |

**Exempel:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema som Data Factory

För schemafria data butiker, till exempel Azure Cosmos DB härleder kopieringsaktiviteten schemat på något av följande sätt. Därför, om du inte vill [import/export av JSON-dokument som-är](#importexport-json-documents), det bästa sättet är att ange strukturen för data i den **struktur** avsnittet.

1. Om du anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory-tjänsten godkänner den här strukturen som schema. Om en rad inte innehåller ett värde för en kolumn, tillhandahålls i det här fallet ett null-värde för den.
2. Om du inte anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory-tjänsten härleder schemat med hjälp av den första raden i data. I det här fallet om den första raden inte innehåller fullständig schemat kommer vissa kolumner att saknas i resultatet av kopieringsåtgärden.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cosmos DB källa och mottagare.

### <a name="azure-cosmos-db-as-source"></a>Azure Cosmos-DB som källa

Om du vill kopiera data från Azure Cosmos DB, anger du källa i kopieringsaktiviteten till **DocumentDbCollectionSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **DocumentDbCollectionSource** |Ja |
| DocumentDB |Ange Cosmos-DB-frågan som läser data.<br/><br/>Exempel:`SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nej <br/><br/>Om inget annat anges, SQL-instruktionen som körs:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som visar att dokumentet är kapslad och hur till flattern resultatuppsättningen.<br/><br/>Om exempelvis en Cosmos-DB-fråga returnerar ett kapslat resultat `"Name": {"First": "John"}`, kopieringsaktiviteten identifierar kolumnnamn som ”Name.First” med värdet ”John” när nestedSeparator är punkt. |Nej (standardvärdet är punkt `.`) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
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
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure Cosmos-DB som mottagare

Om du vill kopiera data från Azure Cosmos DB, anger du sink i kopieringsaktiviteten till **DocumentDbCollectionSink**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **DocumentDbCollectionSink** |Ja |
| nestingSeparator |Ett specialtecken i källkolumnsnamnet att ange kapslade dokumentet krävs. <br/><br/>Till exempel `Name.First` i datamängd för utdata struktur genererar följande JSON-strukturen i dokumentet DB Cosmos:`"Name": {"First": "[value maps to this column from source]"}` när nestedSeparator är punkt. |Nej (standardvärdet är punkt `.`) |
| writeBatchTimeout |Vänta tills åtgärden har slutförts innan tidsgränsen uppnås.<br/><br/>Tillåtna värden är: timespan. Exempel ”: 00: 30:00” (30 minuter). |Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
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
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Importera och exportera JSON-dokument

Den här Cosmos-DB-anslutningen kan du enkelt

* Importera JSON-dokument från olika källor till Cosmos-DB, inklusive Azure Blob, Azure Data Lake Store och andra filbaserade butiker som stöds av Azure Data Factory.
* Exportera JSON-dokument från Cosmos DB collecton till olika filbaserade butiker.
* Kopiera mellan två Cosmos DB samlingar som-är.

Att uppnå sådan schema-oberoende kopia:

- I Cosmos DB datauppsättning/ar, inte anges i avsnittet ”struktur”; och i en Kopieringsaktivitet Cosmos DB källor/mottagare inte ange egenskapen ”nestingSeparator”.
- När import från / exporterar till JSON-filer i motsvarande filen store dataset anger formatet som ”JsonFormat” och config ”filePattern” korrekt (se [JSON-format](supported-file-formats-and-compression-codecs.md#json-format) information), sedan ange inte ”-struktur ”avsnittet och hoppa över inställningarna för rest-format.

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).