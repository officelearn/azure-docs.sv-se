---
title: Schemamappning i kopieringsaktiviteten | Microsoft Docs
description: Läs mer om hur Kopieringsaktivitet i Azure Data Factory mappar scheman och datatyper från datakällan för att registrera data när du kopierar data.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9108f83e854b51720c64c5a74a828543cc5e7688
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875809"
---
# <a name="schema-mapping-in-copy-activity"></a>Schemamappning i kopieringsaktivitet

Den här artikeln beskriver hur Azure Data Factory Kopieringsaktivitet utför schemamappning och datatypmappningen från källdata till mottagare data när kör Datakopieringen.

## <a name="schema-mapping"></a>Schemamappning

Kolumnmappningen gäller när du kopierar data från källa till mottagare. Kopiera aktivitet som standard **mappa källdata till mottagare av kolumnnamn**. Du kan ange [explicit mappning](#explicit-mapping) anpassa kolumnmappningen utifrån dina behov. Mer specifikt Kopieringsaktivitet:

1. Läsa data från källan och avgöra källans schema
2. Använd standard kolumnmappningen mappa kolumner efter namn eller tillämpa explicit kolumnmappning om anges.
3. Skriva data till mottagare

### <a name="explicit-mapping"></a>Explicit mappning

Du kan ange vilka kolumner som ska mappa i kopieringsaktiviteten -> `translator`  ->  `mappings` egenskapen. I följande exempel definierar en Kopieringsaktivitet i en pipeline för att kopiera data från avgränsad text till Azure SQL Database.

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

Följande egenskaper stöds `translator`  ->  `mappings` -> objekt med `source` och `sink`:

| Egenskap  | Beskrivning                                                  | Krävs |
| -------- | ------------------------------------------------------------ | -------- |
| namn     | Namnet på källan eller mottagaren kolumn.                           | Ja      |
| ordinal  | Kolumnindex. Börja med 1. <br>Tillämpa och krävs när med avgränsad text utan rubrikrad. | Nej       |
| path     | JSON-sökvägsuttrycket för varje fält ska extraheras eller mappar. Gäller för hierarkiska data t.ex. MongoDB/REST.<br>För fält under rotobjektet, JSON-sökvägen som börjar med $; för fält inuti matrisen som väljs av `collectionReference` egenskapen startar JSON-sökvägen från matriselementet. | Nej       |
| typ     | Data Factory tillfälliga datatypen för kolumnen källan eller mottagaren. | Nej       |
| kultur  | Kulturen kolumnens källan eller mottagaren. <br>Tillämpa när typen är `Datetime` eller `Datetimeoffset`. Standardvärdet är `en-us`. | Nej       |
| Format   | Formatera strängen som ska användas när typen är `Datetime` eller `Datetimeoffset`. Referera till [anpassade datum- och Datumformatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) om hur du formaterar datetime. | Nej       |

Följande egenskaper stöds `translator`  ->  `mappings` förutom objekt med `source` och `sink`:

| Egenskap             | Beskrivning                                                  | Krävs |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Stöds endast när hierarkiska data t.ex. MongoDB/REST är källan.<br>Om du vill iterera och extrahera data från objekten **i ett matrisfält** med samma mönster och konvertera till ange JSON-sökvägen för matrisen för cross-gäller per rad per objekt. | Nej       |

### <a name="alternative-column-mapping"></a>Alternativa kolumnmappning

Du kan ange copy activity -> `translator`  ->  `columnMappings` att mappa mellan tabular-formade data. I det här fallet krävs ”struktur” avsnittet för både in- och utdatauppsättningar. Stöd för mappning av kolumnen **mappning av alla eller en delmängd med kolumner i datauppsättningen för källan ”struktur” på alla kolumner i datauppsättning för mottagare ”struktur”**. Här följer felvillkor som resulterar i ett undantag:

* Källans datalager fråga resultatet inte har ett kolumnnamn som anges i avsnittet ”struktur” datauppsättningen för indata.
* Datalager för mottagare (med fördefinierat schema) har inte ett kolumnnamn som anges i avsnittet ”struktur” utdata-datauppsättning.
* Färre kolumner eller fler kolumner i ”strukturen” för datauppsättning för mottagare än anges i mappningen.
* Duplicera mappning.

I följande exempel datauppsättningen för indata har en struktur och den pekar på en tabell i en lokal Oracle-databas.

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

I det här exemplet datauppsättningen för utdata har en struktur och den pekar på en tabell i Salesfoce.

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

Följande JSON definierar en Kopieringsaktivitet i en pipeline. Kolumner från källan som är mappade till kolumnerna i mottagare med hjälp av den **translator** -> **columnMappings** egenskapen.

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

Om du använder syntaxen för `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` för att ange kolumnmappning, stöds det fortfarande som – är.

### <a name="alternative-schema-mapping"></a>Alternativa schemamappning

Du kan ange copy activity -> `translator`  ->  `schemaMapping` för att mappa mellan hierarkisk formade data och tabular-formade data, t.ex. kopiera från MongoDB/REST till textfilen och kopiera från Oracle till Azure Cosmos DB API för MongoDB. Följande egenskaper stöds i kopieringsaktiviteten `translator` avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för kopiera aktivitet translator måste anges till: **TabularTranslator** | Ja |
| schemaMapping | En samling nyckel / värde-par som representerar mappning relationen **från källan sida till sida för mottagare**.<br/>- **Nyckel:** representerar källan. För **tabular källa**, ange kolumnnamnet som definierats i datauppsättningsstrukturen; för **hierarkiska källa**, ange JSON-sökvägsuttrycket för varje fält för att extrahera och mappa.<br>- **Värde:** representerar mottagare. För **tabular mottagare**, ange kolumnnamnet som definierats i datauppsättningsstrukturen; för **hierarkiska mottagare**, ange JSON-sökvägsuttrycket för varje fält för att extrahera och mappa. <br>När det gäller hierarkiska data för fält under rotobjektet, JSON-sökvägen som börjar med $; för fält inuti matrisen som väljs av `collectionReference` egenskapen startar JSON-sökvägen från matriselementet.  | Ja |
| collectionReference | Om du vill iterera och extrahera data från objekten **i ett matrisfält** med samma mönster och konvertera till ange JSON-sökvägen för matrisen för cross-gäller per rad per objekt. Den här egenskapen stöds endast när hierarkiska data är källan. | Nej |

**Exempel: kopiera från MongoDB till Oracle:**

Exempel: Om du har MongoDB dokument med följande innehåll:

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

och du vill kopiera den till en Azure SQL-tabell i följande format, genom att förenkla data i matrisen *(order_pd och order_price)* och cross join med den gemensamma rotinformationen *(tal, datum och ort)* :

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Konfigurera schemamappning regeln som följande kopiera aktivitet JSON-exempel:

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
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Datatypsmappningen

Kopieringsaktiviteten utför typer av datakällor för att mottagare typer mappning med följande metod i steg 2:

1. Konvertera från interna källtyper till Azure Data Factory tillfälliga datatyper
2. Konvertera från Azure Data Factory tillfälliga datatyper till interna mottagare

Du kan hitta mappningen mellan ursprunglig typ. till tillfälliga typ i avsnittet ”datatypen mappning” i varje avsnitt om anslutningsprogram.

### <a name="supported-data-types"></a>Datatyper som stöds

Data Factory stöder följande datatyper av mellanliggande: Du kan ange värdena nedan när du konfigurerar anger du följande information i [datauppsättningsstrukturen](concepts-datasets-linked-services.md#dataset-structure-or-schema) konfiguration:

* Byte[]
* Boolean
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Tidsintervall

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna i Kopieringsaktiviteten:

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
