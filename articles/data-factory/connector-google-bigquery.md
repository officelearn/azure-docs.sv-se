---
title: "Kopiera data från Google BigQuery med hjälp av Azure Data Factory (Beta) | Microsoft Docs"
description: "Lär dig hur du kopierar data från Google BigQuery till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 2d3327bd3f27e9743524590faaec98d36bf6c549
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="copy-data-from-google-bigquery-using-azure-data-factory-beta"></a>Kopiera data från Google BigQuery med hjälp av Azure Data Factory (Beta)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från Google BigQuery. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Den här anslutningen är för närvarande i betaversion. Du kan prova och ge oss feedback. Använd den inte i produktionsmiljöer.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Google BigQuery till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar, måste du därför inte att manuellt installera en drivrutin med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Google BigQuery connector.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Google BigQuery länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **GoogleBigQuery** | Ja |
| projektet | Projekt-ID för BigQuery standardprojektet vid frågor mot.  | Ja |
| additionalProjects | En kommaavgränsad lista över projekt-ID: N av offentliga BigQuery projekt till åtkomst.  | Nej |
| requestGoogleDriveScope | Om du vill begära åtkomst till Google Drive. Google Drive åtkomst aktiverar stöd för externa tabeller som kombinerar BigQuery data med data från Google Drive. Standardvärdet är false.  | Nej |
| AuthenticationType | OAuth 2.0 autentiseringsmekanism som används för autentisering. ServiceAuthentication kan bara användas på själva värdbaserade IR. <br/>Tillåtna värden är: **ServiceAuthentication**, **UserAuthentication** | Ja |
| refreshToken | Den uppdateringstoken som hämtats från Google för att ge åtkomst till BigQuery för UserAuthentication. Du kan välja att markera det här fältet som en SecureString att lagra den säkert i ADF eller lagra lösenord i Azure Key Vault och låta kopieringsaktiviteten hämtar därifrån vid kopiering av data - mer information från [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md). | Nej |
| e-post | Den servicekonto e-post-ID som används för ServiceAuthentication och kan bara användas på själva värdbaserade IR.  | Nej |
| keyFilePath | Den fullständiga sökvägen till .p12 nyckelfilen som används för att autentisera tjänsten kontos e-postadress och kan bara användas på egenvärdbaserat IR.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till PEM-filen som innehåller certifikat för betrodda Certifikatutfärdare för att verifiera servern när du ansluter via SSL. Den här egenskapen kan bara anges när du använder SSL på själva värdbaserade IR. Standardvärdet är filen cacerts.pem installeras med IR.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett certifikat från arkivet med betrodda system eller från en angiven PEM-fil. Standardvärdet är false.  | Nej |

**Exempel:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Google BigQuery dataset.

Ange typegenskapen för dataset för att kopiera data från Google BigQuery, **GoogleBigQueryObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

**Exempel**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Google BigQuery källa.

### <a name="googlebigquerysource-as-source"></a>GoogleBigQuerySource som källa

Om du vill kopiera data från Google BigQuery, anger du källa i kopieringsaktiviteten till **GoogleBigQuerySource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **GoogleBigQuerySource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
