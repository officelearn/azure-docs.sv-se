---
title: "Kopiera data från DB2 med Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från DB2 till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 
ms.author: jingwang
ms.openlocfilehash: a96de1814afc7947205a0dc7ed005f7cadff20bc
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiera data från DB2 med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-onprem-db2-connector.md)
> * [Version 2 – förhandsversion](connector-db2.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en DB2-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [DB2-koppling i V1](v1/data-factory-onprem-db2-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från DB2-databas till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt DB2-koppling har stöd för följande IBM DB2-plattformar och versioner med distribuerade relationella Database Architecture (DRDA) SQL åtkomst Manager (SQLAM) version 9, 10 och 11:

* IBM DB2 för z/OS 11,1
* IBM DB2 för z/OS 10.1
* IBM DB2 för i 7.2
* IBM DB2 för i 7.1
* IBM DB2 för LUW 11
* IBM DB2 för LUW 10.5
* IBM DB2 för LUW 10.1

> [!TIP]
> Om du får ett felmeddelande som säger ”paketet som motsvarar en SQL-instruktionen Körningsbegäran inte hittades. SQLSTATE = 51002 SQLCODE =-805 ”, beror nödvändiga paketet inte skapas för normal användare på dessa operativsystem. Följ dessa instruktioner enligt servertyp DB2:
> - DB2 för i (AS400): låta användare skapa samling för användarens inloggning innan du använder kopieringsaktiviteten. Kommandot:`create collection <username>`
> - DB2 för z/OS eller LUW: Använd ett konto för privilegierade - privilegierad användare eller administratör med paketet myndigheter och BIND BINDADD, BEVILJA EXECUTE till offentliga behörigheter - för att köra kopieringsaktiviteten en gång och sedan nödvändiga paketet skapas automatiskt under kopia. Därefter kan du växla tillbaka till normal användare för efterföljande kopia-körs.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda data från en DB2-databas som inte är offentligt tillgänglig, måste du ställer in en Self-hosted integrering Runtime. Mer information om automatisk värdbaserade integration körningar, se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikel. Integration Runtime innehåller en inbyggd DB2-drivrutin, därför behöver du inte installera en drivrutin manuellt när du kopierar data från DB2.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till DB2-koppling.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för DB2 länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Db2** | Ja |
| server |Namnet på DB2-server. |Ja |
| databas |Namnet på DB2-databasen. |Ja |
| schema |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänslig. |Nej |
| AuthenticationType |Typ av autentisering som används för att ansluta till DB2-databasen.<br/>Tillåtna värde är: **grundläggande**. |Ja |
| användarnamn |Ange användarnamn för att ansluta till DB2-databasen. |Ja |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. Markera det här fältet som SecureString. |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
            "database": "<dbname>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av DB2 dataset.

Ange typegenskapen för dataset för att kopiera data från DB2, **RelationalTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i DB2-databasen. | Nej (om ”fråga” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av DB2 källa.

### <a name="db2-as-source"></a>DB2 som källa

Om du vill kopiera data från DB2, anger du källa i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nej (om ”tabellnamn” i datamängden har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Datatypen mappning för DB2

När du kopierar data från DB2, används följande mappningar från DB2-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Typ av DB2-databas | Data factory tillfälliga datatyp |
|:--- |:--- |
| BigInt |Int64 |
| Binär |Byte[] |
| Blob |Byte[] |
| Char |Sträng |
| CLOB |Sträng |
| Date |DateTime |
| DB2DynArray |Sträng |
| DbClob |Sträng |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Dubbel |Dubbel |
| Flyttal |Dubbel |
| Bild |Sträng |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Sträng |
| LongVarGraphic |Sträng |
| numeriskt |Decimal |
| Real |Ogift |
| SmallInt |Int16 |
| Tid |TimeSpan |
| Tidsstämpel |DateTime |
| VarBinary |Byte[] |
| VarChar |Sträng |
| VarGraphic |Sträng |
| Xml |Byte[] |


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).