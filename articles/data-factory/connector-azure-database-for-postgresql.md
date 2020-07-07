---
title: Kopiera data till och från Azure Database for PostgreSQL
description: Lär dig hur du kopierar data till och från Azure Database for PostgreSQL med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: b85e72ae6698cd9fa018c940e158bfcf25279ed5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81410470"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopiera data till och från Azure Database for PostgreSQL med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder funktionen Kopiera aktivitet i Azure Data Factory för att kopiera data från Azure Database for PostgreSQL. Den bygger på [kopierings aktiviteten i Azure Data Factory](copy-activity-overview.md) artikel, som visar en översikt över kopierings aktiviteten.

Den här anslutningen är specialiserad för den [Azure Database for PostgreSQL tjänsten](../postgresql/overview.md). Om du vill kopiera data från en allmän PostgreSQL-databas som finns lokalt eller i molnet använder du [postgresql-anslutningen](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Database for PostgreSQL anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med en [matris för käll/mottagare som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Azure Database for PostgreSQL till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till Azure Database for PostgreSQL. En lista över data lager som kopierings aktiviteten stöder som källor och mottagare finns i tabellen [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory innehåller en inbyggd driv rutin som möjliggör anslutning. Därför behöver du inte installera någon driv rutin manuellt för att använda den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Database for PostgreSQL koppling.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Azure Database for PostgreSQL:

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **AzurePostgreSql**. | Ja |
| Begär | En ODBC-anslutningssträng för att ansluta till Azure Database for PostgreSQL.<br/>Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) för mer information. | Ja |
| connectVia | Den här egenskapen representerar [integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Nej |

En typisk anslutnings sträng är `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Här är fler egenskaper som du kan ställa in per ärende:

| Egenskap | Beskrivning | Alternativ | Obligatorisk |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Den metod som driv rutinen använder för att kryptera data som skickas mellan driv rutinen och databas servern. Till exempel`EncryptionMethod=<0/1/6>;`| 0 (ingen kryptering) **(standard)** /1 (SSL)/6 (RequestSSL) | Nej |
| ValidateServerCertificate (VSC) | Avgör om driv rutinen verifierar certifikatet som skickas av databas servern när SSL-kryptering är aktiverat (krypterings metod = 1). Till exempel`ValidateServerCertificate=<0/1>;`| 0 (inaktive rad) **(standard)** /1 (aktive rad) | Nej |

**Exempel**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Exempel**:

***Lagra lösen ord i Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns [i data uppsättningar i Azure Data Factory](concepts-datasets-linked-services.md). Det här avsnittet innehåller en lista över egenskaper som Azure Database for PostgreSQL stöder i data uppsättningar.

Om du vill kopiera data från Azure Database for PostgreSQL anger du egenskapen type för data uppsättningen till **AzurePostgreSqlTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **AzurePostgreSqlTable** | Ja |
| tableName | Tabellens namn | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av en Azure Database for PostgreSQL källa.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database for PostgreSql som källa

Om du vill kopiera data från Azure Database for PostgreSQL anger du käll typen i kopierings aktiviteten till **AzurePostgreSqlSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **AzurePostgreSqlSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempelvis: `"SELECT * FROM MyTable"` | Nej (om egenskapen tableName i data uppsättningen anges) |

**Exempel**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL som mottagare

För att kopiera data till Azure Database for PostgreSQL, stöds följande egenskaper i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **AzurePostgreSQLSink**. | Ja |
| preCopyScript | Ange en SQL-fråga för kopierings aktiviteten som ska köras innan du skriver data till Azure Database for PostgreSQL i varje körning. Du kan använda den här egenskapen för att rensa de förinstallerade data. | Nej |
| writeBatchSize | Infogar data i Azure Database for PostgreSQL tabellen när buffertstorleken når writeBatchSize.<br>Tillåtet värde är ett heltal som representerar antalet rader. | Nej (standard är 10 000) |
| writeBatchTimeout | Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes.<br>Tillåtna värden är TimeSpan-strängar. Ett exempel är 00:30:00 (30 minuter). | Nej (standard är 00:00:30) |

**Exempel**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Mer information om egenskaperna finns [i lookup-aktivitet i Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
