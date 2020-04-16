---
title: Kopiera data från AWS Marketplace
description: Lär dig hur du kopierar data från Amazon Marketplace Web Service till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 4620ef5b6a72afbe86b0ace33328a769eab31e5e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418277"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory"></a>Kopiera data från Amazon Marketplace Web Service med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från Amazon Marketplace Web Service. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Amazon Marketplace Web Service-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Amazon Marketplace Web Service till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, därför behöver du inte installera någon drivrutin manuellt med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Amazon Marketplace Web Service-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten Amazon Marketplace Web Service:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste ställas in på: **AmazonMWS** | Ja |
| slutpunkt | Slutpunkten för Amazon MWS-servern, (det vill mws.amazonservices.com)  | Ja |
| marketplaceID (marketplaceID) | Amazon Marketplace-ID som du vill hämta data från. Om du vill hämta data från flera Marketplace-ID:er avgränsar du dem med ett kommatecken (`,`). (det vill än A2EUQ1WTGCTBG2)  | Ja |
| säljareID | Amazon säljaren ID.  | Ja |
| mwsAuthToken | Amazon MWS-autentiseringstoken. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| accessKeyId | Åtkomstnyckel-ID:et som används för att komma åt data.  | Ja |
| secretKey (secretKey) | Den hemliga nyckeln som används för att komma åt data. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| användaKrypteradeEndpoints | Anger om slutpunkterna för datakällan är krypterade med HTTPS. Standardvärdet är True.  | Inga |
| useHostVerification | Anger om värdnamnet i serverns certifikat ska behövas för att matcha serverns värdnamn när du ansluter via TLS. Standardvärdet är True.  | Inga |
| användaUppvering | Anger om serverns identitet ska verifieras när den ansluter via TLS. Standardvärdet är True.  | Inga |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon Marketplace Web Service-datauppsättningen.

Om du vill kopiera data från Amazon Marketplace Web Service anger du egenskapen typ för datauppsättningen till **AmazonMWSObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för datauppsättningen måste ställas in på: **AmazonMWSObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon Marketplace Web Service-källan.

### <a name="amazon-mws-as-source"></a>Amazon MWS som källa

Om du vill kopiera data från Amazon Marketplace Web Service anger du källtypen i kopieringsaktiviteten till **AmazonMWSSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för kopieringsaktivitetskällan måste ställas in på: **AmazonMWSSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`. | Nej (om "tableName" i datauppsättningen har angetts) |

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

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
