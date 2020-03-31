---
title: Kopiera data från/till SAP Cloud för kund
description: Lär dig hur du kopierar data från SAP Cloud för kunden till sink-datalager som stöds (eller) från källdatalager som stöds till SAP Cloud för kund med hjälp av Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 0b0352632491dbfc46ed4819653c6cb902eee4ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923725"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopiera data från SAP Cloud for Customer (C4C) med Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från/till SAP Cloud for Customer (C4C). Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

>[!TIP]
>Mer information om ADF:s övergripande support i SCENARIOt för SAP-dataintegrering finns i [SAP-dataintegration med hjälp av Azure Data Factory-faktablad](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, jämförelse och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP Cloud för kundkopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP Cloud för kunden till alla sink-datalager som stöds eller kopiera data från ett källdatalager som stöds till SAP Cloud för kund. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här anslutningen gör det möjligt för Azure Data Factory att kopiera data från/till SAP Cloud för kund, inklusive SAP Cloud for Sales, SAP Cloud for Service och SAP Cloud for Social Engagement-lösningar.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för SAP Cloud för kundkoppling.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för SAP Cloud för kundlänkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapCloudForCustomer**. | Ja |
| url | URL:en för SAP C4C OData-tjänsten. | Ja |
| användarnamn | Ange användarnamnet som ska anslutas till SAP C4C. | Ja |
| password | Ange lösenordet för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Om det inte anges används standardkörningen för Azure Integration. | Nej för källa, Ja för diskho |

>[!IMPORTANT]
>Om du vill kopiera data till SAP Cloud för kunden skapar du uttryckligen [en Azure IR](create-azure-integration-runtime.md#create-azure-ir) med en plats nära ditt SAP-moln för kund och associerar i den länkade tjänsten som följande exempel:

**Exempel:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SAP Cloud för kunddatauppsättning.

Om du vill kopiera data från SAP Cloud för kund anger du egenskapen typ för datauppsättningen till **SapCloudForCustomerResource**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **SapCloudForCustomerResource** |Ja |
| path | Ange sökväg för SAP C4C OData-entiteten. |Ja |

**Exempel:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SAP Cloud för kundkälla.

### <a name="sap-c4c-as-source"></a>SAP C4C som källa

Om du vill kopiera data från SAP Cloud för kunden anger du källtypen i kopieringsaktiviteten till **SapCloudForCustomerSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste ställas in på: **SapCloudForCustomerSource**  | Ja |
| DocumentDB | Ange den anpassade OData-frågan för att läsa data. | Inga |

Exempel på fråga för att hämta data för en viss dag:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C som diskbänk

Om du vill kopiera data till SAP Cloud för kund anger du sink-typen i kopieringsaktiviteten till **SapCloudForCustomerSink**. Följande egenskaper stöds i avsnittet kopiera **aktivitetsmottagare:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste ställas in på: **SapCloudForCustomerSink**  | Ja |
| skriverBeteende | Skrivbeteendet för åtgärden. Kan vara "Infoga", "Uppdatera". | Nej. Standard "Infoga". |
| skriverBatchSize | Batchstorleken för skrivåtgärden. Batchstorleken för att få bästa prestanda kan vara annorlunda för olika bord eller server. | Nej. Standard 10. |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Datatypsmappning för SAP Cloud för kund

Vid kopiering av data från SAP Cloud för kund används följande mappningar från SAP Cloud för kunddatatyper till Azure Data Factory interimsdatatyper. Se [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta mer om hur du kopierar aktivitetsschemat och datatypen till diskhon.

| SAP C4C OData-datatyp | Data fabrik interim datatyp |
|:--- |:--- |
| Edm.Binary (Edm.Binary) | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Singel | Enkel |
| Edm.Guid (edm.guid) | GUID |
| Edm.Int16 (på andra) | Int16 (int16) |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte (edm.sbyte) | Int16 (int16) |
| Edm.String | String |
| Edm.Time (på Edm.Time) | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
