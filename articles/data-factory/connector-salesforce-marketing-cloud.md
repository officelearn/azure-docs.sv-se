---
title: Kopiera data från Salesforce marknadsföring molnet med Azure Data Factory (Beta) | Microsoft Docs
description: Lär dig hur du kopierar data från Salesforce marknadsföring molnet till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
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
ms.date: 04/30/2018
ms.author: jingwang
ms.openlocfilehash: 59ad27a8609b4491ba131649b574d2a3bf55309b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34617937"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory-beta"></a>Kopiera data från Salesforce marknadsföring molnet med Azure Data Factory (Beta)

Den här artikeln beskriver hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från Salesforce marknadsföring molnet. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Den här anslutningen är för närvarande i betaversion. Du kan prova och ge oss feedback. Använd den inte i produktionsmiljöer.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Salesforce marknadsföring molnet till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar, måste du därför inte att manuellt installera en drivrutin med den här anslutningen.

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Salesforce marknadsföring molnanslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Salesforce marknadsföring länkade Molntjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **SalesforceMarketingCloud** | Ja |
| clientId | Klient-ID som är associerade med programmet Salesforce marknadsföring molnet.  | Ja |
| clientSecret | Klienthemlighet som är associerade med applikationen Salesforce marknadsföring molnet. Du kan välja att markera det här fältet som en SecureString att lagra den säkert i ADF eller lagra lösenord i Azure Key Vault och låta ADF kopiera aktivitet pull därifrån vid kopiering av data - mer information från [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är true.  | Nej |
| useHostVerification | Anger om värdnamnet i servercertifikatet så att den matchar värdnamnet för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |
| usePeerVerification | Anger om att verifiera identiteten för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |

**Exempel:**

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce marknadsföring molnet dataset.

Ange typegenskapen för datauppsättningen till för att kopiera data från Salesforce marknadsföring molnet **SalesforceMarketingCloudObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

**Exempel**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce marknadsföring molnet källa.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce marknadsföring moln som källa

Om du vill kopiera data från Salesforce marknadsföring moln som källtypen i kopieringsaktiviteten till **SalesforceMarketingCloudSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **SalesforceMarketingCloudSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
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
