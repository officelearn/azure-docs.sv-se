---
title: Kopiera data från Salesforce Marketing Cloud med Azure Data Factory (förhandsversion) | Microsoft Docs
description: Lär dig hur du kopierar data från Salesforce Marketing Cloud till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 6548f4add8a794276bd4e7f7fa3c0bd7e24a2d5c
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726016"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory-preview"></a>Kopiera data från Salesforce Marketing Cloud med Azure Data Factory (förhandsversion)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Salesforce Marketing Cloud. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

> [!IMPORTANT]
> Den här anslutningsappen är för närvarande i förhandsversion. Du kan testa och ge oss feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Salesforce Marketing Cloud till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Salesforce Marketing Cloud Connector stöder OAuth 2-autentisering. Den är byggd på [Salesforce Marketing REST API](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Den här kopplingen stöder inte hämtning av anpassade objekt eller anpassade data tillägg.

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med en Kopieringsaktivitet med hjälp av .NET SDK, Python SDK, Azure PowerShell, REST API eller Azure Resource Manager-mall. Se [kopiera aktivitet självstudien](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Salesforce Marketing Cloud connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Salesforce Marketing Cloud länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till: **SalesforceMarketingCloud** | Ja |
| clientId | Klient-ID som är associerade med Salesforce Marketing Cloud-programmet.  | Ja |
| clientSecret | Klienthemlighet som är kopplade till Salesforce Marketing Cloud-programmet. Du kan välja att markera det här fältet som en SecureString för att lagra det på ett säkert sätt i ADF eller lagra lösen ord i Azure Key Vault och låta ADF kopierings aktivitet hämta från där när data kopian ska utföras – Läs mer från [lagra autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är sant.  | Nej |
| useHostVerification | Anger om värdnamnet i servercertifikatet så att de matchar värdnamnet för servern när du ansluter via SSL. Standardvärdet är sant.  | Nej |
| usePeerVerification | Anger om du vill kontrollera identiteten på servern när du ansluter via SSL. Standardvärdet är sant.  | Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce Marketing Cloud-datauppsättningen.

Om du vill kopiera data från Salesforce Marketing Cloud, ange typegenskapen på datauppsättningen till **SalesforceMarketingCloudObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till: **SalesforceMarketingCloudObject** | Ja |
| tableName | Namnet på tabellen. | Nej (om ”frågan” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce Marketing Cloud-källa.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud som källa

Om du vill kopiera data från Salesforce Marketing Cloud, ange typ av datakälla i kopieringsaktiviteten till **SalesforceMarketingCloudSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för kopierings aktivitets källan måste anges till: **SalesforceMarketingCloudSource** | Ja |
| query | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

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
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
