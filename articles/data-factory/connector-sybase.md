---
title: "Kopiera data från Sybase med Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från Sybase till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 1170ee7232e1046e194f5223f7b7bf582ef18dfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Kopiera data från Sybase med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-onprem-sybase-connector.md)
> * [Version 2 – förhandsversion](connector-sybase.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en Sybase-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Sybase-anslutningen i V1](v1/data-factory-onprem-sybase-connector.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data från Sybase-databas till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Sybase-anslutningen:

- Sybase **version 16 och högre**.
- Kopiera data med hjälp av **grundläggande** eller **Windows** autentisering.

## <a name="prerequisites"></a>Krav

Om du vill använda denna Sybase-anslutning måste du:

- Ställ in en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikeln för information.
- Installera den [dataprovider för Sybase iAnywhere.Data.SQLAnywhere](http://go.microsoft.com/fwlink/?linkid=324846) 16 eller senare på Integration Runtime-datorn.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Sybase-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Sybase länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Sybase** | Ja |
| server | Namnet på Sybase-servern. |Ja |
| Databasen | Namnet på Sybase-databasen. |Ja |
| Schemat | Namnet på schemat i databasen. |Nej |
| AuthenticationType | Typ av autentisering som används för att ansluta till Sybase-databasen.<br/>Tillåtna värden är: **grundläggande**, och **Windows**. |Ja |
| användarnamn | Ange användarnamn för att ansluta till Sybase-databasen. |Ja |
| lösenord | Ange lösenordet för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString. |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En Self-hosted integrering Runtime krävs enligt [krav](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Sybase dataset.

Ange typegenskapen för dataset för att kopiera data från Sybase, **RelationalTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i Sybase-databasen. | Nej (om ”fråga” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "SybaseDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Sybase-källa.

### <a name="sybase-as-source"></a>Sybase som källa

Om du vill kopiera data från Sybase anger källa i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tabellnamn” i datamängden har angetts) |

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

## <a name="data-type-mapping-for-sybase"></a>Datatypen mappning för Sybase

När du kopierar data från Sybase, används följande mappningar från Sybase-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

Sybase stöder T-SQL-typer. En Mappningstabell från SQL-typer till Azure Data Factory tillfälliga datatyper finns i [Azure SQL Database Connector - datatypsmappningen](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) avsnitt.


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).