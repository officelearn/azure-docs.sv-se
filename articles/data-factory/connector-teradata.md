---
title: Kopiera data från Teradata med Azure Data Factory | Microsoft Docs
description: Med Teradata-anslutaren i Data Factory-tjänsten kan du kopiera data från en Teradata-databas till data lager som stöds av Data Factory som mottagare.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: ce326d7284e22a8734f6be671a277795ba659522
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720527"
---
# <a name="copy-data-from-teradata-by-using-azure-data-factory"></a>Kopiera data från Teradata med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuell version](connector-teradata.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en Teradata-databas. Den bygger på [Översikt över kopierings aktiviteten](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en Teradata-databas till alla mottagar data lager som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Teradata-anslutaren:

- Teradata **version 14,10, 15,0, 15,10, 16,0, 16,10 och 16,20**.
- Kopiera data med hjälp av **Basic** -eller **Windows** -autentisering.
- Parallell kopiering från en Teradata-källa. Mer information finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) .

> [!NOTE]
>
> Azure Data Factory uppgraderat Teradata-anslutaren efter lanseringen av integrering runtime v-3.18 med egen värd. Alla befintliga arbets belastningar som använder den tidigare Teradata-anslutningen stöds fortfarande. För nya arbets belastningar är det dock en bra idé att använda den nya. Observera att den nya sökvägen kräver en annan uppsättning länkad tjänst, data uppsättning och kopierings källa. Konfigurations information finns i respektive avsnitt som följer.

## <a name="prerequisites"></a>Förutsättningar

