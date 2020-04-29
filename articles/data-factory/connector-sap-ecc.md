---
title: Kopiera data från SAP ECC
description: Lär dig hur du kopierar data från SAP ECC till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81413805"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopiera data från SAP ECC med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från SAP Enterprise Central Component (ECC). Mer information finns i [Översikt över kopierings aktivitet](copy-activity-overview.md).

>[!TIP]
>Om du vill lära dig mer om ADF: s övergripande support i SAP data integrations scenario, se [SAP data integration med Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, comparsion och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Denna SAP ECC-anslutning stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP ECC till alla mottagar data lager som stöds. En lista över data lager som stöds som källor eller mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder denna SAP ECC-anslutning:

- Kopiera data från SAP ECC på SAP NetWeaver version 7,0 och senare.
- Kopiera data från alla objekt som exponeras av SAP ECC OData-tjänster, till exempel:

  - SAP-tabeller eller-vyer.
  - BAPI-objekt (Business Application Programming Interface).
  - Data utdrag.
  - Data eller mellanliggande dokument (IDOCs) som skickats till SAP process integration (PI) som kan tas emot som OData via relativa kort.

- Kopiera data med hjälp av grundläggande autentisering.

>[!TIP]
>Om du vill kopiera data från SAP ECC via en SAP-tabell eller-vy använder du [SAP Table](connector-sap-table.md) Connector som är snabbare och mer skalbar.

## <a name="prerequisites"></a>Krav

I allmänhet exponerar SAP ECC entiteter via OData-tjänster via SAP Gateway. Om du vill använda denna SAP ECC-anslutning måste du:

- **Konfigurera SAP Gateway**. För servrar med SAP NetWeaver-versioner senare än 7,4 är SAP Gateway redan installerat. För tidigare versioner måste du installera den inbäddade SAP-gatewayen eller SAP Gateway Hub-systemet innan du exponerar SAP ECC-data via OData-tjänster. Information om hur du konfigurerar SAP Gateway finns i [installations guiden](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)för.

- **Aktivera och konfigurera SAP OData-tjänsten**. Du kan aktivera OData-tjänsten via TCODE SICF på några sekunder. Du kan också konfigurera vilka objekt som ska visas. Mer information finns i [steg-för-steg-anvisningar](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för SAP ECC-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade SAP ECC-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | `type` Egenskapen måste anges till `SapEcc`. | Ja |
| `url` | URL: en för SAP ECC OData-tjänsten. | Ja |
| `username` | Det användar namn som används för att ansluta till SAP ECC. | Nej |
| `password` | Lösen ordet för klartext används för att ansluta till SAP ECC. | Nej |
| `connectVia` | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om du inte anger någon körnings miljö används standard körningen av Azure integration. | Nej |

### <a name="example"></a>Exempel

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över de avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md). Följande avsnitt innehåller en lista över de egenskaper som stöds av SAP ECC-datauppsättningen.

Om du vill kopiera data från SAP ECC anger `type` du egenskapen för data uppsättningen `SapEccResource`till.

Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `path` | Sökväg till SAP ECC OData-entiteten. | Ja |

### <a name="example"></a>Exempel

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över de avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). Följande avsnitt innehåller en lista över de egenskaper som stöds av SAP ECC-källan.

### <a name="sap-ecc-as-a-source"></a>SAP ECC som källa

Om du vill kopiera data från SAP ECC ställer `type` du in egenskapen `source` i avsnittet i kopierings aktiviteten `SapEccSource`till.

Följande egenskaper stöds i `source` avsnittet kopierings aktivitet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | `type` Egenskapen för kopierings aktivitetens `source` avsnitt måste anges till `SapEccSource`. | Ja |
| `query` | OData-frågealternativen för att filtrera data. Ett exempel:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC-anslutaren kopierar data från den kombinerade URL: en:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Mer information finns i [OData URL-komponenter](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nej |

### <a name="example"></a>Exempel

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Data typs mappningar för SAP ECC

När du kopierar data från SAP ECC används följande mappningar från OData-datatyper för SAP ECC-data för att Azure Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och data typ till mottagaren finns i schema- [och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| OData-datatyp | Data Factory data typen Interim |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Komplexa data typer stöds inte för närvarande.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
