---
title: Kopiera data från Sybase med Azure Data Factory
description: Lär dig hur du kopierar data från Sybase till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 495d16efcc26fc336a87c0f2d88f5202ab0b4a3e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416624"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Kopiera data från Sybase med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-sybase-connector.md)
> * [Aktuell version](connector-sybase.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från en Sybase-databas. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Sybase-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Sybase-databasen till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här Sybase-kontakten stöder:

- SAP Sybase SQL Anywhere (ASA) **version 16 och senare;** IQ och ASE stöds inte.
- Kopiera data med **grundläggande** autentisering eller Windows-autentisering. **Windows**

## <a name="prerequisites"></a>Krav

Om du vill använda den här Sybase-kopplingen måste du:

- Konfigurera en självvärderad integrationskörning. Mer information finns i artikeln Integration Runtime för [självvärd.](create-self-hosted-integration-runtime.md)
- Installera [dataprovidern för Sybase iAnywhere.Data.SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 eller högre på Integration Runtime-datorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Sybase-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Sybase-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Sybase** | Ja |
| server | Namn på Sybase-servern. |Ja |
| databas | Namn på Sybase-databasen. |Ja |
| authenticationType | Typ av autentisering som används för att ansluta till Sybase-databasen.<br/>Tillåtna värden är: **Grundläggande**och **Windows**. |Ja |
| användarnamn | Ange användarnamn som ska anslutas till Sybase-databasen. |Ja |
| password | Ange lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. En självvärdad integrationskörning krävs som nämns i [Förutsättningar](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Sybase-datauppsättningen.

Så här kopierar du data från Sybase och följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **SybaseTable** | Ja |
| tableName | Namnet på tabellen i Sybase-databasen. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du `RelationalTable` använde maskinskriven datauppsättning stöds den fortfarande som den är, medan du föreslås använda den nya framöver.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Sybase-källan.

### <a name="sybase-as-source"></a>Sybase som källa

Om du vill kopiera data från Sybase stöds följande egenskaper i avsnittet kopiera **aktivitetskälla:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste ställas in på: **SybaseSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du `RelationalSource` använde den maskinskrivna källan stöds den fortfarande som den är, medan du föreslås använda den nya framåt.

## <a name="data-type-mapping-for-sybase"></a>Mappning av datatyp för Sybase

När du kopierar data från Sybase används följande mappningar från Sybase-datatyper till Azure Data Factory interimsdatatyper. Se [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta mer om hur du kopierar aktivitetsschemat och datatypen till diskhon.

Sybase stöder T-SQL-typer. En mappningstabell från SQL-typer till interimdatatyper i Azure Data Factory finns i Azure SQL Database Connector - mappningsavsnitt för [datatyp.](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database)

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).



## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
