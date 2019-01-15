---
title: Kopiera data från SAP HANA med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från SAP HANA till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: cdd83c3ff9d34a5e8b7f2c164136ab82f498ffb5
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022979"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiera data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuell version](connector-sap-hana.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en SAP HANA-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från SAP HANA-databas till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SAP HANA-anslutningsappen:

- Kopiera data från en version av SAP HANA-databas.
- Kopiera data från **HANA-informationsmodeller** (till exempel vyer för analys och beräkning) och **rad/kolumn tabeller** med hjälp av SQL-frågor.
- Kopiera data med hjälp av **grundläggande** eller **Windows** autentisering.

> [!NOTE]
> Kopiera data **till** SAP HANA data lagra, Använd allmän ODBC-anslutningsprogram. Se [SAP HANA-mottagare](connector-odbc.md#sap-hana-sink) med information. Observera de länkade tjänsterna för SAP HANA-anslutningsappen och ODBC-anslutningsprogram med annan typ kan därför inte får återanvändas.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här SAP HANA-anslutningsappen måste du:

- Konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.
- Installera SAP HANA ODBC-drivrutinen på den Integration Runtime-datorn. Du kan hämta SAP HANA ODBC-drivrutinen från den [SAP Software Download Center](https://support.sap.com/swdc). Sök med nyckelordet **SAP HANA-klienten för Windows**.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifik för SAP HANA-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SAP HANA-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **SapHana** | Ja |
| server | Namnet på den server som SAP HANA-instans finns. Om servern använder en anpassad port, ange `server:port`. | Ja |
| authenticationType | Typ av autentisering som används för att ansluta till SAP HANA-databas.<br/>Tillåtna värden är: **Grundläggande**, och **Windows** | Ja |
| Användarnamn | Namnet på den användare som har åtkomst till SAP-server. | Ja |
| lösenord | Lösenordet för användaren. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En lokal Integration Runtime krävs enligt [krav](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA-datauppsättningen.

För att kopiera data från SAP HANA, ange typegenskapen på datauppsättningen till **RelationalTable**. Det finns inga typspecifika egenskaper som stöds för SAP HANA-datauppsättningen för skriver RelationalTable.

**Exempel:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA källan.

### <a name="sap-hana-as-source"></a>SAP HANA som källa

För att kopiera data från SAP HANA, ange typ av datakälla i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Anger SQL-frågan som läser data från SAP HANA-instans. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Datatypsmappningen för SAP HANA

När du kopierar data från SAP HANA, används följande mappningar från SAP HANA-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| SAP HANA-datatypen | Data factory tillfälliga datatyp |
|:--- |:--- |
| ALPHANUM | Sträng |
| BIGINT | Int64 |
| BLOB | Byte] |
| BOOLESKT VÄRDE | Byte |
| CLOB | Byte] |
| DATE | DateTime |
| DECIMALTAL | Decimal |
| DOUBLE-VÄRDE | Enkel |
| INT | Int32 |
| NVARCHAR | Sträng |
| VERKLIGA | Enkel |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| TIME | Tidsintervall |
| TIDSSTÄMPEL | DateTime |
| TINYINT | Byte |
| VARCHAR | Sträng |

## <a name="known-limitations"></a>Kända begränsningar

Det finns några kända begränsningar när du kopierar data från SAP HANA:

- NVARCHAR-strängar trunkeras till högst 4 000 Unicode-tecken
- SMALLDECIMAL stöds inte
- VARBINARY stöds inte
- Giltiga datum är mellan 1899-12-30 och 9999-12-31


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