Om din Teradata inte är offentligt tillgänglig måste du konfigurera en [integration runtime med egen värd](create-self-hosted-integration-runtime.md). Integrerings körningen innehåller en inbyggd Teradata-drivrutin från och med version 3,18. Du behöver inte installera någon driv rutin manuellt. Driv rutinen kräver " C++ Visual Redistributable 2012 uppdatering 4" på den lokala datorn för integration Runtime. Om du inte redan har installerat det kan du ladda ned det [här](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

För en lokal version av integration runtime som är äldre än 3,18 installerar du [.net-dataprovidern för Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), version 14 eller senare på integration runtime-datorn. 

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Teradata-anslutaren.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Den länkade tjänsten Teradata stöder följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till **Teradata**. | Ja |
| connectionString | Anger den information som krävs för att ansluta till Teradata-databasens instans. Se följande exempel.<br/>Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) . | Ja |
| username | Ange ett användar namn för att ansluta till Teradata-databasen. Gäller när du använder Windows-autentisering. | Nej |
| password | Ange ett lösen ord för det användar konto som du har angett som användar namn. Du kan också välja att [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). <br>Gäller när du använder Windows-autentisering eller refererar till ett lösen ord i Key Vault för grundläggande autentisering. | Nej |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. En integration runtime med egen värd krävs, enligt vad som krävs [.](#prerequisites) |Ja |

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

## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-datauppsättningen. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md).

Följande egenskaper stöds för att kopiera data från Teradata:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type för data mängden måste anges till `TeradataTable`. | Ja |
| database | Namnet på Teradata-databasen. | Nej (om ”frågan” i aktivitetskälla har angetts) |
| table | Namnet på tabellen i Teradata-databasen. | Nej (om ”frågan” i aktivitetskälla har angetts) |

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
> `RelationalTable`typ data uppsättningen stöds fortfarande. Vi rekommenderar dock att du använder den nya data uppsättningen.

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

Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-källan. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter [](concepts-pipelines-activities.md)finns i pipelines. 

### <a name="teradata-as-a-source-type"></a>Teradata som käll typ

> [!TIP]
>
> Information om hur du kan läsa in data från Teradata effektivt genom att använda data partitionering finns i avsnittet [parallell kopia från Teradata](#parallel-copy-from-teradata) .

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från Teradata:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för kopierings aktivitets källan måste vara inställd på `TeradataSource`. | Ja |
| query | Använda anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`.<br>När du aktiverar partitionerad belastning måste du koppla alla motsvarande inbyggda partitionsalternativ i frågan. Exempel finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) . | Nej (om tabellen i data uppsättningen har angetts) |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från Teradata. <br>Tillåtna värden är: **Ingen** (standard), **hash** -och **DynamicRange**.<br>När ett partitionsalternativ är aktiverat (det vill säga inte `None`) konfigurerar du [`parallelCopies`](copy-activity-performance.md#parallel-copy) även inställningen för kopierings aktiviteten. Detta fastställer parallell graden för att samtidigt läsa in data från en Teradata-databas. Du kan till exempel ange 4. | Nej |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när partition alternativet inte `None`är. | Nej |
| partitionColumnName | Ange namnet på den käll kolumn **i Integer-typ** som ska användas av intervall partitionering för parallell kopiering. Om detta inte anges identifieras primär nyckeln för tabellen automatiskt och används som partition-kolumn. <br>Använd när alternativet partition är `Hash` eller. `DynamicRange` Om du använder en fråga för att hämta källdata, Hook `?AdfHashPartitionCondition` eller `?AdfRangePartitionColumnName` i WHERE-satsen. Se exempel i [Parallel Copy från Teradata](#parallel-copy-from-teradata) -avsnittet. | Nej |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för att kopiera data. <br>Använd när partition alternativet är `DynamicRange`. Om du använder Query för att hämta källdata, Hook `?AdfRangePartitionUpbound` i WHERE-satsen. Ett exempel finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) . | Nej |
| PartitionLowerBound | Det minimala värdet för kolumnen partition som ut data ska kopieras. <br>Använd när alternativet partition är `DynamicRange`. Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionLowbound` i WHERE-satsen. Ett exempel finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) . | Nej |

> [!NOTE]
>
> `RelationalSource`typ kopierings källan stöds fortfarande, men den har inte stöd för den nya inbyggda parallella inläsningen från Teradata (partitionsalternativ). Vi rekommenderar dock att du använder den nya data uppsättningen.

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

När du aktiverar partitionerad kopiering körs Data Factory parallella frågor mot din Teradata-källa för att läsa in data med partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar. Varje fråga hämtar en del av data från Teradata-databasen.

Det är en bra idé att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från Teradata-databasen. Följande är föreslagna konfigurationer för olika scenarier:

| Scenario                                                     | Föreslagna inställningar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell.                                   | **Partitions alternativ**: Beräkna. <br><br/>Under körningen identifierar Data Factory automatiskt kolumnen PK, använder en hash mot den och kopierar data efter partitioner. |
| Läs in stora mängder data med hjälp av en anpassad fråga.                 | **Partitions alternativ**: Beräkna.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Partitionstabell**: Ange den kolumn som används för Apply hash-partition. Om inget anges identifierar Data Factory automatiskt kolumnen PK i tabellen som du angav i Teradata-datauppsättningen.<br><br>Under körningen ersätts `?AdfHashPartitionCondition` Data Factory med hash-partitionens logik och skickas till Teradata. |
| Läs in stora mängder data med hjälp av en anpassad fråga med en heltals kolumn med jämnt distribuerat värde för intervall partitionering. | **Partitionsalternativ**: Partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med data typen Integer.<br>**Partitionens övre gränser** och **partitionens nedre gränser**: Ange om du vill filtrera mot kolumnen partition för att bara hämta data mellan det nedre och övre intervallet.<br><br>Under körningen ersätts `?AdfRangePartitionColumnName`Data Factory `?AdfRangePartitionUpbound`, och `?AdfRangePartitionLowbound` med det faktiska kolumn namnet och värde intervallet för varje partition och skickas till Teradata. <br>Om t. ex. partitionens kolumn "ID" har angetts med den nedre gränser som 1 och den övre gränser som 80, med parallell kopierings uppsättning som 4, Data Factory hämtar data med 4 partitioner. Deras ID: n är mellan [1, 20], [21, 40], [41, 60] och [61, 80]. |

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

| Data typen Teradata | Data Factory tillfälliga datatyp |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |Sträng |
| CLOB |Sträng |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Stöds ej. Använd explicit Cast i käll frågan. |
| Integer |Int32 |
| Interval Day |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Day To Hour |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Day To Minute |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Day To Second |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Hour |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Hour To Minute |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Hour To Second |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Minute |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Minute To Second |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Month |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Second |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Year |Stöds ej. Använd explicit Cast i käll frågan. |
| Interval Year To Month |Stöds ej. Använd explicit Cast i käll frågan. |
| Number |Double |
| Period (datum) |Stöds ej. Använd explicit Cast i käll frågan. |
| Period (tid) |Stöds ej. Använd explicit Cast i käll frågan. |
| Period (tid med tidszon) |Stöds ej. Använd explicit Cast i käll frågan. |
| Period (tidsstämpel) |Stöds ej. Använd explicit Cast i käll frågan. |
| Period (tidsstämpel med tidszon) |Stöds ej. Använd explicit Cast i käll frågan. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |Sträng |
| VarGraphic |Stöds ej. Använd explicit Cast i käll frågan. |
| Xml |Stöds ej. Använd explicit Cast i käll frågan. |


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
