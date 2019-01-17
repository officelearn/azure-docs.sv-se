---
title: Schemamappning i kopieringsaktiviteten | Microsoft Docs
description: Läs mer om hur Kopieringsaktivitet i Azure Data Factory mappar scheman och datatyper från datakällan för att registrera data när du kopierar data.
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
ms.openlocfilehash: c2f58a3510699cdf74e3150d3ad5882929f4f05b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358719"
---
# <a name="schema-mapping-in-copy-activity"></a>Schemamappning i kopieringsaktiviteten
Den här artikeln beskriver hur Azure Data Factory Kopieringsaktivitet utför schemamappning och datatypmappningen från källdata till mottagare data när kör Datakopieringen.

## <a name="column-mapping"></a>Kolumnmappning

Kolumnmappningen gäller när du kopierar data mellan tabular-formade data. Kopiera aktivitet som standard **mappa källdata till mottagare av kolumnnamn**, såvida inte [explicit kolumnmappning](#explicit-column-mapping) har konfigurerats. Mer specifikt Kopieringsaktivitet:

1. Läsa data från källan och avgöra källans schema

    * För datakällor med fördefinierat schema i data store /-format, till exempel databasfiler med metadata (Avro/ORC/Parquet/Text rubrik), extraheras källans schema från frågan resultatet eller filmetadata.
    * Datakällans schema härleds från frågeresultat för datakällor med flexibelt schema, till exempel Azure tabell/Cosmos DB. Du kan skriva över den genom att konfigurera ”strukturen” i datauppsättningen.
    * Textfilen utan rubrik skapas standardkolumnvärdena med mönstret ”Prop_0”, ”Prop_1”... Du kan skriva över den genom att konfigurera ”strukturen” i datauppsättningen.
    * Du måste ange schemainformationen i avsnittet ”struktur” datauppsättning för Dynamics-källa.

2. Använd explicit kolumnmappning om anges.

3. Skriva data till mottagare

    * För datalager med fördefinierat schema skrivs informationen till kolumner med samma namn.
    * Datalager utan fasta schemat och filformat genereras namn/kolumnmetadata baserat på datakällans schema.

### <a name="explicit-column-mapping"></a>Explicit kolumnmappning

Du kan ange **columnMappings** i den **typeProperties** delen av kopieringsaktiviteten inte explicit kolumnmappning. I det här scenariot måste ”struktur”-avsnittet anges för både in- och utdatauppsättningar. Stöd för mappning av kolumnen **mappning av alla eller en delmängd med kolumner i datauppsättningen för källan ”struktur” på alla kolumner i datauppsättning för mottagare ”struktur”**. Här följer felvillkor som resulterar i ett undantag:

* Källans datalager fråga resultatet inte har ett kolumnnamn som anges i avsnittet ”struktur” datauppsättningen för indata.
* Datalager för mottagare (med fördefinierat schema) har inte ett kolumnnamn som anges i avsnittet ”struktur” utdata-datauppsättning.
* Färre kolumner eller fler kolumner i ”strukturen” för datauppsättning för mottagare än anges i mappningen.
* Duplicera mappning.

#### <a name="explicit-column-mapping-example"></a>Explicit kolumnmappning exempel

I det här exemplet indatatabellen har en struktur och den pekar på en tabell i en lokal SQL-databas.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

I det här exemplet utdatatabellen har en struktur och den pekar på en tabell i en Azure SQL Database.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Följande JSON definierar en Kopieringsaktivitet i en pipeline. Kolumner från källan som är mappade till kolumnerna i mottagare (**columnMappings**) med hjälp av den **translator** egenskapen.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
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

**Kolumnmappningen flöde:**

![Kolumnen mappning flöde](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>Schemamappning

Schemamappning gäller när du kopierar data mellan hierarkisk formade data och tabular-formade data, t.ex. kopiera från MongoDB/REST till textfil och kopiera från SQL till Azure Cosmos DB-API för MongoDB. Följande egenskaper stöds i kopieringsaktiviteten `translator` avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för kopiera aktivitet translator måste anges till: **TabularTranslator** | Ja |
| schemaMapping | En samling nyckel / värde-par som representerar mappning relationen från tabular sida till hierarkiska sida.<br/>- **Nyckel:** kolumnnamnet för tabelldata som definierats i datauppsättningsstrukturen.<br/>- **Värde:** JSON-sökvägsuttrycket för varje fält för att extrahera och mappa. För fält under rotobjektet börjar du med $; för fält inuti matrisen som väljs av egenskapen `collectionReference` börjar du från matriselementet.  | Ja |
| collectionReference | Om du vill iterera och extrahera data från objekten **i ett matrisfält** med samma mönster och konvertera till ange JSON-sökvägen för matrisen för cross-gäller per rad per objekt. Den här egenskapen stöds endast när hierarkiska data är källan. | Nej |

**Exempel: kopiera från MongoDB till SQL:**

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
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
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

Data Factory stöder följande datatyper av mellanliggande: Du kan ange värdena nedan när du konfigurerar anger du följande information i [datauppsättningsstrukturen](concepts-datasets-linked-services.md#dataset-structure) konfiguration:

* Byte]
* Boolesk
* DateTime
* DateTimeOffset
* Decimal
* Double-värde
* GUID
* Int16
* Int32
* Int64
* Enkel
* Sträng
* Tidsintervall

### <a name="explicit-data-type-conversion"></a>Explicit konvertering av datatyper

När kopierar data till data som lagras med fast schema, till exempel SQL Server/Oracle, när källa och mottagare har en annan typ för samma kolumn anges explicit typkonvertering i käll-sida:

* För källa, till exempel CSV/Avro typ konverze anges via källstrukturen med fullständig kolumnlista (källa på klientsidan namn och en mottagare sida kolumntyp)
* För relationella källor (till exempel SQL/Oracle), bör typkonvertering uppnås genom explicit typ omvandling i frågeuttrycket.

## <a name="when-to-specify-dataset-structure"></a>När du ska ange ”datauppsättningsstrukturen”

I nedan scenarier krävs ”struktur” i datauppsättningen:

* Tillämpa [explicit konvertering av datatyper](#explicit-data-type-conversion) för filen källor vid kopiering (datauppsättningen för indata)
* Tillämpa [explicit kolumnmappning](#explicit-column-mapping) vid kopiering (både indata och utdata datauppsättning)
* Kopiera från Dynamics 365 / CRM källa (datauppsättningen för indata)
* Kopiera till Cosmos DB som kapslade objekt när källan inte är JSON-filer (datauppsättningen för utdata)

I nedan scenarier föreslås ”struktur” i datauppsättningen:

* Kopiera från textfil utan rubrik (datauppsättningen för indata). Du kan ange kolumnnamnen textfilen som överensstämmer med motsvarande mottagare kolumn, spara konfigurerar explicit kolumnmappning.
* Kopiera från data lagrar med flexibelt schema, till exempel Azure tabell/Cosmos DB (datauppsättningen för indata), för att garantera förväntade data (kolumner) kopieras över i stället för att låta kopiera aktivitet Skapa schema baserat på översta raderna under varje aktivitet som körs.


## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna i Kopieringsaktiviteten:

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Kopiera aktivitet feltolerans](copy-activity-fault-tolerance.md)
- [Kopiera aktivitet prestanda](copy-activity-performance.md)
