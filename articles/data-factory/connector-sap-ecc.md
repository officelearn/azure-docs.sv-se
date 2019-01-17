---
title: Kopiera data från SAP ECC med Azure Data Factory | Microsoft Docs
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
ms.date: 04/26/2018
ms.author: jingwang
ms.openlocfilehash: d6a6d9b352db61d98e85c840a3ebc5cb6a832a3f
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352469"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Kopiera data från SAP ECC med Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från SAP ECC (SAP Enterprise Central komponent). Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från SAP ECC till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SAP ECC-anslutningen:

- Kopiera data från SAP ECC på SAP NetWeaver version 7.0 och senare. 
- Kopiera data från alla objekt som visas av SAP ECC OData-tjänster (t.ex. SAP tabell/vyer, BAPI, Data Extraktorer osv.) eller data/idoc: er skickas till SAP-PI som tas emot som OData via relativt kort.
- Kopiera data med hjälp av grundläggande autentisering.

## <a name="prerequisites"></a>Förutsättningar

I allmänhet exponerar SAP ECC entiteter via OData-tjänster via SAP-Gateway. Om du vill använda denna SAP ECC-anslutning måste du:

- **Konfigurera SAP-Gateway**. SAP-gatewayen har redan installerats för servrar med SAP NetWeaver-version som är högre än 7.4. Annars kan du behöva installera inbäddad Gateway eller Gateway hub innan du exponerar SAP ECC data via OData-tjänster. Lär dig hur du ställer in SAP-Gateway från [installationsguide](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktivera och konfigurera SAP OData-tjänst**. Du kan aktivera OData-tjänster via TCODE SICF på några sekunder. Du kan också konfigurera vilka objekt som behöver exponeras. Här är ett exempel [stegvisa anvisningar](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med en Kopieringsaktivitet med hjälp av .NET SDK, Python SDK, Azure PowerShell, REST API eller Azure Resource Manager-mall. Se [kopiera aktivitet självstudien](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifik för SAP ECC-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SAP ECC länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **SapEcc** | Ja |
| url | Url till SAP ECC OData-tjänsten. | Ja |
| användarnamn | Användarnamnet som används för att ansluta till SAP ECC. | Nej |
| lösenord | Används för att ansluta till SAP ECC lösenordet i klartext. | Nej |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP ECC-datauppsättningen.

Kopiera data från SAP ECC genom att ange typegenskapen på datauppsättningen till **SapEccResource**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| sökväg | Sökväg för SAP ECC OData-entiteten. | Ja |

**Exempel**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP ECC-källa.

### <a name="sap-ecc-as-source"></a>SAP ECC som källa

För att kopiera data från SAP ECC, ange typ av datakälla i kopieringsaktiviteten till **SapEccSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **SapEccSource** | Ja |
| DocumentDB | OData-frågealternativ att filtrera data. Exempel ”: $select = namn, beskrivning och $top = 10”.<br/><br/>SAP ECC connector kopierar data från den kombinerade URL: (URL: en som anges i den länkade tjänsten) / (sökväg som anges i datauppsättningen)? (fråga som anges i kopieringskälla för aktiviteten). Referera till [OData-URL komponenter](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nej |

**Exempel:**

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

## <a name="data-type-mapping-for-sap-ecc"></a>Datatypsmappningen för SAP ECC

När du kopierar data från SAP ECC används följande mappningar från OData-datatyper för SAP ECC-data till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| OData-datatypen | Data factory tillfälliga datatyp |
|:--- |:--- |:--- |
| Edm.Binary | Sträng |
| Edm.Boolean | Bool |
| Edm.Byte | Sträng |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double-värde |
| Edm.Single | Enkel |
| Edm.Guid | Sträng |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sträng |
| Edm.Time | Tidsintervall |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Komplexa datatyper stöds inte nu.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
