---
title: Kopiera data från Teradata med Azure Data Factory | Microsoft Docs
description: Läs mer om Teradata koppling av Data Factory-tjänsten som låter dig kopieringsdata från Teradata-databas till datalager som stöds av Data Factory som mottagare.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 37e7281af87a8cfc57aae95411eb2d4cce9eef65
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228070"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Kopiera data från Teradata med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuell version](connector-teradata.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en Teradata-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Teradata-databas till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Teradata-anslutningen:

- Teradata **version 12 och senare**.
- Kopiera data med hjälp av **grundläggande** eller **Windows** autentisering.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda denna Teradata-anslutning måste du:

- Konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.
- Installera den [.NET Data Provider för Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) version 14 eller senare på den Integration Runtime-datorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Teradata-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Teradata länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **Teradata** | Ja |
| server | Namnet på Teradata-servern. | Ja |
| authenticationType | Typ av autentisering som används för att ansluta till Teradata-databasen.<br/>Tillåtna värden är: **grundläggande**, och **Windows**. | Ja |
| användarnamn | Ange användarnamn för att ansluta till Teradata-databasen. | Ja |
| lösenord | Ange lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En lokal Integration Runtime krävs enligt [krav](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-datauppsättningen.

Kopiera data från Teradata genom att ange typegenskapen på datauppsättningen till **RelationalTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i Teradata-databasen. | Nej (om ”frågan” i aktivitetskälla har angetts) |

**Exempel:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-källa.

### <a name="teradata-as-source"></a>Teradata som källkod

För att kopiera data från Teradata, ange typ av datakälla i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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

## <a name="data-type-mapping-for-teradata"></a>Datatypen mappning för Teradata

När du kopierar data från Teradata, används följande mappningar från Teradata-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Datatypen för Teradata | Data factory tillfälliga datatyp |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte] |
| Byte |Byte] |
| ByteInt |Int16 |
| Char |Sträng |
| CLOB |Sträng |
| Date |DateTime |
| decimaltal |decimaltal |
| Double-värde |Double-värde |
| Bild |Sträng |
| Integer |Int32 |
| Intervall för dag |Tidsintervall |
| Dag för intervall och timme |Tidsintervall |
| Intervall dag till minut |Tidsintervall |
| Intervall dag till andra |Tidsintervall |
| Intervall timme |Tidsintervall |
| Intervall för timme och minut |Tidsintervall |
| Intervall timme till andra |Tidsintervall |
| Intervall minut |Tidsintervall |
| Intervall minut till andra |Tidsintervall |
| Intervall för månad |Sträng |
| Intervall för andra |Tidsintervall |
| Intervall år |Sträng |
| Intervall årets månad |Sträng |
| Tal |Double-värde |
| Period(Date) |Sträng |
| Period(Time) |Sträng |
| Period (tid med tidszon) |Sträng |
| Period(timestamp) |Sträng |
| Period (tidsstämpel med tidszon) |Sträng |
| SmallInt |Int16 |
| Tid |Tidsintervall |
| Tid med tidszon |Sträng |
| Tidsstämpel |DateTime |
| Tidsstämpel med tidszon |DateTimeOffset |
| VarByte |Byte] |
| VarChar |Sträng |
| VarGraphic |Sträng |
| Xml |Sträng |


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
