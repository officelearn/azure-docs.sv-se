---
title: Kopiera data från Netezza med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Netezza till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c7e17f7c4493560bd6118b8d4837fd795a6ab0c8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422866"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopiera data från Netezza med hjälp av Azure Data Factory

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från Netezza. Artikeln bygger på [Kopiera aktivitet i Azure Data Factory](copy-activity-overview.md), som presenterar en allmän översikt över kopiera aktivitet.

>[!TIP]
>För scenario med datamigrering från Netezza till Azure kan du läsa mer från [Använd Azure Data Factory för att migrera data från lokal Netezza-server till Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Netezza-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)


Du kan kopiera data från Netezza till alla sink-datalager som stöds. En lista över datalager som Kopierar aktivitet stöder som källor och sänkor finns i [Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Netezza-anslutning stöder parallellkopiering från källan. Se [avsnittet Parallellkopia från Netezza](#parallel-copy-from-netezza) för mer information.

Azure Data Factory tillhandahåller en inbyggd drivrutin för anslutning. Du behöver inte installera någon drivrutin manuellt för att använda den här kontakten.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

Du kan skapa en pipeline som använder en kopieringsaktivitet med hjälp av .NET SDK, Python SDK, Azure PowerShell, REST API eller en Azure Resource Manager-mall. Se [självstudien Kopiera aktivitet](quickstart-create-data-factory-dot-net.md) för steg-för-steg-instruktioner om hur du skapar en pipeline som har en kopieringsaktivitet.

I följande avsnitt finns information om egenskaper som du kan använda för att definiera datafabrikentiteter som är specifika för Netezza-kopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den Netezza-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **Netezza**. | Ja |
| Connectionstring | En ODBC-anslutningssträng som ska anslutas till Netezza. <br/>Du kan också placera lösenord i `pwd` Azure Key Vault och dra ut konfigurationen ur anslutningssträngen. Mer information finns i följande exempel och [Store-autentiseringsuppgifter i](store-credentials-in-key-vault.md) azure key vault-artikeln. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om inget anges används standardkörningen för Azure Integration Runtime. |Inga |

En typisk anslutningssträng är `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. I följande tabell beskrivs fler egenskaper som du kan ange:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| SäkerhetNivå | Den säkerhetsnivå (SSL/TLS) som drivrutinen använder för anslutningen till datalagret. Exempel: `SecurityLevel=preferredSecured`. Värden som stöds är:<br/>- **Endast osäker** **(endastOSecured):** Drivrutinen använder inte SSL.<br/>- **Föredragen oskyddad (preferredUnSecured) (standard):** Om servern ger ett val använder drivrutinen inte SSL. <br/>- **Önskad säker (preferredSecured):** Om servern ger ett val använder drivrutinen SSL. <br/>- **Endast säker (onlySecured)**: Drivrutinen ansluter inte om inte en SSL-anslutning är tillgänglig. | Inga |
| CaCertFile (cacertfile) | Den fullständiga sökvägen till SSL-certifikatet som används av servern. Exempel: `CaCertFile=<cert path>;`| Ja, om SSL är aktiverat |

**Exempel**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra lösenord i Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
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

Det här avsnittet innehåller en lista över egenskaper som Netezza-datauppsättningen stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar](concepts-datasets-linked-services.md).

Om du vill kopiera data från Netezza anger du **egenskapen type** för datauppsättningen till **NetezzaTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **NetezzaTable** | Ja |
| Schemat | Namnet på schemat. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tableName | Namn på tabellen med schema. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` `table` och för ny arbetsbelastning. | Nej (om "fråga" i aktivitetskällan har angetts) |

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

Det här avsnittet innehåller en lista över egenskaper som Netezza-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza som källa

>[!TIP]
>Om du vill läsa in data från Netezza effektivt med hjälp av datapartitionering kan du läsa mer från [avsnittet Parallellkopia från Netezza.](#parallel-copy-from-netezza)

Om du vill kopiera data från Netezza anger du **källtypen** i Kopiera aktivitet till **NetezzaSource**. Följande egenskaper stöds i **källavsnittet** Kopiera aktivitet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för källan Kopiera aktivitet måste anges till **NetezzaSource**. | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"` | Nej (om "tableName" i datauppsättningen har angetts) |
| partitionOptions | Anger de datapartitionsalternativ som används för att läsa in data från Netezza. <br>Tillåt värden är: **Ingen** (standard), **DataSlice**och **DynamicRange**.<br>När ett partitionsalternativ är aktiverat (det vill ha ), `None`styrs graden av parallellitet till samtidig inläsning av data från en Netezza-databas genom [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) att ange på kopieringsaktiviteten. | Inga |
| partitionSätta | Ange gruppen för inställningarna för datapartitionering. <br>Använd när partitionsalternativet `None`inte är . | Inga |
| partitionColumnName | Ange namnet på källkolumnen **i heltalstyp** som ska användas av områdespartitionering för parallellkopia. Om inget anges, är den primära nyckeln i tabellen automatisktdet och används som partitionskolumnen. <br>Använd när partitionsalternativet är `DynamicRange`. Om du använder en fråga för `?AdfRangePartitionColumnName` att hämta källdata ansluter du WHERE-satsen. Se exempel i [Parallellkopia från avsnittet Netezza.](#parallel-copy-from-netezza) | Inga |
| partitionUpperBound | Det maximala värdet för partitionskolumnen för att kopiera data ut. <br>Använd när partitionsalternativet är `DynamicRange`. Om du använder frågan för `?AdfRangePartitionUpbound` att hämta källdata ansluter du WHERE-satsen. Ett exempel finns i avsnittet [Parallellkopia från Netezza.](#parallel-copy-from-netezza) | Inga |
| partitionLowerBound | Det minsta värdet för partitionskolumnen för att kopiera data ut. <br>Använd när partitionsalternativet är `DynamicRange`. Om du använder en fråga för `?AdfRangePartitionLowbound` att hämta källdata ansluter du WHERE-satsen. Ett exempel finns i avsnittet [Parallellkopia från Netezza.](#parallel-copy-from-netezza) | Inga |

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

## <a name="parallel-copy-from-netezza"></a>Parallell kopia från Netezza

Data Factory Netezza-anslutningen tillhandahåller inbyggd datapartitionering för att kopiera data från Netezza parallellt. Du hittar alternativ för datapartitionering i **källtabellen** för kopieringsaktiviteten.

![Skärmbild av partitionsalternativ](./media/connector-netezza/connector-netezza-partition-options.png)

När du aktiverar partitionerad kopia kör Data Factory parallella frågor mot Netezza-källan för att läsa in data efter partitioner. Den parallella graden [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) styrs av inställningen på kopieringsaktiviteten. Om du till `parallelCopies` exempel anger fyra genererar och kör Data Factory samtidigt fyra frågor baserat på det angivna partitionsalternativet och inställningarna, och varje fråga hämtar en del data från Netezza-databasen.

Du föreslås aktivera parallellkopiering med datapartitionering, särskilt när du läser in stora mängder data från Netezza-databasen. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat datalager rekommenderas att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Full belastning från stort bord.                                   | **Partition alternativ**: Data Slice. <br><br/>Under körningen partitionerar Data Factory automatiskt data baserat på [Netezzas inbyggda datasegment](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)och kopierar data efter partitioner. |
| Läs in stora mängder data med hjälp av en anpassad fråga.                 | **Partition alternativ**: Data Slice.<br>**Fråga** `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`: .<br>Under körningen ersätter `?AdfPartitionCount` Data Factory (med parallellkopieringsnummer inställt på kopieringsaktivitet) och `?AdfDataSliceCondition` med partitionslogiken för datasegmentet och skickas till Netezza. |
| Läs in stora mängder data med hjälp av en anpassad fråga med en heltalskolumn med jämnt fördelat värde för intervallpartitionering. | **Partitionsalternativ**: Partitionering med dynamiskt omfång.<br>**Fråga** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Partitionskolumnen**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med heltalsdatatyp.<br>**Partitionens övre gräns** och **partitionens nedre gräns:** Ange om du vill filtrera mot partitionskolumnen för att hämta data endast mellan det nedre och det övre intervallet.<br><br>Under körningen ersätter `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Data `?AdfRangePartitionLowbound` Factory , och med de faktiska kolumnnamn- och värdeintervallen för varje partition och skickas till Netezza. <br>Om partitionskolumnen "ID" har angetts med den nedre gränsen som 1 och den övre gränsen som 80, med parallellkopiering inställd som 4, hämtar Data Factory data med 4 partitioner. Deras ID är mellan [1,20], [21, 40], [41, 60] respektive [61, 80]. |

**Exempel: fråga med datasegmentpartition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Exempel: fråga med partition för dynamiskt omfång**

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

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg

En lista över datalager som kopierar aktivitet stöder som källor och sänkor i Azure Data Factory finns [i Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
