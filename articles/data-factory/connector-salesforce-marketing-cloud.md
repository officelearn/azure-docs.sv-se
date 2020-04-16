---
title: Kopiera data från Salesforce Marketing Cloud
description: Lär dig hur du kopierar data från Salesforce Marketing Cloud till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.date: 10/25/2019
ms.openlocfilehash: 1a5a2682198f9ce9f5cb39f21e244c723ca513d9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416655"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Kopiera data från Salesforce Marketing Cloud med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från Salesforce Marketing Cloud. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Salesforce Marketing Cloud-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Salesforce Marketing Cloud till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Salesforce Marketing Cloud-anslutningen stöder OAuth 2-autentisering. Den är byggd ovanpå [Salesforce Marketing Cloud REST API](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Den här kopplingen stöder inte hämtning av anpassade objekt eller anpassade datatillägg.

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med kopieringsaktivitet med .NET SDK, Python SDK, Azure PowerShell, REST API eller Azure Resource Manager-mall. Se [Kopiera aktivitetshandledning](quickstart-create-data-factory-dot-net.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Salesforce Marketing Cloud-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Salesforce Marketing Cloud-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **SalesforceMarketingCloud** | Ja |
| ClientID | Klient-ID som är associerat med Salesforce Marketing Cloud-programmet.  | Ja |
| clientSecret (klientSecret) | Klienthemligheten som är associerad med Salesforce Marketing Cloud-programmet. Du kan välja att markera det här fältet som en SecureString för att lagra det säkert i ADF, eller lagra lösenord i Azure Key Vault och låta ADF kopiera aktivitet hämta därifrån när du utför datakopiering - läs mer från [Store-autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| användaKrypteradeEndpoints | Anger om slutpunkterna för datakällan är krypterade med HTTPS. Standardvärdet är True.  | Inga |
| useHostVerification | Anger om värdnamnet i serverns certifikat ska behövas för att matcha serverns värdnamn när du ansluter via TLS. Standardvärdet är True.  | Inga |
| användaUppvering | Anger om serverns identitet ska verifieras när den ansluter via TLS. Standardvärdet är True.  | Inga |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce Marketing Cloud-datauppsättningen.

Om du vill kopiera data från Salesforce Marketing Cloud anger du egenskapen type för datauppsättningen till **SalesforceMarketingCloudObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för datauppsättningen måste anges till: **SalesforceMarketingCloudObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitetskällan har angetts) |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce Marketing Cloud-källan.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud som källa

Om du vill kopiera data från Salesforce Marketing Cloud anger du källtypen i kopieringsaktiviteten till **SalesforceMarketingCloudSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **SalesforceMarketingCloudSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i datauppsättningen har angetts) |

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

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
