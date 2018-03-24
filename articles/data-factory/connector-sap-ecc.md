---
title: Kopiera data från SAP ECC med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från SAP ECC till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: jingwang
ms.openlocfilehash: abd3ef00148795f4b3e79e2ed64dd35f56946455
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Kopiera data från SAP ECC med Azure Data Factory

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från SAP ECC (SAP Enterprise Central komponent). Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från SAP ECC till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SAP ECC-anslutningen:

- Kopiera data från SAP ECC på SAP NetWeaver version 7.0 och senare. 
- Kopiera data från alla objekt som exponeras av SAP ECC OData-tjänster (t.ex. SAP tabellvyer, BAPI, Data juicepressar osv.) eller data/idoc: er skickas till SAP PI som kan tas emot som OData via relativt kort.
- Kopierar data med hjälp av grundläggande autentisering.

## <a name="prerequisites"></a>Förutsättningar

I allmänhet visar SAP ECC enheter via OData-tjänster genom SAP-Gateway. Om du vill använda den här anslutningen SAP ECC, måste du:

- **Konfigurera SAP-Gateway**. För servrar med SAP NetWeaver högre version än 7.4 finns redan en SAP-Gateway. I annat fall måste du installera inbäddad Gateway eller Gateway hubb innan exponera SAP ECC data via OData-tjänster. Lär dig hur du ställer in SAP-Gateway från [installationsguiden](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Aktivera och konfigurera SAP OData-tjänsten**. Du kan aktivera OData-tjänster genom TCODE SICF i sekunder. Du kan också konfigurera vilka objekt som behöver göras tillgängliga. Här är ett exempel [stegvisa anvisningar](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till SAP ECC-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för SAP ECC länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **SapEcc** | Ja |
| url | Url till SAP ECC OData-tjänsten. | Ja |
| användarnamn | Användarnamnet som används för att ansluta till SAP ECC. | Nej |
| lösenord | Används för att ansluta till SAP ECC lösenordet i klartext. | Nej |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP ECC dataset.

Ange typegenskapen för dataset för att kopiera data från SAP ECC, **SapEccResource**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| sökväg | Sökvägen till SAP ECC OData-entiteten. | Ja |

**Exempel**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP ECC-källa.

### <a name="sap-ecc-as-source"></a>SAP ECC som källa

Om du vill kopiera data från SAP ECC anger källa i kopieringsaktiviteten till **SapEccSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **SapEccSource** | Ja |
| DocumentDB | OData-frågealternativ att filtrera data. Exempel ”: $select = namn, beskrivning och $top = 10”.<br/><br/>SAP ECC connector kopierar data från den kombinerade URL: (url som anges i den länkade tjänsten) / (sökväg har angetts i dataset)? (fråga som anges i aktiviteten kopieringskälla). Referera till [OData-URL komponenter](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Ja |

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

När du kopierar data från SAP ECC används följande mappningar från OData-datatyper för SAP ECC data till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| OData-datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |:--- |
| Edm.Binary | Sträng |
| Edm.Boolean | Booleskt |
| Edm.Byte | Sträng |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Dubbel |
| Edm.Single | Ogift |
| Edm.Guid | Sträng |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sträng |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Komplexa datatyper stöds inte nu.

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
