---
title: Kopiera data från OData-källor med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från OData-källor till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
ms.openlocfilehash: c8bee6902fb74cb77c34395fd05c1c861b4f630e
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166142"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopiera data från en OData-källan med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-odata-connector.md)
> * [Aktuell version](connector-odata.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en OData-källan. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en OData-källan till alla datalager för mottagare som stöds. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Mer specifikt stöder denna OData-koppling:

- OData version 3.0 och 4.0.
- Kopiera data med någon av följande autentiseringar: **anonym**, **grundläggande**, eller **Windows**.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory-entiteter som är specifika för en OData-koppling.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för en OData-länkad tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen måste anges till **OData**. |Ja |
| url | Rot-URL för OData-tjänsten. |Ja |
| authenticationType | Vilken typ av autentisering som används för att ansluta till OData-källan. Tillåtna värden är **anonym**, **grundläggande**, och **Windows**. OAuth stöds inte. | Ja |
| Användarnamn | Ange **användarnamn** om du använder grundläggande eller Windows-autentisering. | Nej |
| lösenord | Ange **lösenord** för användaren-konto som du angav för **användarnamn**. Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Du kan välja Azure Integration Runtime eller en lokal Integration Runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

**Exempel 1: Använder anonym autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: Använder grundläggande autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**Exempel 3: Med hjälp av Windows-autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

Det här avsnittet innehåller en lista över egenskaper som har stöd för OData-datauppsättningen.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

För att kopiera data från OData-, ange den **typ** egenskapen på datauppsättningen till **ODataResource**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskap måste anges till **ODataResource**. | Ja |
| sökväg | Sökvägen till OData-resursen. | Ja |

**Exempel**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som har stöd för OData-källan.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData som källa

För att kopiera data från OData-, ange den **källa** typ i Kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen för Kopieringsaktiviteten källan måste anges till **RelationalSource**. | Ja |
| DocumentDB | OData-frågealternativ för filtrering av data. Exempel: `"?$select=Name,Description&$top=5"`.<br/><br/>**Obs**: The OData-anslutningsapp kopierar data från den kombinerade URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Mer information finns i [OData-URL komponenter](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nej |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "query": "?$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>Datatypen mappning för OData

När du kopierar data från OData-, används följande mappningar mellan OData-datatyper och Azure Data Factory tillfälliga datatyper. Läs hur Kopieringsaktiviteten mappar källtypen schema och data till mottagaren i [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

| OData-datatypen | Data Factory tillfälliga datatyp |
|:--- |:--- |
| Edm.Binary | Byte] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte] |
| Edm.DateTime | DateTime |
| Edm.Decimal | decimaltal |
| Edm.Double | Double-värde |
| Edm.Single | Enkel |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sträng |
| Edm.Time | Tidsintervall |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData komplexa datatyper (till exempel **objektet**) stöds inte.


## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md##supported-data-stores-and-formats).