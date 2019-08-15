---
title: Kopiera data från PostgreSQL med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från PostgreSQL till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0efb884de9deaa2784e160785c26d78179da6567
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966879"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Kopiera data från PostgreSQL med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-postgresql-connector.md)
> * [Aktuell version](connector-postgresql.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en PostgreSQL-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från PostgreSQL-databasen till alla mottagar data lager som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder denna PostgreSQL-anslutning PostgreSQL **version 7,4 och senare**.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

För den lokala IR-versionen som är lägre än 3,7 måste du installera Ngpsql-dataprovidern [för postgresql](https://go.microsoft.com/fwlink/?linkid=282716) med version mellan 2.0.12 och 3.1.9 på den integration runtime datorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för PostgreSQL-anslutaren.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten PostgreSQL:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till: **PostgreSql** | Ja |
| connectionString | En ODBC-anslutningssträng att ansluta till Azure Database för PostgreSQL. <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ställa in lösen ord i Azure Key Vault och `password` Hämta konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Läs mer från avsnittet [krav](#prerequisites) . Om den inte anges används standard Azure Integration Runtime. |Nej |

En typisk anslutningssträng är `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Fler egenskaper som du kan ställa in per ditt ärende:

| Egenskap | Beskrivning | Alternativ | Krävs |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoden drivrutinen använder för att kryptera data som skickas mellan drivrutinen och databasservern. T.ex. `EncryptionMethod=<0/1/6>;`| 0 (Ingen kryptering) **(standard)** / 1 (SSL) / 6 (RequestSSL) | Nej |
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

**Exempel: lagra lösen ord i Azure Key Vault**

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

Om du använder PostgreSQL-länkad tjänst med följande nytto Last, stöds den fortfarande som den är, medan du föreslås att använda den nya som skickas.

**Föregående nytto last:**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av PostgreSQL DataSet.

Om du vill kopiera data från PostgreSQL anger du egenskapen type för data uppsättningen till **RelationalTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i PostgreSQL-databasen. | Nej (om ”query” i aktivitetskälla har angetts) |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av PostgreSQL-källan.

### <a name="postgresql-as-source"></a>PostgreSQL som källa

Om du vill kopiera data från PostgreSQL anger du käll typen i kopierings aktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **source** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för kopierings aktivitets källan måste anges till: **RelationalSource** | Ja |
| query | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nej (om ”tableName” i datauppsättningen har angetts) |

> [!NOTE]
> Schema-och tabell namn är Skift läges känsliga. Omge dem med `""` (dubbla citat tecken) i frågan.

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
