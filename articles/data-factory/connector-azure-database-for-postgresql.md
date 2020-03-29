---
title: Kopiera data till och från Azure Database för PostgreSQL
description: Lär dig hur du kopierar data till och från Azure Database för PostgreSQL med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444379"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopiera data till och från Azure Database för PostgreSQL med hjälp av Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktivitetsfunktionen i Azure Data Factory för att kopiera data från Azure Database för PostgreSQL. Den bygger på [kopiera aktivitet i Azure Data Factory](copy-activity-overview.md) artikeln, som presenterar en allmän översikt över kopieringsaktivitet.

Den här anslutningen är specialiserad för [Azure Database for PostgreSQL-tjänsten](../postgresql/overview.md). Om du vill kopiera data från en allmän PostgreSQL-databas som finns lokalt eller i molnet använder du [PostgreSQL-anslutningen](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure-databasen för PostgreSQL-anslutning stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med en [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Azure Database för PostgreSQL till alla sink-datalager som stöds. Du kan också kopiera data från alla källdatalager som stöds till Azure Database för PostgreSQL. En lista över datalager som kopieringsaktiviteten stöder som källor och sänkor finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory tillhandahåller en inbyggd drivrutin för anslutning. Därför behöver du inte installera någon drivrutin manuellt för att använda den här kontakten.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Database för PostgreSQL-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Azure Database for PostgreSQL-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till: **AzurePostgreSql**. | Ja |
| Connectionstring | En ODBC-anslutningssträng för att ansluta till Azure Database för PostgreSQL.<br/>Du kan också placera ett lösenord i `password` Azure Key Vault och hämta konfigurationen ur anslutningssträngen. Mer information finns i följande exempel och [Store-autentiseringsuppgifter i Azure Key Vault.](store-credentials-in-key-vault.md) | Ja |
| connectVia (på) | Den här egenskapen representerar [den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

En typisk anslutningssträng är `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Här är fler egenskaper som du kan ange per ditt ärende:

| Egenskap | Beskrivning | Alternativ | Krävs |
|:--- |:--- |:--- |:--- |
| KrypteringMethod (EM)| Den metod som drivrutinen använder för att kryptera data som skickas mellan drivrutinen och databasservern. Till exempel kan`EncryptionMethod=<0/1/6>;`| 0 (Ingen kryptering) **(standard)** / 1 (SSL) / 6 (RequestSSL) | Inga |
| ValideraServercertificate (VSC) | Avgör om drivrutinen validerar certifikatet som skickas av databasservern när SSL-kryptering är aktiverat (Krypteringsmetod=1). Till exempel kan`ValidateServerCertificate=<0/1>;`| 0 (Inaktiverad) **(standard)** / 1 (aktiverad) | Inga |

**Exempel:**

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

**Exempel:**

***Lagra lösenord i Azure Key Vault***

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns [i Datauppsättningar i Azure Data Factory](concepts-datasets-linked-services.md). Det här avsnittet innehåller en lista över egenskaper som Azure Database for PostgreSQL stöder i datauppsättningar.

Om du vill kopiera data från Azure Database för PostgreSQL anger du egenskapen type för datauppsättningen till **AzurePostgreSqlTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för datauppsättningen måste anges till **AzurePostgreSqlTable** | Ja |
| tableName | Tabellens namn | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel:**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns [i Pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av en Azure-databas för PostgreSQL-källa.

### <a name="azure-database-for-postgresql-as-source"></a>Azure-databas för PostgreSql som källa

Om du vill kopiera data från Azure Database för PostgreSQL anger du källtypen i kopieringsaktiviteten till **AzurePostgreSqlSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för kopians aktivitetskälla måste anges till **AzurePostgreSqlSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"` | Nej (om egenskapen tableName i datauppsättningen har angetts) |

**Exempel:**

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

### <a name="azure-database-for-postgresql-as-sink"></a>Azure-databas för PostgreSQL som mottagare

Om du vill kopiera data till Azure Database för PostgreSQL stöds följande egenskaper i avsnittet kopiera **aktivitetsmottagare:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för copy activity sink måste anges till **AzurePostgreSQLSink**. | Ja |
| preCopyScript | Ange en SQL-fråga för kopieringsaktiviteten som ska köras innan du skriver data till Azure Database för PostgreSQL i varje körning. Du kan använda den här egenskapen för att rensa förinstallerade data. | Inga |
| skriverBatchSize | Infogar data i Azure Database för PostgreSQL-tabellen när buffertstorleken når writeBatchSize.<br>Tillåtet värde är ett heltal som representerar antalet rader. | Nej (standard är 10 000) |
| skriverBatchTimeout | Vänta på att batchinsatsen ska slutföras innan den har klarats upp.<br>Tillåtna värden är Timespan-strängar. Ett exempel är 00:30:00 (30 minuter). | Nej (standard är 00:00:30) |

**Exempel:**

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

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Mer information om egenskaperna finns [i Uppslagsaktivitet i Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [i Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
