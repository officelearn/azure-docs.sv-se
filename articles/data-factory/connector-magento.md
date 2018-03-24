---
title: Kopiera data från Magento med hjälp av Azure Data Factory (Beta) | Microsoft Docs
description: Lär dig hur du kopierar data från Magento till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
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
ms.openlocfilehash: aafdd0b3ac126bb91e67a350ad634e8073d09d87
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-magento-using-azure-data-factory-beta"></a>Kopiera data från Magento med hjälp av Azure Data Factory (Beta)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från Magento. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Den här anslutningen är för närvarande i betaversion. Du kan prova och ge oss feedback. Använd den inte i produktionsmiljöer.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Magento till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar, måste du därför inte att manuellt installera en drivrutin med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Magento connector.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Magento länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Magento** | Ja |
| värd | URL till Magento-instans. (det vill säga 192.168.222.110/magento3)  | Ja |
| accessToken | Åtkomst-token från Magento. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är true.  | Nej |
| useHostVerification | Anger om värdnamnet i servercertifikatet så att den matchar värdnamnet för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |
| usePeerVerification | Anger om att verifiera identiteten för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |

**Exempel:**

```json
{
    "name": "MagentoLinkedService",
    "properties": {
        "type": "Magento",
        "typeProperties": {
            "host" : "192.168.222.110/magento3",
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Magento dataset.

Ange typegenskapen för dataset för att kopiera data från Magento, **MagentoObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

**Exempel**

```json
{
    "name": "MagentoDataset",
    "properties": {
        "type": "MagentoObject",
        "linkedServiceName": {
            "referenceName": "<Magento linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Magento källa.

### <a name="magentosource-as-source"></a>MagentoSource som källa

Om du vill kopiera data från Magento, anger du källa i kopieringsaktiviteten till **MagentoSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **MagentoSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Customers"`. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromMagento",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Magento input dataset name>",
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
                "type": "MagentoSource",
                "query": "SELECT * FROM Customers where Id > XXX"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
