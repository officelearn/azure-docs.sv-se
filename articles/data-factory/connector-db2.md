---
title: Kopiera data från DB2 med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från DB2 till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: 4bf4c5c8339c8c56d91737fa1ff62f55b9c38696
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019630"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiera data från DB2 med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuell version](connector-db2.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en DB2-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från DB2-databas till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här DB2-anslutningsapp följande IBM DB2-plattformar och versioner med distribuerade Relational Database Architecture (DRDA) SQL Access Manager (SQLAM) version 9, 10 och 11:

* IBM DB2 för z/OS 11.1
* IBM DB2 för z/OS 10.1
* IBM DB2 för i 7.2
* IBM DB2 för i 7.1
* IBM DB2 för LUW 11
* IBM DB2 för LUW 10,5
* IBM DB2 för LUW 10.1

> [!TIP]
> Om du får ett felmeddelande som säger ”den paket som motsvarar en SQL-instruktionsförfrågan inte hittades. SQLSTATE = 51002 SQLCODE =-805 ”, orsaken är ett nödvändiga paket inte skapas för normal användare på sådana OS. Följ de här instruktionerna enligt din DB2 servertyp:
> - DB2 för i (AS400): Låt privilegierad användare skapa samling för användarens inloggning innan du använder Kopieringsaktivitet. Kommandot: `create collection <username>`
> - DB2 för z/OS eller LUW: Använd ett Privilegierade konto - privilegierad användare eller administratör med paketet myndigheter och BIND BINDADD, BEVILJA köra till offentliga behörigheter - för att köra kopieringsaktiviteten en gång och sedan det nödvändiga paketet skapas automatiskt vid kopiering. Därefter kan du gå tillbaka till normal användare för efterföljande kopia-körningar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda data från en DB2-databas som inte är allmänt tillgänglig, måste du konfigurera en lokal Integration Runtime. Mer information om IR-körningar, se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) artikeln. Integreringskörningen innehåller en inbyggd DB2-drivrutin, måste du därför inte installera några drivrutinen manuellt när du kopierar data från DB2.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för DB2-koppling.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för DB2 länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **Db2** | Ja |
| server |Namn på DB2-servern. Du kan ange det portnummer som följer servernamnet avgränsat med kolon t.ex. `server:port`. |Ja |
| databas |Namn på DB2-databas. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till DB2-databasen.<br/>Tillåtna värdet är: **Grundläggande**. |Ja |
| användarnamn |Ange användarnamn för att ansluta till DB2-databas. |Ja |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av DB2-datauppsättningen.

Kopiera data från DB2 genom att ange typegenskapen på datauppsättningen till **RelationalTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i DB2-databas. | Nej (om ”frågan” i aktivitetskälla har angetts) |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av DB2 källa.

### <a name="db2-as-source"></a>DB2 som källa

För att kopiera data från DB2, ange typ av datakälla i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nej (om ”tableName” i datauppsättningen har angetts) |

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

När du kopierar data från DB2, används följande mappningar från DB2-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Typ av DB2-databas | Data factory tillfälliga datatyp |
|:--- |:--- |
| BigInt |Int64 |
| Binär |Byte] |
| Blob |Byte] |
| Char |Sträng |
| CLOB |Sträng |
| Date |DateTime |
| DB2DynArray |Sträng |
| DbClob |Sträng |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double-värde |Double-värde |
| Flyttal |Double-värde |
| Bild |Sträng |
| Integer |Int32 |
| LongVarBinary |Byte] |
| LongVarChar |Sträng |
| LongVarGraphic |Sträng |
| Numerisk |Decimal |
| Real |Enkel |
| SmallInt |Int16 |
| Tid |Tidsintervall |
| Tidsstämpel |DateTime |
| VarBinary |Byte] |
| VarChar |Sträng |
| VarGraphic |Sträng |
| Xml |Byte] |


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
