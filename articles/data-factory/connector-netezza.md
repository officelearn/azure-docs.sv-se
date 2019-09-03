---
title: Kopiera data från Netezza med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Netezza till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 7664c2f4fd08e06b51734b5508871b67d1a1b7c9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231407"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopiera data från Netezza med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Netezza. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Netezza till alla datalager för mottagare som stöds. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Netezza-anslutaren stöder parallell kopiering från källan. Mer information finns i avsnittet [parallell kopiering från Netezza](#parallel-copy-from-netezza) .

Azure Data Factory tillhandahåller en inbyggd drivrutin om du vill aktivera anslutningen. Du behöver inte installera en drivrutin för att använda den här anslutningen manuellt.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

Du kan skapa en pipeline som använder en Kopieringsaktivitet med hjälp av .NET SDK, Python SDK, Azure PowerShell, REST API eller en Azure Resource Manager-mall. Se den [Kopieringsaktiviteten självstudien](quickstart-create-data-factory-dot-net.md) stegvisa instruktioner om hur du skapar en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory-entiteter som är specifika för Netezza-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Netezza länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen måste anges till **Netezza**. | Ja |
| connectionString | En ODBC-anslutningssträng att ansluta till Netezza. <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ställa in lösen ord i Azure Key Vault och `pwd` Hämta konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Läs mer från avsnittet [krav](#prerequisites) . Om den inte anges används standard Azure Integration Runtime. |Nej |

En typisk anslutningssträng är `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. I följande tabell beskrivs fler egenskaper som du kan ange:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| SecurityLevel | Nivån på security (SSL/TLS) som drivrutinen använder för anslutning till datalagret. Exempel: `SecurityLevel=preferredSecured`. Värden som stöds är:<br/>- **Endast oskyddade** (**onlyUnSecured**): Driv rutinen använder inte SSL.<br/>- **Föredra oskyddad (preferredUnSecured) (standard)** : Om servern är ett alternativ använder driv rutinen inte SSL. <br/>- **Önskad säkerhet (preferredSecured)** : Om servern är ett alternativ använder driv rutinen SSL. <br/>- **Endast skyddat (onlySecured)** : Driv rutinen ansluter inte om en SSL-anslutning är tillgänglig. | Nej |
| CaCertFile | Den fullständiga sökvägen till SSL-certifikatet som används av servern. Exempel: `CaCertFile=<cert path>;`| Ja, om SSL är aktiverat |

**Exempel**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra lösen ord i Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Det här avsnittet innehåller en lista över egenskaper som har stöd för Netezza-datauppsättningen.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar](concepts-datasets-linked-services.md).

Om du vill kopiera data från Netezza, ange den **typ** egenskapen på datauppsättningen till **NetezzaTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till: **NetezzaTable** | Ja |
| schema | Schemats namn. |Nej (om ”query” i aktivitetskälla har angetts)  |
| table | Namnet på tabellen. |Nej (om ”query” i aktivitetskälla har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och`table` för nya arbets belastningar. | Nej (om ”query” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som har stöd för Netezza-källan.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza som källa

>[!TIP]
>Om du vill läsa in data från Netezza effektivt genom att använda data partitionering kan du läsa mer från [parallell kopiering från Netezza](#parallel-copy-from-netezza) -avsnittet.

Om du vill kopiera data från Netezza, ange den **källa** typ i Kopieringsaktiviteten till **NetezzaSource**. Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen för Kopieringsaktiviteten källan måste anges till **NetezzaSource**. | Ja |
| query | Använda anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"` | Nej (om ”tableName” i datauppsättningen har angetts) |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från Netezza. <br>Tillåtna värden är: **Ingen** (standard), **DataSlice** och **DynamicRange**.<br>När ett partitions alternativ är aktiverat (dvs. inte `None`), styrs graden av parallellitet för data från en Netezza-databas genom [`parallelCopies`](copy-activity-performance.md#parallel-copy) att ställa in på kopierings aktiviteten. | Nej |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när partition alternativet inte `None`är. | Nej |
| partitionColumnName | Ange namnet på den käll kolumn **i Integer-typ** som ska användas av intervall partitionering för parallell kopiering. Om detta inte anges identifieras primär nyckeln för tabellen automatiskt och används som partition-kolumn. <br>Använd när alternativet partition är `DynamicRange`. Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionColumnName` i WHERE-satsen. Se exempel i [parallell kopiering från avsnittet Netezza](#parallel-copy-from-netezza) . | Nej |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för att kopiera data. <br>Använd när partition alternativet är `DynamicRange`. Om du använder Query för att hämta källdata, Hook `?AdfRangePartitionUpbound` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Netezza](#parallel-copy-from-netezza) . | Nej |
| partitionLowerBound | Det minimala värdet för kolumnen partition som ut data ska kopieras. <br>Använd när alternativet partition är `DynamicRange`. Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionLowbound` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från Netezza](#parallel-copy-from-netezza) . | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Parallell kopiering från Netezza

Data Factory Netezza-anslutningsprogrammet tillhandahåller inbyggd data partitionering för att kopiera data från Netezza parallellt. Du kan hitta data partitionerings alternativ i **käll** tabellen för kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-netezza/connector-netezza-partition-options.png)

När du aktiverar partitionerad kopiering körs Data Factory parallella frågor mot din Netezza-källa för att läsa in data efter partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från Netezza-databasen.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från din Netezza-databas. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager, skrivs de om för att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Föreslagna inställningar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell.                                   | **Partitions alternativ**: Data sektor. <br><br/>Under körningen partitionerar Data Factory automatiskt data baserat på de [inbyggda data sektorerna i Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)och kopierar data efter partitioner. |
| Läs in stora mängder data med hjälp av en anpassad fråga.                 | **Partitions alternativ**: Data sektor.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Under körningen ersätts `?AdfPartitionCount` Data Factory (med parallell kopians nummer som angetts för kopierings aktiviteten) och `?AdfDataSliceCondition` med data sektorns partition logik och skickas till Netezza. |
| Läs in stora mängder data med hjälp av en anpassad fråga med en heltals kolumn med jämnt distribuerat värde för intervall partitionering. | **Partitionsalternativ**: Partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med data typen Integer.<br>**Partitionens övre gränser** och **partitionens nedre gränser**: Ange om du vill filtrera mot kolumnen partition för att bara hämta data mellan det nedre och övre intervallet.<br><br>Under körningen ersätts `?AdfRangePartitionColumnName`Data Factory `?AdfRangePartitionUpbound`, och `?AdfRangePartitionLowbound` med det faktiska kolumn namnet och värde intervallet för varje partition och skickas till Netezza. <br>Om t. ex. partitionens kolumn "ID" har angetts med den nedre gränser som 1 och den övre gränser som 80, med parallell kopierings uppsättning som 4, Data Factory hämtar data med 4 partitioner. Deras ID: n är mellan [1, 20], [21, 40], [41, 60] och [61, 80]. |

**Exempel: fråga med data sektor partition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Exempel: fråga med Dynamic Range-partition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
