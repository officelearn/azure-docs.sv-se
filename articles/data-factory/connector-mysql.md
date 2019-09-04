---
title: Kopiera data från MySQL med Azure Data Factory | Microsoft Docs
description: Lär dig mer om MySQL Connector i Azure Data Factory som gör att du kan kopiera data från en MySQL-databas till ett data lager som stöds som mottagare.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: d76b51aa5117e662e9ff17bb91516c758de3071c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277715"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Kopiera data från MySQL med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-mysql-connector.md)
> * [Aktuell version](connector-mysql.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en MySQL-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

>[!NOTE]
>Om du vill kopiera data från eller till [Azure Database for MySQL](../mysql/overview.md) tjänsten använder du den specialiserade [Azure Database for MySQL-anslutningen](connector-azure-database-for-mysql.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från MySQL-databasen till alla mottagar data lager som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här MySQL-anslutaren MySQL **version 5,6 och 5,7**.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime innehåller en inbyggd MySQL-drivrutin som börjar med version 3,7, och därför behöver du inte installera någon driv rutin manuellt.

För den lokala IR-versionen som är lägre än 3,7 måste du installera [MySQL Connector/net för Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) -versionen mellan 6.6.5 och 6.10.7 på den integration runtime datorn. Den här 32-bitars driv rutinen är kompatibel med 64-bitars IR.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för MySQL Connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för MySQL-länkade tjänster:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till: **MySql** | Ja |
| connectionString | Ange information som krävs för att ansluta till Azure Database for MySQL-instansen.<br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ställa in lösen ord i Azure Key Vault och `password` Hämta konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Läs mer från avsnittet [krav](#prerequisites) . Om den inte anges används standard Azure Integration Runtime. |Nej |

En typisk anslutningssträng är `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Fler egenskaper som du kan ställa in per ditt ärende:

| Egenskap | Beskrivning | Alternativ | Obligatorisk |
|:--- |:--- |:--- |:--- |
| SSLMode | Det här alternativet anger om driv rutinen använder SSL-kryptering och verifiering vid anslutning till MySQL. T.ex. `SSLMode=<0/1/2/3/4>`| Inaktive rad (0)/PRIORITERAt (1) **(standard)** /obligatoriskt (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | Nej |
| UseSystemTrustStore | Det här alternativet anger om du vill använda ett CA-certifikat från arkivet för system förtroende eller från en angiven PEM-fil. T.ex. `UseSystemTrustStore=<0/1>;`| Aktiverat (1)/inaktiverat (0) **(standard)** | Nej |

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

**Exempel: lagra lösen ord i Azure Key Vault**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;"
            },
            "password": { 
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

Om du använder MySQL-länkad tjänst med följande nytto Last, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya som skickas.

**Föregående nytto last:**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av MySQL-datauppsättningen.

Följande egenskaper stöds för att kopiera data från MySQL:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till: **MySqlTable** | Ja |
| tableName | Namnet på tabellen i MySQL-databasen. | Nej (om ”query” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du använder typ `RelationalTable` av data uppsättning, stöds den fortfarande som den är, medan du föreslås att använda den nya som går framåt.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av MySQL-källan.

### <a name="mysql-as-source"></a>MySQL som källa

För att kopiera data från MySQL stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för kopierings aktivitets källan måste anges till: **MySqlSource** | Ja |
| query | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder typ `RelationalSource` av källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen framåt.

## <a name="data-type-mapping-for-mysql"></a>Data typs mappning för MySQL

När du kopierar data från MySQL används följande mappningar från MySQL-datatyper för att Azure Data Factory interimistiska data typer. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Data typen MySQL | Data factory tillfälliga datatyp |
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
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
