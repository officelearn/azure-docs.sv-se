---
title: Schemamappning i kopieringsaktivitet
description: Lär dig mer om hur kopierings aktivitet i Azure Data Factory mappar scheman och data typer från källdata för att ta med data när data kopieras.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423647"
---
# <a name="schema-mapping-in-copy-activity"></a>Schemamappning i kopieringsaktivitet

I den här artikeln beskrivs hur Azure Data Factory kopierings aktivitet använder schema mappning och data typs mappning från källdata för att visa data när data kopian körs.

## <a name="schema-mapping"></a>Schema mappning

Kolumn mappning gäller när du kopierar data från källa till mottagare. Som standard **konverteras aktivitets data till Sink efter kolumn namn**. Du kan ange [explicit mappning](#explicit-mapping) för att anpassa kolumn mappningen utifrån dina behov. Mer specifikt, kopierings aktivitet:

1. Läs data från källan och fastställ käll schemat
2. Använd standard kolumn mappning för att mappa kolumner efter namn eller Använd explicit kolumn mappning om det anges.
3. Skriv data till Sink

### <a name="explicit-mapping"></a>Explicit mappning

Du kan ange vilka kolumner som ska mappas i kopierings aktiviteten – > `translator` -> `mappings` egenskap. I följande exempel definieras en kopierings aktivitet i en pipeline för att kopiera data från avgränsad text till Azure SQL Database.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

Följande egenskaper stöds under `translator` -> `mappings`->-objekt med `source` och `sink`:

| Egenskap | Beskrivning                                                  | Krävs |
| -------- | ------------------------------------------------------------ | -------- |
| namn     | Namnet på källan eller kolumnen Sink.                           | Ja      |
| Numret  | Kolumn index. Börja med 1. <br>Använd och krävs när du använder avgränsad text utan rubrik rad. | Nej       |
| path     | Uttryck för JSON-sökvägar för varje fält som ska extraheras eller mappas. Ansök om hierarkiska data, t. ex. MongoDB/REST.<br>För fält under rotobjektet börjar JSON-sökvägen med roten $; för fält i matrisen som valts av `collectionReference` egenskap börjar JSON-sökvägen från mat ris elementet. | Nej       |
| typ     | Data Factory data typen datatyp för kolumnen källa eller mottagare. | Nej       |
| culture  | Kultur för kolumnen källa eller mottagare. <br>Använd när typen är `Datetime` eller `Datetimeoffset`. Standardvärdet är `en-us`. | Nej       |
| format   | Format strängen som ska användas när typen är `Datetime` eller `Datetimeoffset`. Referera till [anpassade datum-och tids format strängar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) för hur du formaterar DateTime. | Nej       |

Följande egenskaper stöds under `translator` -> `mappings` utöver objekt med `source` och `sink`:

| Egenskap            | Beskrivning                                                  | Krävs |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Stöds bara när hierarkiska data, t. ex. MongoDB/REST är källa.<br>Om du vill iterera och extrahera data från objekten **inuti ett mat ris fält** med samma mönster och konvertera till per rad per objekt, anger du JSON-sökvägen för den matrisen för att göra kors koppling. | Nej       |

### <a name="alternative-column-mapping"></a>Alternativ kolumn mappning

Du kan ange kopierings aktivitet – > `translator` -> `columnMappings` för att mappa mellan tabellbaserade data. I det här fallet krävs avsnittet "struktur" för både data uppsättningar för indata och utdata. Kolumn mappning stöder **Mappning av alla eller delmängd av kolumner i käll data uppsättningens struktur till alla kolumner i data uppsättningen för Sink-datauppsättningen**. Följande är fel villkor som resulterar i ett undantag:

* Frågeresultatet för käll data lagret har inget kolumn namn som är angivet i avsnittet struktur för indata-DataSet.
* Sink-datalagret (om med fördefinierat schema) inte har något kolumn namn som anges i "struktur"-avsnittet för utdata-datauppsättningen.
* Antingen färre kolumner eller fler kolumner i "struktur" för mottagar data uppsättningen än vad som anges i mappningen.
* Duplicera mappning.

I följande exempel har indata-datauppsättningen en struktur och den pekar på en tabell i en lokal Oracle-databas.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

I det här exemplet har data uppsättningen för utdata en struktur och den pekar på en tabell i Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Följande JSON definierar en kopierings aktivitet i en pipeline. Kolumnerna från källan mappas till kolumner i Sink med hjälp av **Translator** - -> **columnMappings** -egenskapen.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Om du använder syntaxen för `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` för att ange kolumn mappning, stöds den fortfarande som-är.

### <a name="alternative-schema-mapping"></a>Alternativ schema mappning

Du kan ange kopierings aktivitet – > `translator` -> `schemaMapping` för att mappa mellan hierarkiskt data och tabellbaserade data, t. ex. Kopiera från MongoDB/REST till textfil och kopiera från Oracle till Azure Cosmos DBs API för MongoDB. Följande egenskaper stöds i avsnittet Kopiera aktivitet `translator`:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets översättaren måste anges till: **TabularTranslator** | Ja |
| schemaMapping | En samling nyckel/värde-par som representerar mappnings relationen **från käll sidan till mottagar sidan**.<br/>- **nyckel:** representerar källa. För **tabell källa**anger du kolumn namnet som definierats i data uppsättnings strukturen. för **hierarkisk källa**anger du JSON-sökvägar för varje fält som ska extraheras och mappas.<br>- **värde:** representerar mottagare. För **tabell mottagare**anger du kolumn namnet enligt definitionen i data uppsättnings strukturen. för **hierarkisk mottagare**anger du JSON-sökvägar för varje fält som ska extraheras och mappas. <br>Om det finns hierarkiska data för fält under rot objekt börjar JSON-sökvägen med roten $; för fält i matrisen som valts av `collectionReference` egenskap börjar JSON-sökvägen från mat ris elementet.  | Ja |
| collectionReference | Om du vill iterera och extrahera data från objekten **inuti ett mat ris fält** med samma mönster och konvertera till per rad per objekt, anger du JSON-sökvägen för den matrisen för att göra kors koppling. Den här egenskapen stöds endast när hierarkiska data är källa. | Nej |

**Exempel: kopiera från MongoDB till Oracle:**

Om du till exempel har MongoDB-dokument med följande innehåll:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

och du vill kopiera den till en Azure SQL-tabell i följande format, genom att förenkla data i matrisen *(order_pd och order_price)* och korsa anslutning med den gemensamma rot informationen *(nummer, datum och ort)* :

| orderNumber | Datum | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Konfigurera schema mappnings regeln som följande JSON-exempel för kopierings aktiviteter:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Data typs mappning

Kopierings aktiviteten utför käll typer för att avbilda typ mappningar med följande 2-steg-metod:

1. Konvertera från interna käll typer till Azure Data Factory interimistiska data typer
2. Konvertera från Azure Data Factory interimistiska data typer till intern mottagar typ

Du hittar mappningen mellan typ och typ i avsnittet "data typs mappning" i varje anslutnings ämne.

### <a name="supported-data-types"></a>Data typer som stöds

Data Factory stöder följande Interimistiska data typer: du kan ange värden nedan när du konfigurerar typ information i [data uppsättnings struktur](concepts-datasets-linked-services.md#dataset-structure-or-schema) konfiguration:

* Byte[]
* Boolean
* Datum/tid
* DateTimeOffset
* decimaltal
* Double-värde
* Guid
* Int16
* Int32
* Int64
* Enkel
* String
* Tidsintervall

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna i Kopieringsaktiviteten:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
