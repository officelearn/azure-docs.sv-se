---
title: Kopiera data från PostgreSQL med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från PostgreSQL till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 8515b3f357d77ea4f3d98101f8dd058f13b69206
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091128"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Kopiera data från PostgreSQL med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-onprem-postgresql-connector.md)
> * [Aktuell version](connector-postgresql.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en PostgreSQL-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från PostgreSQL-databas till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här kopplingen PostgreSQL PostgreSQL **version 7.4 och senare**.

## <a name="prerequisites"></a>Förutsättningar

Om din PostgreSQL-databasen inte är offentligt tillgänglig, måste du ställer in en lokal Integration Runtime. Mer information om IR-körningar, se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) artikeln. Integreringskörningen innehåller en inbyggd PostgreSQL-drivrutin från och med version 3.7, måste du därför inte att manuellt installera en drivrutin.

För lokal IR-versionen är lägre än 3.7, måste du installera den [Ngpsql dataprovider för PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) med version mellan 2.0.12 och 3.1.9 på Integration Runtime-datorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för PostgreSQL-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för PostgreSQL länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **PostgreSql** | Ja |
| connectionString | En ODBC-anslutningssträng att ansluta till Azure Database för PostgreSQL. <br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory. Du kan också publicera lösenord i Azure Key Vault och använda pull i `password` konfiguration av anslutningssträngen. Följande exempel finns och [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

En typisk anslutningssträng är `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Fler egenskaper som du kan ställa in per ditt ärende:

| Egenskap  | Beskrivning | Alternativ | Krävs |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoden drivrutinen använder för att kryptera data som skickas mellan drivrutinen och databasservern. T.ex. `ValidateServerCertificate=<0/1/6>;`| 0 (Ingen kryptering) **(standard)** / 1 (SSL) / 6 (RequestSSL) | Nej |
| ValidateServerCertificate (VSC) | Avgör om drivrutinen att bekräfta det certifikat som skickas av databasservern när SSL-kryptering är aktiverat (krypteringsmetod = 1). T.ex. `ValidateServerCertificate=<0/1>;`| 0 (inaktiverad) **(standard)** / 1 (aktiverad) | Nej |

**Exempel:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
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
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;"
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

Om du använde PostgreSQL länkade tjänsten med följande nyttolasten, stöds det fortfarande som – är att medan du rekommenderas för att använda den nya framöver.

**Föregående nyttolast:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av PostgreSQL-datauppsättningen.

Om du vill kopiera data från PostgreSQL, ange typegenskapen på datauppsättningen till **RelationalTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i databasen för PostgreSQL. | Nej (om ”frågan” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av PostgreSQL-källa.

### <a name="postgresql-as-source"></a>PostgreSQL som källa

Om du vill kopiera data från PostgreSQL, ange typ av datakälla i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nej (om ”tableName” i datauppsättningen har angetts) |

> [!NOTE]
> Schema och tabellnamn är skiftlägeskänsliga. Sätter dem inom `""` (dubbla citattecken) i frågan.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
