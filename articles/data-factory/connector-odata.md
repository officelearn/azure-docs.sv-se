---
title: Kopiera data från OData-källor med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från OData-datakällor till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: d1de8baf725233bee30a14eca770e4f04e7a70b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-odata-source-using-azure-data-factory"></a>Kopiera data från OData-datakälla med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-odata-connector.md)
> * [Version 2 – förhandsversion](connector-odata.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en OData-källa. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [OData-anslutningen i V1](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från OData-källan till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder denna OData-koppling:

- OData **version 3.0 och 4.0**.
- Kopiera data med hjälp av följande autentiseringar: **anonym**, **grundläggande**, och **Windows**.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till OData-anslutningen.

## <a name="linked-service-properties"></a>Den länkade tjänstens egenskaper

Följande egenskaper stöds för länkad OData-tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **OData** |Ja |
| url | Rot-URL för OData-tjänsten. |Ja |
| AuthenticationType | Typ av autentisering som används för att ansluta till OData-källan.<br/>Tillåtna värden är: **anonym**, **grundläggande**, och **Windows**. Observera OAuth inte stöds. | Ja |
| userName | Ange användarnamnet om du använder grundläggande eller Windows-autentisering. | Nej |
| lösenord | Ange lösenordet för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel 1: använder anonym autentisering**

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

**Exempel 2: använder grundläggande autentisering**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
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

**Exempel 3: med Windows-autentisering**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av OData-datauppsättningen.

Ange egenskapen type för datauppsättningen till för att kopiera data från OData **ODataResource**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **ODataResource** | Ja |
| sökväg | Sökvägen till OData-resurs. | Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av OData-källan.

### <a name="odata-as-source"></a>OData som källa

Om du vill kopiera data från OData anger källa i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | OData-frågealternativ att filtrera data. Exempel ”:? $select = namn, beskrivning och $top = 5”.<br/><br/>Observera vid senaste OData-koppling kopierar data från den kombinerade URL: `[url specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Referera till [OData-URL komponenter](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nej |

**Exempel:**

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

## <a name="data-type-mapping-for-odata"></a>Mappning för OData-datatyp

När du kopierar data från OData, används följande mappningar från OData-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| OData-datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Booleskt |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Dubbel |
| Edm.Single | Ogift |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Sträng |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!Note]
> OData komplexa datatyper (till exempel objekt) stöds inte.


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).