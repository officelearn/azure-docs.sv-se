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
ms.openlocfilehash: 67d59e3f733efe5a248e6763f46402302496d437
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444379"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopiera data till och från Azure Database for PostgreSQL med Azure Data Factory

Den här artikeln beskriver hur du använder funktionen Kopiera aktivitet i Azure Data Factory för att kopiera data från Azure Database for PostgreSQL. Den bygger på [kopierings aktiviteten i Azure Data Factory](copy-activity-overview.md) artikel, som visar en översikt över kopierings aktiviteten.

Den här anslutningen är specialiserad för den [Azure Database for PostgreSQL tjänsten](../postgresql/overview.md). Om du vill kopiera data från en allmän PostgreSQL-databas som finns lokalt eller i molnet använder du [postgresql-anslutningen](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Database for PostgreSQL anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med en [matris för käll/mottagare som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Azure Database för PostgreSQL till alla datalager för mottagare som stöds. Du kan också kopiera data från alla käll data lager som stöds till Azure Database for PostgreSQL. En lista över data lager som kopierings aktiviteten stöder som källor och mottagare finns i tabellen [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory tillhandahåller en inbyggd drivrutin om du vill aktivera anslutningen. Därför behöver du inte installera någon driv rutin manuellt för att använda den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Database for PostgreSQL koppling.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten Azure Database for PostgreSQL:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **AzurePostgreSql**. | Ja |
| connectionString | En ODBC-anslutningssträng att ansluta till Azure Database för PostgreSQL.<br/>Du kan också ange ett lösen ord i Azure Key Vault och hämta `password`-konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) för mer information. | Ja |
| connectVia | Den här egenskapen representerar [integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Inga |

En typisk anslutningssträng är `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Här är fler egenskaper som du kan ställa in per ärende:

| Egenskap | Beskrivning | Alternativ | Krävs |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoden drivrutinen använder för att kryptera data som skickas mellan drivrutinen och databasservern. Till exempel `EncryptionMethod=<0/1/6>;`| 0 (Ingen kryptering) **(standard)** / 1 (SSL) / 6 (RequestSSL) | Inga |
| ValidateServerCertificate (VSC) | Avgör om driv rutinen verifierar certifikatet som skickas av databas servern när SSL-kryptering är aktiverat (krypterings metod = 1). Till exempel `ValidateServerCertificate=<0/1>;`| 0 (inaktiverad) **(standard)** / 1 (aktiverad) | Inga |

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

För att kopiera data från Azure Database för PostgreSQL, ange typegenskapen på datauppsättningen till **AzurePostgreSqlTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **AzurePostgreSqlTable** | Ja |
| tableName | Tabellens namn | Nej (om ”query” i aktivitetskälla har angetts) |

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

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database för PostgreSql som källa

För att kopiera data från Azure Database för PostgreSQL, ange typ av datakälla i kopieringsaktiviteten till **AzurePostgreSqlSource**. Följande egenskaper stöds i kopieringsaktiviteten **source** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **AzurePostgreSqlSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"` | Nej (om egenskapen tableName i data uppsättningen anges) |

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

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **AzurePostgreSQLSink**. | Ja |
| preCopyScript | Ange en SQL-fråga för kopierings aktiviteten som ska köras innan du skriver data till Azure Database for PostgreSQL i varje körning. Du kan använda den här egenskapen för att rensa de förinstallerade data. | Inga |
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
