---
title: Kopiera data från MySQL med Azure Data Factory | Microsoft Docs
description: Läs mer om MySQL-anslutningen i Azure Data Factory som låter dig kopiera data från en MySQL-databas till ett dataarkiv som stöds som en mottagare.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/23/2018
ms.author: jingwang
ms.openlocfilehash: bb3179f1db077aacc7e36acf16486ee77a7f36e7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051271"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Kopiera data från MySQL med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-onprem-mysql-connector.md)
> * [Aktuell version](connector-mysql.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en MySQL-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från MySQL-databas till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här MySQL-anslutningen MySQL **version 5.1 och högre**.

## <a name="prerequisites"></a>Förutsättningar

Om MySQL-databasen inte är offentligt tillgänglig, måste du ställa in en Self-hosted integrering Runtime. Mer information om automatisk värdbaserade integration körningar, se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikel. Integration Runtime innehåller en inbyggd MySQL-drivrutin från version 3.7, därför behöver du inte installera en drivrutin manuellt.

För Self-hosted IR-versionen är lägre än 3.7, måste du installera den [MySQL Connector/Net för Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) version mellan 6.6.5 och 6.10.7 på Integration Runtime-datorn. Den här 32-bitars-drivrutinen är kompatibel med 64-bitars IR.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till MySQL-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för MySQL länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **MySql** | Ja |
| connectionString | Ange information som behövs för att ansluta till Azure-databas för MySQL-instansen. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

En typisk anslutningssträng är `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Fler egenskaper som du kan ange per ditt fall:

| Egenskap  | Beskrivning | Alternativ | Krävs |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Det här alternativet anger om drivrutinen använder SSL-kryptering och kontroll när du ansluter till MySQL. T.ex. `SSLMode=<0/1/2/3/4>`| INAKTIVERAD (0) / PREFERRED (1) **(standard)** / krävs (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nej |
| useSystemTrustStore | Det här alternativet anger om du vill använda ett certifikat från arkivet med betrodda system eller från en angiven PEM-fil. T.ex. `UseSystemTrustStore=<0/1>;`| (1) aktiveras / inaktiveras (0) **(standard)** | Nej |

**Exempel:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Om du använde MySQL länkade tjänsten med följande nyttolast fortfarande stöds som-är, medan du rekommenderas för att använda den nya framöver.

**Tidigare nyttolast:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av MySQL dataset.

Ange typegenskapen för dataset för att kopiera data från MySQL, **RelationalTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i MySQL-databas. | Nej (om ”fråga” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av MySQL-källa.

### <a name="mysql-as-source"></a>MySQL som källa

Om du vill kopiera data från MySQL som källtypen i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tabellnamn” i datamängden har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-mysql"></a>Mappning för MySQL-datatyp

När du kopierar data från MySQL, används följande mappningar från MySQL-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| MySQL-datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
