---
title: Kopiera data till och från Azure Cosmos DB med Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från datalager som stöds till Azure Cosmos DB (eller) från Cosmos DB till mottagarens datalager med Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
ms.openlocfilehash: 92b45c1038fd099926360dc80802ababf0e8ee93
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37052774"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Kopiera data till och från Azure Cosmos DB med Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuell version](connector-azure-cosmos-db.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från och till Azure Cosmos DB (SQL-API). Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure Cosmos DB till alla mottagare som stöds eller kopiera data från alla dataarkiv till Azure Cosmos DB. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Azure Cosmos DB-anslutningen:

- Cosmos DB [SQL API: T](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Importerar/exporterar JSON-dokument som – är eller kopiera data från/till datauppsättning i tabellformat t.ex. SQL-databas, CSV-filer, osv.

Dokument som att kopiera-är till och från JSON-filer eller en annan Cosmos DB-samling finns i [Import/Export JSON-dokument](#importexport-json-documents).

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Cosmos DB.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Azure Cosmos DB-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **CosmosDb**. | Ja |
| connectionString |Ange information som behövs för att ansluta till Azure Cosmos DB-databas. Observera att du måste ange information för databasen i anslutningssträngen som nedan exemplet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cosmos DB-datauppsättningen.

För att kopiera data från/till Azure Cosmos DB, ange typegenskapen på datauppsättningen till **DocumentDbCollection**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **DocumentDbCollection** |Ja |
| Samlingsnamn |Namnet på Cosmos DB-dokumentsamling. |Ja |

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

### <a name="schema-by-data-factory"></a>Schemat av Data Factory

För schemafria datalager som Azure Cosmos DB härleder kopieringsaktiviteten schemat på något av följande sätt. Därför, om du inte vill [import/export JSON-dokument som-är](#importexport-json-documents), är bra att anger strukturen för data i den **struktur** avsnittet.

*. Om du anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory-tjänsten godkänner den här strukturen som schema. I det här fallet ges en rad inte innehåller ett värde för en kolumn, ett null-värde för den.
*. Om du inte anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory-tjänsten härleder scheman med hjälp av den första raden i data. I det här fallet om den första raden inte innehåller fullständig schemat, blir vissa kolumner saknas i resultatet för kopieringsåtgärden.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cosmos DB-datakälla och mottagare.

### <a name="azure-cosmos-db-as-source"></a>Azure Cosmos DB som källa

För att kopiera data från Azure Cosmos DB, ange typ av datakälla i kopieringsaktiviteten till **DocumentDbCollectionSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **DocumentDbCollectionSource** |Ja |
| DocumentDB |Ange Cosmos DB-fråga för att läsa data.<br/><br/>Exempel: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nej <br/><br/>Om inte anges, SQL-instruktionen som körs: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som visar att dokumentet är kapslade och hur till flattern för resultatuppsättningen.<br/><br/>Exempel: om en Cosmos DB-fråga returnerar ett kapslade resultat `"Name": {"First": "John"}`, Kopieringsaktivitet ska identifieras kolumnnamn som ”Name.First” med värdet ”John” när nestedSeparator är punkt. |Nej (standard är punkt `.`) |

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

### <a name="azure-cosmos-db-as-sink"></a>Azure Cosmos DB som mottagare

För att kopiera data till Azure Cosmos DB, ange Mottagartyp i kopieringsaktiviteten till **DocumentDbCollectionSink**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till: **DocumentDbCollectionSink** |Ja |
| nestingSeparator |Specialtecken i källkolumnsnamnet som visar det kapslade dokumentet krävs. <br/><br/>Till exempel `Name.First` i datauppsättningen för utdata struktur genererar följande JSON-strukturen i Cosmos DB-dokument:`"Name": {"First": "[value maps to this column from source]"}` när nestedSeparator är punkt. |Nej (standard är punkt `.`) |

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

## <a name="importexport-json-documents"></a>Import/Export JSON-dokument

Den här Cosmos DB-anslutningen kan du enkelt

* Importera JSON-dokument från olika källor till Cosmos DB, såsom Azure Blob, Azure Data Lake Store och andra filbaserade butiker som stöds av Azure Data Factory.
* Exportera JSON-dokument från Cosmos DB collecton till olika filbaserade lager.
* Kopiera mellan två Cosmos DB-samlingar som – är.

Få kopian schemaoberoende:

* När du använder verktyget för att kopiera data, kontrollera den **”exportera som – är att JSON-filer eller Cosmos DB-samling”** alternativet.
* När med redigering av aktivitet, inte anger avsnittet ”struktur” (även kallat schema) i Cosmos DB-datauppsättningar och inte heller ”nestingSeparator”-egenskapen i Cosmos DB källa/mottagare i kopieringsaktiviteten. När import från / exportera till JSON-filer i motsvarande filen store datauppsättningen anger formattyp som ”JsonFormat” och config ”filePattern” korrekt (se [JSON-format](supported-file-formats-and-compression-codecs.md#json-format) information), sedan ange inte ”-struktur ”(även kallat schema) avsnittet och hoppar över inställningar för rest-format.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
