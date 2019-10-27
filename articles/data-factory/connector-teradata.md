---
title: Kopiera data från Teradata-Vantage med hjälp av Azure Data Factory | Microsoft Docs
description: Med Teradata-anslutaren i Data Factory-tjänsten kan du kopiera data från en Teradata-Vantage till data lager som stöds av Data Factory som mottagare.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 923e248ff7b793d5187faeabdbf073ca90d9efc2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72930971"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Kopiera data från Teradata-Vantage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuell version](connector-teradata.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Teradata-Vantage. Den bygger på [Översikt över kopierings aktiviteten](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Teradata-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Teradata-Vantage till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här Teradata-anslutaren:

- Teradata **version 14,10, 15,0, 15,10, 16,0, 16,10 och 16,20**.
- Kopiera data med hjälp av **Basic** -eller **Windows** -autentisering.
- Parallell kopiering från en Teradata-källa. Mer information finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) .

> [!NOTE]
>
> Azure Data Factory uppgraderat Teradata-anslutaren efter lanseringen av integrering runtime v-3.18 med egen värd. Alla befintliga arbets belastningar som använder den tidigare Teradata-anslutningen stöds fortfarande. För nya arbets belastningar är det dock en bra idé att använda den nya. Observera att den nya sökvägen kräver en annan uppsättning länkad tjänst, data uppsättning och kopierings källa. Konfigurations information finns i respektive avsnitt som följer.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integrerings körningen innehåller en inbyggd Teradata-drivrutin från och med version 3,18. Du behöver inte installera någon driv rutin manuellt. Driv rutinen kräver " C++ Visual Redistributable 2012 uppdatering 4" på den lokala datorn för integration Runtime. Om du inte redan har installerat det kan du ladda ned det [här](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

För en lokal version av integration runtime som är äldre än 3,18 installerar du [.net-dataprovidern för Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), version 14 eller senare på integration runtime-datorn. 

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Teradata-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Den länkade tjänsten Teradata stöder följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **Teradata**. | Ja |
| Begär | Anger den information som krävs för att ansluta till Teradata-instansen. Se följande exempel.<br/>Du kan också ange ett lösen ord i Azure Key Vault och hämta `password`-konfigurationen från anslutnings strängen. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) . | Ja |
| användarnamn | Ange ett användar namn för att ansluta till Teradata. Gäller när du använder Windows-autentisering. | Nej |
| lösenord | Ange ett lösen ord för det användar konto som du har angett som användar namn. Du kan också välja att [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). <br>Gäller när du använder Windows-autentisering eller refererar till ett lösen ord i Key Vault för grundläggande autentisering. | Nej |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Ja |

Fler anslutnings egenskaper som du kan ange i anslutnings strängen per ärende:

| Egenskap | Beskrivning | Standardvärde |
|:--- |:--- |:--- |
| CharacterSet | Den teckenuppsättning som ska användas för sessionen. T. ex. `CharacterSet=UTF16`.<br><br/>Det här värdet kan vara en användardefinierad teckenuppsättning eller någon av följande fördefinierade teckenuppsättningar: <br/>– ASCII<br/>– UTF8<br/>– UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>-Shift-JIS (Windows, DOS-kompatibel, KANJISJIS_0S)<br/>– EUC (UNIX-kompatibel, KANJIEC_0U)<br/>– IBM-stordator (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>– GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Standardvärdet är `ASCII`. |
| MaxRespSize |Maximal storlek för svars buffert för SQL-begäranden i kilobyte (KB). T. ex. `MaxRespSize=‭10485760‬`.<br/><br/>För Teradata-databas version 16,00 eller senare är det maximala värdet 7361536. För anslutningar som använder tidigare versioner är det maximala värdet 1048576. | Standardvärdet är `65536`. |

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

**Exempel som använder Windows-autentisering**

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
> Följande nytto Last stöds fortfarande. Du bör dock använda den nya.

**Föregående nytto last:**

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

## <a name="dataset-properties"></a>Egenskaper för data mängd

Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-datauppsättningen. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md).

Följande egenskaper stöds för att kopiera data från Teradata:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till `TeradataTable`. | Ja |
| databas | Namnet på Teradata-instansen. | Nej (om "fråga" i aktivitets källan har angetts) |
| Partitionstabell | Namnet på tabellen i Teradata-instansen. | Nej (om "fråga" i aktivitets källan har angetts) |

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
> `RelationalTable` typ data uppsättning stöds fortfarande. Vi rekommenderar dock att du använder den nya data uppsättningen.

**Föregående nytto last:**

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

Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-källan. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata som källa

