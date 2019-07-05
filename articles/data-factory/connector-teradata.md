---
title: Kopiera data från Teradata med Azure Data Factory | Microsoft Docs
description: Läs mer om Teradata koppling av Data Factory-tjänsten som låter dig kopieringsdata från Teradata-databas till datalager som stöds av Data Factory som mottagare.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561437"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Kopiera data från Teradata med Azure Data Factory
> [!div class="op_single_selector" title1="Välj versionen av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuell version](connector-teradata.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en Teradata-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Teradata-databas till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Teradata-anslutningen:

- Teradata **version 14.10, 15.0, 15.10, 16,0, 16,10 och 16.20**.
- Kopiera data med hjälp av **grundläggande** eller **Windows** autentisering.
- Parallell kopia från Teradata-källa. Se [parallell kopia från Teradata](#parallel-copy-from-teradata) avsnitt med information.

> [!NOTE]
>
> Azure Data Factory uppgraderas Teradata-anslutningen eftersom lokal Integration Runtime v3.18 som är drivande av en inbyggd ODBC-drivrutinen och erbjuder alternativ för flexibel anslutning samt out-of-box parallella kopia om du vill öka prestandan. Alla befintliga arbetsbelastningar med föregående Teradata-kopplingen möjligheter med .NET Data Provider för Teradata stöds fortfarande som – är att medan du rekommenderas för att använda den nya framöver. Observera att den nya sökvägen kräver en annan uppsättning länkad tjänst/datauppsättningskopia källa. Se avsnitten på konfigurationsinformation.

## <a name="prerequisites"></a>Förutsättningar

Om din Teradata inte är offentligt tillgänglig, måste du ställer in en lokal Integration Runtime. Mer information om integration runtime finns i [lokal Integration Runtime](create-self-hosted-integration-runtime.md). Integreringskörningen innehåller en inbyggd Teradata-drivrutin från och med version 3,18, måste du därför inte att manuellt installera en drivrutin. Drivrutinen kräver ”Visual C++ Redistributable 2012 Update 4” på den lokala IR-datorn, ladda ned från [här](https://www.microsoft.com/en-sg/download/details.aspx?id=30679) om du ännu inte har installerat.

För lokal IR-versionen är lägre än 3,18, måste du installera den [.NET Data Provider för Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) version 14 eller senare på den Integration Runtime-datorn. 

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Teradata-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Teradata länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till: **Teradata** | Ja |
| connectionString | Anger information som behövs för att ansluta till Teradata-databasinstans. Se följande exempel.<br/>Du kan också publicera lösenord i Azure Key Vault och använda pull i `password` konfiguration av anslutningssträngen. Referera till [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| username | Ange användarnamn för att ansluta till Teradata-databasen. Gäller när du använder Windows-autentisering. | Nej |
| password | Ange lösenord för det användarkonto som du angav för användarnamnet. Du kan också välja att [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). <br>Gäller när du använder Windows-autentisering eller refererar till lösenordet i Key Vault för grundläggande autentisering. | Nej |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En lokal Integration Runtime krävs enligt [krav](#prerequisites). |Ja |

**Exempel: använder grundläggande autentisering**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: med hjälp av Windows-autentisering**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Om du använder Teradata länkade tjänsten möjligheter med .NET Data Provider för Teradata med följande nyttolasten, stöds det fortfarande som – är att medan du rekommenderas för att använda den nya framöver.

**Föregående nyttolast:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-datauppsättningen.

För att kopiera data från Teradata stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till: **TeradataTable** | Ja |
| database | Namnet på Teradata-databas. | Nej (om ”frågan” i aktivitetskälla har angetts) |
| table | Namnet på tabellen i Teradata-databasen. | Nej (om ”frågan” i aktivitetskälla har angetts) |

**Exempel:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> Om du använde ”RelationalTable” typ av datauppsättning som följande, stöds det fortfarande som – är att medan du rekommenderas för att använda den nya framöver.

**Föregående nyttolast:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-källa.

### <a name="teradata-as-source"></a>Teradata som källkod

> [!TIP]
>
> Läs mer i [parallell kopia från Teradata](#parallel-copy-from-teradata) avsnittet om hur du läser in data från Teradata effektivt med Datapartitionering.

För att kopiera data från Teradata stöds följande egenskaper i kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **TeradataSource** | Ja |
| query | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`.<br>När du aktiverar partitionerade belastningen måste schemafrågor motsvarande inbyggda partition parametrar i frågan. Se exemplen i [parallell kopia från Teradata](#parallel-copy-from-teradata) avsnittet. | Nej (om tabellen i datauppsättningen har angetts) |
| partitionOptions | Anger alternativ som används för att läsa in data från Teradata för Datapartitionering. <br>Tillåt värden är: **Ingen** (standard), **Hash** och **DynamicRange**.<br>När partitionen är aktiverat (inte ”ingen”), även konfigurera **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** inställningen på Kopieringsaktivitet till exempel som 4, som bestämmer vilken parallella samtidigt läsa in data från Teradata -databasen. | Nej |
| partitionSettings | Ange gruppen med inställningar för Datapartitionering av. <br>Tillämpa när partitionen inte är `None`. | Nej |
| partitionColumnName | Ange namnet på källkolumnen **i heltalstyp** som ska användas av intervallet partitionering för parallell kopia. Om inte anges kommer primärnyckel i tabellen att automatisk upptäckt och används som partitionskolumnen. <br>Tillämpa när partitionen är `Hash` eller `DynamicRange`. Om du använder frågan för att hämta källdata, koppla `?AdfHashPartitionCondition` eller `?AdfRangePartitionColumnName` i WHERE-satsen. Se exemplet i [parallell kopia från Teradata](#parallel-copy-from-teradata) avsnittet. | Nej |
| partitionUpperBound | Högsta värdet för partitionskolumnen att kopiera data. <br>Tillämpa när partitionen är `DynamicRange`. Om du använder frågan för att hämta källdata, koppla `?AdfRangePartitionUpbound` i WHERE-satsen. Se exemplet i [parallell kopia från Teradata](#parallel-copy-from-teradata) avsnittet. | Nej |
| partitionLowerBound | Minimivärdet för partitionskolumnen att kopiera data. <br>Tillämpa när partitionen är `DynamicRange`. Om du använder frågan för att hämta källdata, koppla `?AdfRangePartitionLowbound` i WHERE-satsen. Se exemplet i [parallell kopia från Teradata](#parallel-copy-from-teradata) avsnittet. | Nej |

> [!NOTE]
>
> Om du använde kopieringskälla för ”RelationalSource” typ, stöds det fortfarande som – är, men har inte stöd för den nya inbyggda parallellinläsa från Teradata (partitionsalternativ). Du rekommenderas för att använda den här nya framöver.

**Exempel: kopiera data med hjälp av grundläggande frågor utan partition**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Parallell kopia från Teradata

Data factory Teradata-anslutningen tillhandahåller inbyggd för partitionering för att kopiera data från Teradata parallellt med fantastisk prestanda. Du hittar alternativ för partitionering på Kopieringsaktivitet -> Teradata källa:

![Partitionsalternativ](./media/connector-teradata/connector-teradata-partition-options.png)

När du aktiverar partitionerade kopia körs data factory parallella frågor mot dina Teradata-källa för att läsa in data av partition. Parallell graden har konfigurerats och kontrollerad via **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** på Kopieringsaktivitet. Exempel: Om du ställer in `parallelCopies` som fyra, data factory genererar samtidigt och körs fyra frågor baserat på angivna partitionen alternativ och inställningar, varje hämtades del av data från Teradata-databas.

Du rekommenderas för att aktivera parallell kopia med Datapartitionering, särskilt när du läser in stora mängder data från Teradata-databas. Följande är de föreslagna konfigurationerna för olika scenarier:

| Scenario                                                     | Föreslagna inställningar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig inläsning från stora tabell                                   | **Partitionera alternativet**: -Hash. <br><br/>Under körningen data Factory automatiskt identifiera primärnyckelkolumn, tillämpa hash mot dem och kopiera data med partitioner. |
| Läsa in stora mängder data med hjälp av anpassad fråga                 | **Partitionera alternativet**: -Hash.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Partitionskolumnen**: Ange vilken kolumn som används för Använd hash-partitionen. Om inte anges identifierar automatiskt ADF PK-kolumnen i tabellen som du angav i Teradata-datauppsättningen.<br><br>Under körningen data factory Ersätt `?AdfHashPartitionCondition` med hash-partition logik och skicka till Teradata. |
| Läsa in stora mängder data med hjälp av anpassade fråga, med en heltalskolumn med jämnt fördelade värdet för intervallet partitionering | **Partitionsalternativ**: Partition dynamiska omfång.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partitionskolumnen**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumn med datatypen integer.<br>**Partition övre gränsen** och **partition nedre gränsen**: Ange om du vill filtrera mot partitionskolumnen att endast hämta data mellan lägre och övre intervallet.<br><br>Under körningen data factory Ersätt `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, och `?AdfRangePartitionLowbound` med faktiska kolumnnamn och värdet intervall för varje partition och skicka till Teradata. <br>Till exempel om ditt partition kolumnen ”ID” som angetts med nedre gränsen som 1 och övre gränsen som 80, med parallella uppsättningen som 4, ADF hämtar data med 4 partitioner med ID mellan [1,20], [21, 40], [41, 60] och [61, 80]. |

**Exempel: fråga med hash-partition**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Exempel: fråga med partition dynamiska omfång**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Datatypen mappning för Teradata

När du kopierar data från Teradata, används följande mappningar från Teradata-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Datatypen för Teradata | Data factory tillfälliga datatyp |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Integer |Int32 |
| Interval Day |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Day To Hour |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Day To Minute |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Day To Second |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Hour |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Hour To Minute |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Hour To Second |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Minute |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Minute To Second |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Month |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Second |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Year |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Interval Year To Month |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Tal |Double |
| Period (datum) |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Tid (Time) |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Period (tid med tidszon) |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Period (tidsstämpel) |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Period (tidsstämpel med tidszon) |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Stöds ej. Använd explicit typkonvertering i källfrågan. |
| Xml |Stöds ej. Använd explicit typkonvertering i källfrågan. |


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
