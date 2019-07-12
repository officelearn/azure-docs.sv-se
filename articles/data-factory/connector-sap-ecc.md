---
title: Kopiera data från SAP ECC med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från SAP ECC till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827775"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopiera data från SAP ECC med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från SAP Enterprise Central komponent (ECC). Mer information finns i [översikt över Kopieringsaktivitet](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från SAP ECC till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SAP ECC-anslutningen:

- Kopiera data från SAP ECC på SAP NetWeaver version 7.0 och senare.
- Kopiera data från alla objekt som visas av SAP ECC OData-tjänster, till exempel:

  - SAP-tabeller eller vyer.
  - Business Application Programming Interface [BAPI]-objekt.
  - Data extraktorer.
  - Data eller mellanliggande dokument (idoc: er) som skickas till SAP processen Integration (PI) och som kan tas emot som OData via relativt kort.

- Kopiera data med hjälp av grundläggande autentisering.

>[!TIP]
>Kopiera data från SAP ECC via en SAP-tabellen eller vyn genom att använda den [SAP tabell](connector-sap-table.md) anslutningstjänsten, som är snabbare och mer skalbart.

## <a name="prerequisites"></a>Förutsättningar

I allmänhet exponerar SAP ECC entiteter via OData-tjänster via SAP-Gateway. Om du vill använda denna SAP ECC-anslutning måste du:

- **Konfigurera SAP-Gateway**. SAP-Gateway har redan installerats för servrar med SAP NetWeaver senare än 7.4. För tidigare versioner måste du installera den inbäddade SAP-Gateway eller SAP-Gateway hub systemet innan du exponerar SAP ECC data via OData-tjänster. Om du vill konfigurera SAP-Gateway finns i den [installationsguide](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktivera och konfigurera SAP OData-tjänsten**. Du kan aktivera OData-tjänsten via TCODE SICF på några sekunder. Du kan också konfigurera vilka objekt som behöver exponeras. Mer information finns i den [stegvisa anvisningar](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter specifika till SAP ECC-anslutningstjänsten.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SAP ECC länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | Den `type` egenskapen måste anges till `SapEcc`. | Ja |
| `url` | URL till SAP ECC OData-tjänsten. | Ja |
| `username` | Användarnamnet som används för att ansluta till SAP ECC. | Nej |
| `password` | Används för att ansluta till SAP ECC lösenordet i klartext. | Nej |
| `connectVia` | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda en lokal integration runtime eller Azure integration runtime (om ditt datalager är offentligt tillgänglig). Om du inte anger en runtime, `connectVia` använder Azure integration runtime i standardversionen. | Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar](concepts-datasets-linked-services.md). Följande avsnitt innehåller en lista över de egenskaper som stöds av SAP ECC-datauppsättningen.

Om du vill kopiera data från SAP ECC, ange den `type` egenskapen på datauppsättningen till `SapEccResource`.

Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `path` | Sökväg för SAP ECC OData-entiteten. | Ja |

### <a name="example"></a>Exempel

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). Följande avsnitt innehåller en lista över de egenskaper som stöds av SAP ECC-källa.

### <a name="sap-ecc-as-a-source"></a>SAP ECC som en källa

Om du vill kopiera data från SAP ECC, ange den `type` -egenskapen i den `source` delen av kopieringsaktiviteten till `SapEccSource`.

Följande egenskaper stöds i kopieringsaktiviteten `source` avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | Den `type` egenskapen för kopieringsaktiviteten `source` avsnitt måste anges till `SapEccSource`. | Ja |
| `query` | OData-frågealternativ att filtrera data. Exempel:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC-koppling kopierar data från den kombinerade URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Mer information finns i [OData-URL komponenter](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nej |

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

## <a name="data-type-mappings-for-sap-ecc"></a>Mappningar av datatyper för SAP ECC

När du kopierar data från SAP ECC, används följande mappningar från OData-datatyper för SAP ECC-data till Azure Data Factory tillfälliga-datatyper. Läs hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren i [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

| OData-datatypen | Data Factory tillfälliga datatyp |
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
> Komplexa datatyper stöds inte för närvarande.

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
