---
title: "Kopiera data från Teradata med hjälp av Azure Data Factory | Microsoft Docs"
description: "Läs mer om Teradata koppling av Data Factory-tjänsten som du kan använda data från Teradata-databasen för datalager som stöds av Data Factory som sänkor."
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
ms.openlocfilehash: 343facadfec217adaef9a05426e7ae914f4cfd38
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Kopiera data från Teradata med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-onprem-teradata-connector.md)
> * [Version 2 – förhandsversion](connector-teradata.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en Teradata-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Teradata-anslutningen i V1](v1/data-factory-onprem-teradata-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Teradata-databasen till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Teradata-anslutningen:

- Teradata **version 12 och högre**.
- Kopiera data med hjälp av **grundläggande** eller **Windows** autentisering.

## <a name="prerequisites"></a>Krav

Om du vill använda denna Teradata-anslutning måste du:

- Ställ in en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikeln för information.
- Installera den [.NET Data Provider för Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) version 14 eller senare på Integration Runtime-datorn.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Teradata-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Teradata länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Teradata** | Ja |
| server | Namnet på Teradata-server. | Ja |
| AuthenticationType | Typ av autentisering som används för att ansluta till Teradata-databasen.<br/>Tillåtna värden är: **grundläggande**, och **Windows**. | Ja |
| användarnamn | Ange användarnamn för att ansluta till Teradata-databasen. | Ja |
| lösenord | Ange lösenordet för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString. | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En Self-hosted integrering Runtime krävs enligt [krav](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata dataset.

Ange typegenskapen för dataset för att kopiera data från Teradata, **RelationalTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i Teradata-databasen. | Nej (om ”fråga” i aktivitetskälla har angetts) |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-källa.

### <a name="teradata-as-source"></a>Teradata som källa

Om du vill kopiera data från Teradata, anger du källa i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tabellnamn” i datamängden har angetts) |

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

När du kopierar data från Teradata, används följande mappningar från Teradata-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Datatypen för Teradata | Data factory tillfälliga datatyp |
|:--- |:--- |
| BigInt |Int64 |
| Blob |byte] |
| Mottagna byte |byte] |
| ByteInt |Int16 |
| Char |Sträng |
| CLOB |Sträng |
| Date |Datum och tid |
| Decimal |Decimal |
| dubbla |dubbla |
| Bild |Sträng |
| Integer |Int32 |
| Intervall dag |TimeSpan |
| Intervall dag till timme |TimeSpan |
| Intervall dag till minut |TimeSpan |
| Intervall dag till andra |TimeSpan |
| Intervall timme |TimeSpan |
| Intervall timme, minut |TimeSpan |
| Intervall timme till andra |TimeSpan |
| Intervall minut |TimeSpan |
| Intervall minut till andra |TimeSpan |
| Intervall för månad |Sträng |
| Intervall för andra |TimeSpan |
| Intervall år |Sträng |
| Intervall år, månad |Sträng |
| Tal |dubbla |
| Period(Date) |Sträng |
| Period(Time) |Sträng |
| Tid (Time med tidszon) |Sträng |
| Period(timestamp) |Sträng |
| Tid (tidsstämpel med tidszon) |Sträng |
| SmallInt |Int16 |
| Tid |TimeSpan |
| Tid med tidszon |Sträng |
| tidsstämpel |Datum och tid |
| Tidsstämpel med tidszon |DateTimeOffset |
| VarByte |byte] |
| VarChar |Sträng |
| VarGraphic |Sträng |
| XML |Sträng |


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).