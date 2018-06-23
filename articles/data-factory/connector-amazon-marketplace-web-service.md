---
title: Kopiera data från Amazon Marketplace-webbtjänsten med hjälp av Azure Data Factory (förhandsversion) | Microsoft Docs
description: Lär dig hur du kopierar data från Amazon Marketplace-webbtjänsten till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: add0f9185ac94a8b673e0c05de9de919286ffa3a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337956"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory-preview"></a>Kopiera data från Amazon Marketplace-webbtjänsten med hjälp av Azure Data Factory (förhandsgranskning)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från Amazon Marketplace-webbtjänsten. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Den här anslutningen är för närvarande under förhandsgranskning. Du kan prova och ge oss feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Amazon Marketplace Web Service till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar, måste du därför inte att manuellt installera en drivrutin med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Amazon Marketplace Web Service connector.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för webbtjänsten för Amazon Marketplace länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AmazonMWS** | Ja |
| slutpunkt | Slutpunkten för Amazon MWS-servern (det vill säga mws.amazonservices.com)  | Ja |
| marketplaceID | Marketplace Amazon ID du vill hämta data från. Om du vill hämta data från flera Marketplace-ID: N, avgränsade med kommatecken (`,`). (det vill säga A2EUQ1WTGCTBG2)  | Ja |
| sellerID | Amazon säljare-ID.  | Ja |
| mwsAuthToken | Amazon MWS autentiseringstoken. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| accessKeyId | Åtkomst key-ID som används för åtkomst till data.  | Ja |
| secretKey | Den hemliga nyckeln som används för att komma åt data. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är true.  | Nej |
| useHostVerification | Anger om värdnamnet i servercertifikatet så att den matchar värdnamnet för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |
| usePeerVerification | Anger om att verifiera identiteten för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |

**Exempel:**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av webbtjänsten för Amazon Marketplace dataset.

Ange typegenskapen för dataset för att kopiera data från Amazon Marketplace-webbtjänsten, **AmazonMWSObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

**Exempel**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av webbtjänsten för Amazon Marketplace-källa.

### <a name="amazonmwssource-as-source"></a>AmazonMWSSource som källa

Om du vill kopiera data från Amazon Marketplace-webbtjänst som källtypen i kopieringsaktiviteten till **AmazonMWSSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **AmazonMWSSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
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
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
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
