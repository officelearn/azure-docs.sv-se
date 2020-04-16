---
title: Kopiera data från SAP ECC
description: Lär dig hur du kopierar data från SAP ECC till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413805"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopiera data från SAP ECC med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från SAP Enterprise Central Component (ECC). Mer information finns i [Kopiera aktivitetsöversikt](copy-activity-overview.md).

>[!TIP]
>Mer information om ADF:s övergripande support i SCENARIOt för SAP-dataintegrering finns i [SAP-dataintegration med hjälp av Azure Data Factory-faktablad](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, jämförelse och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP ECC-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP ECC till alla sink-datalager som stöds. En lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här SAP ECC-anslutningen stöder:

- Kopiera data från SAP ECC på SAP NetWeaver version 7.0 och senare.
- Kopiera data från objekt som exponeras av SAP ECC OData-tjänster, till exempel:

  - SAP-tabeller eller -vyer.
  - Business Application Programming Interface [BAPI]-objekt.
  - Datautsugare.
  - Data eller mellanliggande dokument (IDOCs) som skickas till SAP Process Integration (PI) som kan tas emot som OData via relativa kort.

- Kopiera data med hjälp av grundläggande autentisering.

>[!TIP]
>Om du vill kopiera data från SAP ECC via en SAP-tabell eller -vy använder du [SAP-tabellkopplingen,](connector-sap-table.md) som är snabbare och mer skalbar.

## <a name="prerequisites"></a>Krav

I allmänhet exponerar SAP ECC enheter via OData-tjänster via SAP Gateway. Om du vill använda den här SAP ECC-anslutningen måste du:

- **Konfigurera SAP Gateway**. För servrar med SAP NetWeaver-versioner senare än 7.4 är SAP Gateway redan installerat. För tidigare versioner måste du installera den inbäddade SAP Gateway eller SAP Gateway-hubbsystemet innan du exponerar SAP ECC-data via OData-tjänster. Om du vill konfigurera SAP Gateway finns i [installationsguiden](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktivera och konfigurera SAP OData-tjänsten**. Du kan aktivera OData-tjänsten via TCODE SICF på några sekunder. Du kan också konfigurera vilka objekt som behöver exponeras. Mer information finns i [steg-för-steg-vägledningen](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteterna som är specifika för SAP ECC-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den SAP ECC-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | Egenskapen `type` måste anges `SapEcc`till . | Ja |
| `url` | URL:en för TJÄNSTEN SAP ECC OData. | Ja |
| `username` | Användarnamnet som används för att ansluta till SAP ECC. | Inga |
| `password` | Det lösenord med klartext som används för att ansluta till SAP ECC. | Inga |
| `connectVia` | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om du inte anger en körning används standardkörningen för Azure-integrering. | Inga |

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

En fullständig lista över de avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [Datauppsättningar](concepts-datasets-linked-services.md). I följande avsnitt finns en lista över de egenskaper som stöds av SAP ECC-datauppsättningen.

Om du vill kopiera data `type` från SAP ECC `SapEccResource`anger du egenskapen för datauppsättningen till .

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

En fullständig lista över de avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). I följande avsnitt finns en lista över de egenskaper som stöds av SAP ECC-källan.

### <a name="sap-ecc-as-a-source"></a>SAP ECC som källa

Om du vill kopiera data `type` från SAP `source` ECC anger `SapEccSource`du egenskapen i avsnittet i kopieringsaktiviteten till .

Följande egenskaper stöds i kopieringsaktivitetens `source` avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| `type` | Egenskapen `type` för kopieringsaktivitetens `source` avsnitt `SapEccSource`måste anges till . | Ja |
| `query` | OData-frågealternativen för att filtrera data. Ett exempel:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC-anslutningen kopierar data från den kombinerade URL:en:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Mer information finns i [OData URL-komponenter](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Inga |

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

## <a name="data-type-mappings-for-sap-ecc"></a>Datatypsmappningar för SAP ECC

När du kopierar data från SAP ECC används följande mappningar från OData-datatyper för SAP ECC-data till Azure Data Factory interimsdatatyper. Mer information om hur kopieringsaktiviteten mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

| Datatyp för OData | Data Factory interimdatatyp |
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
> Komplexa datatyper stöds för närvarande inte.

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

En lista över de datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [i Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