>[!TIP]
>Om du vill läsa in data från Teradata effektivt genom att använda data partitionering kan du läsa mer från [Parallel Copy från Teradata](#parallel-copy-from-teradata) -avsnittet.

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från Teradata:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till `TeradataSource`. | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`.<br>När du aktiverar partitionerad belastning måste du koppla alla motsvarande inbyggda partitionsalternativ i frågan. Exempel finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) . | Nej (om tabellen i data uppsättningen har angetts) |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från Teradata. <br>Tillåt värden är: **ingen** (standard), **hash** -och **DynamicRange**.<br>När ett partitions alternativ är aktiverat (det vill säga inte `None`), kontrol leras graden av parallellitet för att data ska läsas in från Teradata av [`parallelCopies`](copy-activity-performance.md#parallel-copy) inställningen på kopierings aktiviteten. | Nej |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när alternativet partition inte `None`. | Nej |
| partitionColumnName | Ange namnet på den käll kolumn som ska användas av intervall partition eller hash-partition för parallell kopiering. Om inget anges identifieras primärt index för tabellen automatiskt och används som partition-kolumn. <br>Använd när alternativet partition är `Hash` eller `DynamicRange`. Om du använder en fråga för att hämta källdata, Hook-`?AdfHashPartitionCondition` eller `?AdfRangePartitionColumnName` i WHERE-satsen. Se exempel i [Parallel Copy från Teradata](#parallel-copy-from-teradata) -avsnittet. | Nej |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för att kopiera data. <br>Använd när alternativet partition är `DynamicRange`. Om du använder Query för att hämta källdata, Hook-`?AdfRangePartitionUpbound` i WHERE-satsen. Ett exempel finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) . | Nej |
| partitionLowerBound | Det minimala värdet för kolumnen partition som ut data ska kopieras. <br>Använd när alternativet partition är `DynamicRange`. Om du använder en fråga för att hämta källdata, Hook-`?AdfRangePartitionLowbound` i WHERE-satsen. Ett exempel finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) . | Nej |

> [!NOTE]
>
> `RelationalSource` typ kopierings källa stöds fortfarande, men den har inte stöd för den nya inbyggda parallella inläsningen från Teradata (partitionsalternativ). Vi rekommenderar dock att du använder den nya data uppsättningen.

**Exempel: kopiera data med en grundläggande fråga utan partition**

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

## <a name="parallel-copy-from-teradata"></a>Parallell kopiering från Teradata

Data Factory Teradata-anslutaren tillhandahåller inbyggd data partitionering för att kopiera data från Teradata parallellt. Du kan hitta data partitionerings alternativ i **käll** tabellen för kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-teradata/connector-teradata-partition-options.png)

När du aktiverar partitionerad kopiering körs Data Factory parallella frågor mot din Teradata-källa för att läsa in data med partitioner. Den parallella graden styrs av [`parallelCopies`s](copy-activity-performance.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel ställer in `parallelCopies` till fyra, Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från din Teradata.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från din Teradata. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager, skrivs de om för att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Föreslagna inställningar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell.                                   | **Partitionerings alternativ**: hash. <br><br/>Under körningen identifierar Data Factory automatiskt kolumnen PK, använder en hash mot den och kopierar data efter partitioner. |
| Läs in stora mängder data med hjälp av en anpassad fråga.                 | **Partitionerings alternativ**: hash.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Partitionstabell**: Ange den kolumn som används för Apply hash-partition. Om inget anges identifierar Data Factory automatiskt kolumnen PK i tabellen som du angav i Teradata-datauppsättningen.<br><br>Under körningen ersätter Data Factory `?AdfHashPartitionCondition` med hash-partitionens logik och skickar till Teradata. |
| Läs in stora mängder data med hjälp av en anpassad fråga med en heltals kolumn med jämnt distribuerat värde för intervall partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med data typen Integer.<br>**Partitionens övre gränser** och **partition nedre gränser**: Ange om du vill filtrera mot kolumnen partition för att endast hämta data mellan det nedre och övre intervallet.<br><br>Under körningen ersätter Data Factory `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`och `?AdfRangePartitionLowbound` med det faktiska kolumn namnet och värde intervallet för varje partition och skickar till Teradata. <br>Om t. ex. partitionens kolumn "ID" har angetts med den nedre gränser som 1 och den övre gränser som 80, med parallell kopierings uppsättning som 4, Data Factory hämtar data med 4 partitioner. Deras ID: n är mellan [1, 20], [21, 40], [41, 60] och [61, 80]. |

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

**Exempel: fråga med Dynamic Range-partition**

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

## <a name="data-type-mapping-for-teradata"></a>Data typs mappning för Teradata

När du kopierar data från Teradata gäller följande mappningar. Information om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren finns i [schema-och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| Data typen Teradata | Data Factory data typen Interim |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte [] |
| Stor |Byte [] |
| ByteInt |Int16 |
| hängande |Sträng |
| CLOB |Sträng |
| Datum |DateTime |
| Decimal |Decimal |
| Dubbelklicka |Dubbelklicka |
| Infoga |Stöds ej. Använd explicit Cast i käll frågan. |
| Integer |Int32 |
| Intervall dag |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall, dag till timme |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall, dag till minut |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall, dag till sekund |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall timme |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall timme till minut |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall timme till sekund |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall minut |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall i minuter till sekund |Stöds ej. Använd explicit Cast i käll frågan. |
| Månads intervall |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall sekund |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall år |Stöds ej. Använd explicit Cast i käll frågan. |
| Intervall år till månad |Stöds ej. Använd explicit Cast i käll frågan. |
| Tal |Dubbelklicka |
| Period (datum) |Stöds ej. Använd explicit Cast i käll frågan. |
| Period (tid) |Stöds ej. Använd explicit Cast i käll frågan. |
| Period (tid med tidszon) |Stöds ej. Använd explicit Cast i käll frågan. |
| Period (tidsstämpel) |Stöds ej. Använd explicit Cast i käll frågan. |
| Period (tidsstämpel med tidszon) |Stöds ej. Använd explicit Cast i käll frågan. |
| SmallInt |Int16 |
| Tid |Intervall |
| Tid med tidszon |Intervall |
| Tidsstämpel |DateTime |
| Tidsstämpel med tidszon |DateTime |
| VarByte |Byte [] |
| VarChar |Sträng |
| VarGraphic |Stöds ej. Använd explicit Cast i käll frågan. |
| fil |Stöds ej. Använd explicit Cast i käll frågan. |


## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
