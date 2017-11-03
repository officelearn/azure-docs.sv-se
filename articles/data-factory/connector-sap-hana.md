---
title: "Kopiera data från SAP HANA med Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från SAP HANA till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.openlocfilehash: 2e66edf63b4bdac1aacfedb64ca1edf207e46a84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiera data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-sap-hana-connector.md)
> * [Version 2 – förhandsversion](connector-sap-hana.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en SAP HANA-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [SAP HANA-anslutningen i V1](v1/data-factory-sap-hana-connector.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data från SAP HANA-databas till alla stöds sink-datalagret. En lista över datakällor som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SAP HANA-anslutningen:

- Kopiera data från en version av SAP HANA-databas.
- Kopiera data från **HANA informationsmodeller** (till exempel analys- och vyer) och **raden/kolumnen tabeller** med hjälp av SQL-frågor.
- Kopiera data med hjälp av **grundläggande** eller **Windows** autentisering.

> [!NOTE]
> Att kopiera data **till** SAP HANA data lagras, använder allmän ODBC-anslutningen. Se [SAP HANA sink](connector-odbc.md#sap-hana-sink) med information. Observera de länkade tjänsterna för SAP HANA-koppling och ODBC-anslutningen med annan typ, kan vilket inte återanvändas.

## <a name="prerequisites"></a>Krav

Om du vill använda denna SAP HANA-anslutning måste du:

- Ställ in en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikeln för information.
- Installera SAP HANA ODBC-drivrutinen på Integration Runtime-datorn. Du kan hämta SAP HANA ODBC-drivrutinen från den [SAP Software Download Center](https://support.sap.com/swdc). Sökning med nyckelordet **SAP HANA-klienten för Windows**.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till SAP HANA-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för SAP HANA länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **SapHana** | Ja |
| server | Namnet på den server som SAP HANA-instansen finns. Om servern använder en anpassad port, ange `server:port`. | Ja |
| AuthenticationType | Typ av autentisering som används för att ansluta till SAP HANA-databas.<br/>Tillåtna värden är: **grundläggande**, och **Windows** | Ja |
| Användarnamn | Namnet på den användare som har åtkomst till SAP-server. | Ja |
| lösenord | Lösenord för användaren. Markera det här fältet som en SecureString. | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En Self-hosted integrering Runtime krävs enligt [krav](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA dataset.

Ange egenskapen type för datauppsättningen till för att kopiera data från SAP HANA **RelationalTable**. När det finns inga typspecifika egenskaper som stöds för SAP HANA-dataset av skriver RelationalTable.

**Exempel:**

```json
{
    "name": "SAPHANADataset",
    "properties":
    {
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA-källa.

### <a name="sap-hana-as-source"></a>SAP HANA som källa

Om du vill kopiera data från SAP HANA anger källa i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
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

När du kopierar data från SAP HANA, används följande mappningar från SAP HANA-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| SAP HANA-datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| ALPHANUM | Sträng |
| BIGINT | Int64 |
| BLOB | byte] |
| BOOLESKT VÄRDE | Mottagna byte |
| CLOB | byte] |
| DATUM | Datum och tid |
| DECIMAL | Decimal |
| DUBBEL | Enskild |
| INT | Int32 |
| NVARCHAR | Sträng |
| VERKLIG | Enskild |
| SECONDDATE | Datum och tid |
| SMALLINT | Int16 |
| TID | TimeSpan |
| TIDSSTÄMPEL | Datum och tid |
| TINYINT | Mottagna byte |
| VARCHAR | Sträng |

## <a name="known-limitations"></a>Kända begränsningar

Det finns några kända begränsningar när du kopierar data från SAP HANA:

- NVARCHAR strängar trunkeras till högst 4 000 Unicode-tecken
- SMALLDECIMAL stöds inte
- VARBINARY stöds inte
- Giltiga datum är mellan 1899, 12, 30 och 9999-12-31


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).