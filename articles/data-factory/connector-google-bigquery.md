---
title: "Kopiera data från Google BigQuery med hjälp av Azure Data Factory (beta) | Microsoft Docs"
description: "Lär dig hur du kopierar data från Google BigQuery till stöds sink datalager med hjälp av en kopia aktivitet i en data factory-pipelinen."
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 03aeb4fd190ec83a61875168116157404c1d730d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>Kopiera data från Google BigQuery med hjälp av Azure Data Factory (beta)

Den här artikeln beskrivs hur du använder Kopieringsaktiviteten i Azure Data Factory för att kopiera data från Google BigQuery. Den bygger på den [Kopieringsaktiviteten översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över aktiviteten kopia.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, se [Kopieringsaktiviteten i version 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Den här anslutningen är för närvarande i betaversion. Du kan prova och ge oss feedback. Använd den inte i produktionsmiljöer.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Google BigQuery till alla stöds sink-datalagret. En lista över datalager som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

 Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar. Därför behöver du inte manuellt installera en drivrutin om du vill använda den här anslutningen.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Google BigQuery kopplingen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Google BigQuery länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **GoogleBigQuery**. | Ja |
| projektet | Projekt-ID för BigQuery standardprojektet vid frågor mot.  | Ja |
| additionalProjects | En kommaavgränsad lista över projekt-ID: N av offentliga BigQuery projekt till åtkomst.  | Nej |
| requestGoogleDriveScope | Om du vill begära åtkomst till Google Drive. Google Drive åtkomst aktiverar stöd för externa tabeller som kombinerar BigQuery data med data från Google Drive. Standardvärdet är **FALSKT**.  | Nej |
| AuthenticationType | OAuth 2.0 autentiseringsmekanism som används för autentisering. ServiceAuthentication kan användas enbart på Self-hosted integrering Runtime. <br/>Tillåtna värden är **ServiceAuthentication** och **UserAuthentication**. | Ja |
| refreshToken | Den uppdateringstoken som hämtats från Google som används för att auktorisera åtkomst till BigQuery för UserAuthentication. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| e-post | Service-kontot e-ID som används för ServiceAuthentication. Den kan användas enbart på Self-hosted integrering Runtime.  | Nej |
| keyFilePath | Den fullständiga sökvägen till .p12-nyckelfil som används för att autentisera tjänsten kontos e-postadress. Den kan användas enbart på Self-hosted integrering Runtime.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till PEM-filen som innehåller betrodda certifikatutfärdare som används för att verifiera servern när du ansluter via SSL. Den här egenskapen kan anges endast när du använder SSL på Self-hosted integrering Runtime. Standardvärdet är filen cacerts.pem installerades med integration körningsmiljön.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett certifikat från arkivet med betrodda system eller från en angiven PEM-filen. Standardvärdet är **FALSKT**.  | Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av typen av datakälla Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource som typ av datakälla

Om du vill kopiera data från Google BigQuery, anger du källa i kopieringsaktiviteten till **GoogleBigQuerySource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till **GoogleBigQuerySource**. | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`. | Ja |

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
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
