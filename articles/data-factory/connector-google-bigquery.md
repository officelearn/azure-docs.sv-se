---
title: Kopiera data från Google BigQuery med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Google BigQuery till stöds sink datalager med hjälp av en kopia aktivitet i en data factory-pipelinen.
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
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: e88d8046e31a43f444193dff91895b474be70d91
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332345"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopiera data från Google BigQuery med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktiviteten i Azure Data Factory för att kopiera data från Google BigQuery. Den bygger på den [Kopieringsaktiviteten översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över aktiviteten kopia.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, se [Kopieringsaktiviteten i version 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Google BigQuery till alla stöds sink-datalagret. En lista över datalager som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

 Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar. Därför behöver du inte manuellt installera en drivrutin om du vill använda den här anslutningen.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Google BigQuery kopplingen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Google BigQuery länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **GoogleBigQuery**. | Ja |
| projekt | Projekt-ID för BigQuery standardprojektet vid frågor mot.  | Ja |
| additionalProjects | En kommaavgränsad lista över projekt-ID: N av offentliga BigQuery projekt till åtkomst.  | Nej |
| requestGoogleDriveScope | Om du vill begära åtkomst till Google Drive. Google Drive åtkomst aktiverar stöd för externa tabeller som kombinerar BigQuery data med data från Google Drive. Standardvärdet är **FALSKT**.  | Nej |
| authenticationType | OAuth 2.0 autentiseringsmekanism som används för autentisering. ServiceAuthentication kan användas enbart på Self-hosted integrering Runtime. <br/>Tillåtna värden är **UserAuthentication** och **ServiceAuthentication**. Avse respektive avsnitt under den här tabellen på fler egenskaper och JSON-exempel för dessa typer av autentisering. | Ja |

### <a name="using-user-authentication"></a>Med hjälp av autentisering av användare

Egenskapen ”authenticationType” **UserAuthentication**, och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| clientId | ID för program som används för att generera uppdateringstoken för. | Nej |
| clientSecret | Hemligheten för program som används för att generera uppdateringstoken för. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| refreshToken | Den uppdateringstoken som hämtats från Google som används för att auktorisera åtkomst till BigQuery. Lär dig hur du hämtar en från [hämta OAuth 2.0-åtkomsttoken](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) och [community bloggen](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |

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
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Med hjälp av autentiseringsuppgifter

Egenskapen ”authenticationType” **ServiceAuthentication**, och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt. Den här autentiseringstypen kan användas enbart på Self-hosted integrering Runtime.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| e-post | Service-kontot e-ID som används för ServiceAuthentication. Den kan användas enbart på Self-hosted integrering Runtime.  | Nej |
| keyFilePath | Den fullständiga sökvägen till .p12-nyckelfil som används för att autentisera tjänsten kontos e-postadress. | Nej |
| trustedCertPath | Den fullständiga sökvägen till PEM-filen som innehåller betrodda certifikatutfärdare som används för att verifiera servern när du ansluter via SSL. Den här egenskapen kan anges endast när du använder SSL på Self-hosted integrering Runtime. Standardvärdet är filen cacerts.pem installerades med integration körningsmiljön.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett certifikat från arkivet med betrodda system eller från en angiven PEM-filen. Standardvärdet är **FALSKT**.  | Nej |

**Exempel:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
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

| Egenskap  | Beskrivning | Krävs |
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
