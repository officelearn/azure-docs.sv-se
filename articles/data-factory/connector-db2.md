---
title: Kopiera data från DB2 med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från DB2 till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: d89b4018da7c50127fc38219c6cb799a89509258
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092134"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiera data från DB2 med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuell version](connector-db2.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en DB2-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här DB2 Database Connector stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från DB2-databasen till alla mottagar data lager som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

I synnerhet stöder denna DB2-anslutning följande IBM DB2-plattformar och versioner med Distributed Relations databas arkitektur (SQLAM), version 9, 10 och 11:

* IBM DB2 för z/OS 12,1
* IBM DB2 för z/OS 11,1
* IBM DB2 för z/OS 10,1
* IBM DB2 för i 7,3
* IBM DB2 för i 7,2
* IBM DB2 för i 7,1
* IBM DB2 för LUW 11
* IBM DB2 för LUW 10,5
* IBM DB2 för LUW 10,1

> [!TIP]
> Om du får ett fel meddelande om att det inte gick att hitta paketet som motsvarar en körnings förfrågan för SQL-instruktionen. SQLSTATE = 51002 SQLCODE =-805 ", orsaken är att ett nödvändigt paket inte skapas för normal användare på ett sådant operativ system. Följ de här instruktionerna enligt typen av DB2-Server:
> - DB2 för i (AS400): låt Power User skapa samling för inloggnings användaren innan du använder kopierings aktiviteten. Kommandoprompt`create collection <username>`
> - DB2 för z/OS eller LUW: Använd ett konto med hög behörighet – privilegierade användare eller administratörer med paket utfärdare och BIND, BINDADD, bevilja körning till offentliga behörigheter – för att köra kopierings aktiviteten en gång, skapas det paket som krävs automatiskt under kopieringen. Sedan kan du växla tillbaka till normal användare för din efterföljande kopierings körning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime tillhandahåller en inbyggd DB2-drivrutin, och du behöver därför inte installera någon driv rutin manuellt när du kopierar data från DB2.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för DB2 Connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för DB2-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till: **Db2** | Ja |
| server |Namnet på DB2-servern. Du kan ange port numret som följer Server namnet avgränsat med kolon, t `server:port`. ex. |Ja |
| database |Namnet på DB2-databasen. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till DB2-databasen.<br/>Tillåtet värde är: **Basic**. |Ja |
| username |Ange användar namnet för att ansluta till DB2-databasen. |Ja |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Läs mer från avsnittet [krav](#prerequisites) . Om den inte anges används standard Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av DB2-datauppsättningen.

Följande egenskaper stöds för att kopiera data från DB2:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till: **Db2Table** | Ja |
| schema | Schemats namn. |Nej (om ”query” i aktivitetskälla har angetts)  |
| table | Namnet på tabellen. |Nej (om ”query” i aktivitetskälla har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och`table` för nya arbets belastningar. | Nej (om ”query” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du använder typ `RelationalTable` av data uppsättning, stöds den fortfarande som den är, medan du föreslås att använda den nya som går framåt.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av DB2-källan.

### <a name="db2-as-source"></a>DB2 som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från DB2:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för kopierings aktivitets källan måste anges till: **Db2Source** | Ja |
| query | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nej (om ”tableName” i datauppsättningen har angetts) |

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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder typ `RelationalSource` av källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen framåt.

## <a name="data-type-mapping-for-db2"></a>Data typs mappning för DB2

När du kopierar data från DB2 används följande mappningar från DB2-datatyper för att Azure Data Factory interimistiska data typer. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Typ av DB2-databas | Data factory tillfälliga datatyp |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |Sträng |
| CLOB |Sträng |
| Date |Datetime |
| DB2DynArray |Sträng |
| DbClob |Sträng |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |Sträng |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Sträng |
| LongVarGraphic |Sträng |
| Numeric |Decimal |
| Verklig |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |Sträng |
| VarGraphic |Sträng |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
