---
title: Schemamappning i kopieringsaktivitet
description: Lär dig mer om hur kopieringsaktivitet i Azure Data Factory mappar scheman och datatyper från källdata för att sänka data när data kopieras.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260818"
---
# <a name="schema-mapping-in-copy-activity"></a>Schemamappning i kopieringsaktivitet

I den här artikeln beskrivs hur azure data factory-kopieringsaktiviteten gör schemamappning och mappning av datatyp från källdata för att sänka data när datakopian körs.

## <a name="schema-mapping"></a>Schemamappning

Kolumnmappning gäller vid kopiering av data från källa till diskho. Som standard kopierar du information **om aktivitetsmappningskällan som ska sänkas efter kolumnnamn**. Du kan ange [explicit mappning](#explicit-mapping) för att anpassa kolumnmappningen baserat på dina behov. Mer specifikt kopierar aktivitet:

1. Läsa data från källan och bestämma källschemat
2. Använd standardkolumnmappning för att mappa kolumner efter namn eller använd explicit kolumnmappning om det anges.
3. Skriv data som ska sänkas

### <a name="explicit-mapping"></a>Explicit mappning

Du kan ange vilka kolumner som ska `translator`  ->  `mappings` mappas i kopieringsaktivitet -> egenskap. I följande exempel definieras en kopieringsaktivitet i en pipeline för att kopiera data från avgränsad text till Azure SQL Database.

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

Följande egenskaper stöds `translator`  ->  `mappings` under -> objekt `source` med `sink`och:

| Egenskap | Beskrivning                                                  | Krävs |
| -------- | ------------------------------------------------------------ | -------- |
| namn     | Namn på käll- eller sinkkolumnen.                           | Ja      |
| Ordinal  | Kolumnindex. Börja med 1. <br>Använd och krävs när du använder avgränsad text utan rubrikrad. | Inga       |
| path     | JSON-banuttryck för varje fält som ska extraheras eller mappas. Ansök om hierarkiska data t ex MongoDB/REST.<br>För fält under rotobjektet börjar JSON-sökvägen med root $; För fält inuti den `collectionReference` matris som valts efter egenskap startar JSON-banan från matriselementet. | Inga       |
| typ     | Data Factory interimdatatyp för käll- eller sinkkolumnen. | Inga       |
| Kultur  | Kultur av källan eller sjunka kolonnen. <br>Använd när `Datetime` typen `Datetimeoffset`är eller . Standardvärdet är `en-us`. | Inga       |
| format   | Formatera sträng som ska `Datetime` användas `Datetimeoffset`när typen är eller . Se [anpassade datum- och tidsformatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) om hur du formaterar datetime. | Inga       |

Följande egenskaper stöds `translator`  ->  `mappings` under förutom att `source` `sink`objekt med och:

| Egenskap            | Beskrivning                                                  | Krävs |
| ------------------- | ------------------------------------------------------------ | -------- |
| insamlingReference | Stöds endast när hierarkiska data t.ex.<br>Om du vill iterera och extrahera data från objekten **i ett matrisfält** med samma mönster och konvertera till per rad per objekt anger du JSON-sökvägen för den matrisen som ska korsanvändas. | Inga       |

### <a name="alternative-column-mapping"></a>Alternativ kolumnmappning

Du kan ange kopieringsaktivitet `translator`  ->  `columnMappings` -> för att mappa mellan tabellformade data . I det här fallet krävs avsnittet "struktur" för både indata- och utdatauppsättningar. Kolumnmappning stöder **mappning av alla eller delmängd av kolumner i källdatauppsättningen "struktur" till alla kolumner i sink-datauppsättningen "struktur".** Följande är felvillkor som resulterar i ett undantag:

* Frågeresultatet för källdatalager har inget kolumnnamn som anges i avsnittet "struktur" för indatauppsättningen.
* Sink datalagret (om med fördefinierat schema) inte har ett kolumnnamn som anges i avsnittet utdatadatauppsättning "struktur".
* Antingen färre kolumner eller fler kolumner i "strukturen" för sink-datauppsättning än vad som anges i mappningen.
* Dubblettmappning.

I följande exempel har indatauppsättningen en struktur och pekar på en tabell i en lokal Oracle-databas.

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

I det här exemplet har utdatauppsättningen en struktur och pekar på en tabell i Salesfoce.

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

Följande JSON definierar en kopieringsaktivitet i en pipeline. Kolumnerna från källan mappas till kolumner i diskhon med hjälp av egenskapen **translator** -> **columnMappings.**

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

Om du använder syntaxen `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` för att ange kolumnmappning stöds den fortfarande som den är.

### <a name="alternative-schema-mapping"></a>Alternativ schemamappning

Du kan ange kopieringsaktivitet `translator`  ->  `schemaMapping` -> för att mappa mellan hierarkiska data och tabellformade data, t.ex. Följande egenskaper stöds i `translator` avsnittet kopiera aktivitet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen typ för översättaren för kopieringsaktivitet måste ställas in på: **TabularTranslator** | Ja |
| schemaMappning | En samling nyckel-värde-par, som representerar mappningsrelationen **från källsida till diskbänkssida**.<br/>- **Nyckel:** representerar källa. För **tabellkälla**anger du kolumnnamnet som definierats i datauppsättningsstrukturen. För **hierarkisk källa**anger du JSON-banuttrycket för varje fält som ska extraheras och mappas.<br>- **Värde:** representerar diskbänk. För **tabellmottagare**anger du kolumnnamnet som definierats i datauppsättningsstrukturen. För **hierarkisk diskho**anger du JSON-banuttrycket för varje fält som ska extraheras och mappas. <br>När det gäller hierarkiska data börjar JSON-sökvägen med root $. För fält inuti den `collectionReference` matris som valts efter egenskap startar JSON-banan från matriselementet.  | Ja |
| insamlingReference | Om du vill iterera och extrahera data från objekten **i ett matrisfält** med samma mönster och konvertera till per rad per objekt anger du JSON-sökvägen för den matrisen som ska korsanvändas. Den här egenskapen stöds endast när hierarkiska data är källa. | Inga |

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

och du vill kopiera den till en Azure SQL-tabell i följande format, genom att förenkla data i matrisen *(order_pd och order_price)* och korskoppling med den gemensamma rotinformationen *(tal, datum och ort):*

| orderNummer | Orderdatum | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Konfigurera schemamappningsregeln som följande JSON-exempel för kopieringsaktivitet:

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

## <a name="data-type-mapping"></a>Datatypmappning

Kopieringsaktivitet utför källtyper för att sink-typer mappa med följande tvåstegsmetod:

1. Konvertera från inbyggda källtyper till interimsdatatyper i Azure Data Factory
2. Konvertera från Azure Data Factory mellanliggande datatyper till inbyggd sink-typ

Du kan hitta mappningen mellan den inbyggda typen till interimstyp i avsnittet "Mappning av datatyp" i varje kopplingsavsnitt.

### <a name="supported-data-types"></a>Datatyper som stöds

Data Factory stöder följande interimsdatatyper: Du kan ange nedanstående värden när du konfigurerar typinformation i [strukturkonfigurationen för datauppsättning:](concepts-datasets-linked-services.md#dataset-structure-or-schema)

* Byte[]
* Boolean
* Datumtid
* Datumtidsdatum
* Decimal
* Double
* GUID
* Int16 (int16)
* Int32
* Int64
* Enkel
* String
* Tidsintervall

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna för kopieringsaktivitet:

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
