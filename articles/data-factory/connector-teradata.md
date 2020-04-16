---
title: Kopiera data från Teradata Vantage med hjälp av Azure Data Factory
description: Med tjänsten Data Factorys Teradata Connector kan du kopiera data från en Teradata Vantage till datalager som stöds av Data Factory som sänkor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: 4eed79210e3e39f82b892ac0681e161ebb59597e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418039"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Kopiera data från Teradata Vantage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuell version](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från Teradata Vantage. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Teradata-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Teradata Vantage till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här Teradata-anslutningen stöder:

- Teradata **version 14.10, 15.0, 15.10, 16.0, 16.10 och 16.20**.
- Kopiera data med hjälp av **Grundläggande** autentisering eller Windows-autentisering. **Windows**
- Parallellkopiering från en Teradata-källa. Mer information finns i avsnittet [Parallellkopia från Teradata.](#parallel-copy-from-teradata)

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Om du använder Självvärdförd integrationskörning, observera att den innehåller en inbyggd Teradata-drivrutin från version 3.18. Du behöver inte installera någon drivrutin manuellt. Drivrutinen kräver "Visual C++ Redistributable 2012 Update 4" på den självvärderade integrationskörningsdatorn. Om du ännu inte har det installerat, ladda ner den [härifrån](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Teradata-kopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Den Teradata-länkade tjänsten stöder följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **Teradata**. | Ja |
| Connectionstring | Anger den information som behövs för att ansluta till Teradata-instansen. Se följande exempel.<br/>Du kan också placera ett lösenord i `password` Azure Key Vault och hämta konfigurationen ur anslutningssträngen. Se [Store-autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) med mer information. | Ja |
| användarnamn | Ange ett användarnamn som ska anslutas till Teradata. Gäller när du använder Windows-autentisering. | Inga |
| password | Ange ett lösenord för det användarkonto som du angav för användarnamnet. Du kan också välja att referera till [en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). <br>Gäller när du använder Windows-autentisering eller refererar till ett lösenord i Key Vault för grundläggande autentisering. | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

Fler anslutningsegenskaper som du kan ange i anslutningssträngen per ärende:

| Egenskap | Beskrivning | Standardvärde |
|:--- |:--- |:--- |
| Teckenuppsättning | Det teckenuppsättning som ska användas för sessionen. T.ex. `CharacterSet=UTF16`<br><br/>Det här värdet kan vara en användardefinierad teckenuppsättning eller någon av följande fördefinierade teckenuppsättningar: <br/>- ASCII.<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS-kompatibel, KANJISJIS_0S)<br/>- EUC (Unix-kompatibel, KANJIEC_0U)<br/>- IBM Stordator (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NätverkKoreanska (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Standardvärdet `ASCII`är . |
| MaxRespSize |Den maximala storleken på svarsbufferten för SQL-begäranden, i kbs (kilobyte). T.ex. `MaxRespSize=‭10485760‬`<br/><br/>För Teradata Database version 16.00 eller senare är det maximala värdet 7361536. För anslutningar som använder tidigare versioner är det maximala värdet 1048576. | Standardvärdet `65536`är . |

**Exempel med grundläggande autentisering**

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

**Exempel på windowsautentisering**

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
> Följande nyttolast stöds fortfarande. Framöver bör du dock använda den nya.

**Tidigare nyttolast:**

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

Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-datauppsättningen. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar](concepts-datasets-linked-services.md).

Så här kopierar du data från Teradata och följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste `TeradataTable`anges till . | Ja |
| databas | Namnet på Teradata-instansen. | Nej (om "fråga" i aktivitetskällan har angetts) |
| tabell | Namnet på tabellen i Teradata-instansen. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`typdatauppsättning stöds fortfarande. Vi rekommenderar dock att du använder den nya datauppsättningen.

**Tidigare nyttolast:**

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

Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-källan. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata som källa

>[!TIP]
>Om du vill läsa in data från Teradata effektivt med hjälp av datapartitionering kan du läsa mer från [avsnittet Parallellkopia från Teradata.](#parallel-copy-from-teradata)

Om du vill kopiera data från Teradata stöds följande egenskaper i avsnittet kopiera **aktivitetskälla:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type property för kopians `TeradataSource`aktivitet måste anges till . | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`.<br>När du aktiverar partitionerad belastning måste du koppla motsvarande inbyggda partitionsparametrar i frågan. Exempel på exempel finns i avsnittet [Parallellkopia från Teradata.](#parallel-copy-from-teradata) | Nej (om tabellen i datauppsättningen har angetts) |
| partitionOptions | Anger de datapartitionsalternativ som används för att läsa in data från Teradata. <br>Tillåt värden är: **Ingen** (standard), **Hash** och **DynamicRange**.<br>När ett partitionsalternativ är aktiverat (det vill ha), `None`styrs graden av parallellitet till samtidig inläsning av data från Teradata av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopieringsaktiviteten. | Inga |
| partitionSätta | Ange gruppen för inställningarna för datapartitionering. <br>Använd när partitionsalternativet `None`inte är . | Inga |
| partitionColumnName | Ange namnet på källkolumnen som ska användas av områdespartitionen eller Hash-partitionen för parallellkopia. Om inget anges identifieras tabellens primära index automatiskt och används som partitionskolumn. <br>Använd när partitionsalternativet är `Hash` eller `DynamicRange`. Om du använder en fråga för `?AdfHashPartitionCondition` att `?AdfRangePartitionColumnName` hämta källdata, krok eller i WHERE-satsen. Se exempel i [Parallellkopia från avsnittet Teradata.](#parallel-copy-from-teradata) | Inga |
| partitionUpperBound | Det maximala värdet för partitionskolumnen för att kopiera data ut. <br>Använd när partitionsalternativet är `DynamicRange`. Om du använder frågan för `?AdfRangePartitionUpbound` att hämta källdata ansluter du WHERE-satsen. Ett exempel finns i avsnittet [Parallellkopia från Teradata.](#parallel-copy-from-teradata) | Inga |
| partitionLowerBound | Det minsta värdet för partitionskolumnen för att kopiera data ut. <br>Använd när partitionsalternativet är `DynamicRange`. Om du använder en fråga för `?AdfRangePartitionLowbound` att hämta källdata ansluter du WHERE-satsen. Ett exempel finns i avsnittet [Parallellkopia från Teradata.](#parallel-copy-from-teradata) | Inga |

> [!NOTE]
>
> `RelationalSource`typkopiakälla stöds fortfarande, men den stöder inte den nya inbyggda parallella belastningen från Teradata (partitionsalternativ). Vi rekommenderar dock att du använder den nya datauppsättningen.

**Exempel: kopiera data med hjälp av en grundläggande fråga utan partition**

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

Data Factory Teradata-anslutningen tillhandahåller inbyggd datapartitionering för att kopiera data från Teradata parallellt. Du hittar alternativ för datapartitionering i **källtabellen** för kopieringsaktiviteten.

![Skärmbild av partitionsalternativ](./media/connector-teradata/connector-teradata-partition-options.png)

När du aktiverar partitionerad kopia kör Data Factory parallella frågor mot teradatakällan för att läsa in data efter partitioner. Den parallella graden [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) styrs av inställningen på kopieringsaktiviteten. Om du till `parallelCopies` exempel anger fyra genererar och kör Data Factory samtidigt fyra frågor baserat på det angivna partitionsalternativet och inställningarna, och varje fråga hämtar en del data från dina Teradata.

Du föreslås aktivera parallellkopiering med datapartitionering, särskilt när du läser in stora mängder data från dina Teradata. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat datalager rekommenderas att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Full belastning från stort bord.                                   | **Partition alternativ**: Hash. <br><br/>Under körningen identifierar Data Factory automatiskt den primära indexkolumnen, tillämpar en hash mot den och kopierar data efter partitioner. |
| Läs in stora mängder data med hjälp av en anpassad fråga.                 | **Partition alternativ**: Hash.<br>**Fråga** `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`: .<br>**Partitionskolumnen**: Ange den kolumn som används för att tillämpa hash-partition. Om inget anges identifierar Data Factory automatiskt PK-kolumnen i tabellen som du angav i datauppsättningen för Teradata.<br><br>Under körningen ersätter `?AdfHashPartitionCondition` Data Factory med hash-partitionslogiken och skickar till Teradata. |
| Läs in stora mängder data med hjälp av en anpassad fråga med en heltalskolumn med jämnt fördelat värde för intervallpartitionering. | **Partitionsalternativ**: Partitionering med dynamiskt omfång.<br>**Fråga** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Partitionskolumnen**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med heltalsdatatyp.<br>**Partitionens övre gräns** och **partitionens nedre gräns:** Ange om du vill filtrera mot partitionskolumnen för att hämta data endast mellan det nedre och det övre intervallet.<br><br>Under körningen ersätter `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Data `?AdfRangePartitionLowbound` Factory , och med de faktiska kolumnnamn- och värdeintervallen för varje partition och skickas till Teradata. <br>Om partitionskolumnen "ID" har angetts med den nedre gränsen som 1 och den övre gränsen som 80, med parallellkopiering inställd som 4, hämtar Data Factory data med 4 partitioner. Deras ID är mellan [1,20], [21, 40], [41, 60] respektive [61, 80]. |

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

**Exempel: fråga med partition för dynamiskt omfång**

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

## <a name="data-type-mapping-for-teradata"></a>Mappning av datatyp för Teradata

När du kopierar data från Teradata gäller följande mappningar. Mer information om hur kopieringsaktiviteten mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

| Datatyp för Teradata | Data Factory interimdatatyp |
|:--- |:--- |
| Bigint |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt (på ett år) |Int16 (int16) |
| Char |Sträng |
| Clob |Sträng |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Grafisk |Stöds inte. Använd explicit cast i källfrågan. |
| Integer |Int32 |
| Intervalldag |Stöds inte. Använd explicit cast i källfrågan. |
| Intervall dag till timme |Stöds inte. Använd explicit cast i källfrågan. |
| Intervall dag till minut |Stöds inte. Använd explicit cast i källfrågan. |
| Intervall dag till sekund |Stöds inte. Använd explicit cast i källfrågan. |
| Intervalltimme |Stöds inte. Använd explicit cast i källfrågan. |
| Intervall timme till minut |Stöds inte. Använd explicit cast i källfrågan. |
| Intervalltimme till sekund |Stöds inte. Använd explicit cast i källfrågan. |
| Intervallminut |Stöds inte. Använd explicit cast i källfrågan. |
| Intervall minut till sekund |Stöds inte. Använd explicit cast i källfrågan. |
| Intervallmånad |Stöds inte. Använd explicit cast i källfrågan. |
| Intervall andra |Stöds inte. Använd explicit cast i källfrågan. |
| Intervallår |Stöds inte. Använd explicit cast i källfrågan. |
| Intervall år till månad |Stöds inte. Använd explicit cast i källfrågan. |
| Tal |Double |
| Period (datum) |Stöds inte. Använd explicit cast i källfrågan. |
| Period (Tid) |Stöds inte. Använd explicit cast i källfrågan. |
| Period (tid med tidszon) |Stöds inte. Använd explicit cast i källfrågan. |
| Period (tidsstämpel) |Stöds inte. Använd explicit cast i källfrågan. |
| Period (tidsstämpel med tidszon) |Stöds inte. Använd explicit cast i källfrågan. |
| Smallint |Int16 (int16) |
| Tid |TimeSpan |
| Tid med tidszon |TimeSpan |
| Tidsstämpel |DateTime |
| Tidsstämpel med tidszon |DateTime |
| VarByte (olika) |Byte[] |
| Varchar |Sträng |
| Vargrafiska |Stöds inte. Använd explicit cast i källfrågan. |
| Xml |Stöds inte. Använd explicit cast i källfrågan. |


## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns i [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
