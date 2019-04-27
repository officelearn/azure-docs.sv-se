---
title: Kopiera data från Google BigQuery med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Google BigQuery till mottagarens datalager genom att använda en Kopieringsaktivitet i en data factory-pipeline.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: c9320c8d0cf512bc9145accc07ab4c79630a7c84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808884"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopiera data från Google BigQuery med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Google BigQuery. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Google BigQuery till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Data Factory tillhandahåller en inbyggd drivrutin om du vill aktivera anslutningen. Därför behöver du inte manuellt har installerat en drivrutin för att använda den här anslutningen.

>[!NOTE]
>Det här Google BigQuery-anslutningsapp är byggt på BigQuery APIs. Tänk på att BigQuery begränsningar av inkommande begäranden och tillämpar lämpliga kvoter på basis av per projekt, referera till [kvoter och gränser - API: et begär](https://cloud.google.com/bigquery/quotas#api_requests). Kontrollera att du inte utlösa för många samtidiga begäranden till kontot.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Google BigQuery-anslutningsapp.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Google BigQuery länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **GoogleBigQuery**. | Ja |
| Projekt | Projekt-ID för BigQuery standardprojekt att fråga mot.  | Ja |
| additionalProjects | En kommaavgränsad lista över projekt-ID: N offentliga BigQuery projekt till åtkomst.  | Nej |
| requestGoogleDriveScope | Om du vill begära åtkomst till Google Drive. Att tillåta åtkomst för Google Drive aktiverar stöd för externa tabeller som kombinerar BigQuery-data med data från Google Drive. Standardvärdet är **FALSKT**.  | Nej |
| authenticationType | OAuth 2.0 autentiseringsmekanism som används för autentisering. ServiceAuthentication kan användas endast på lokal Integration Runtime. <br/>Tillåtna värden är **UserAuthentication** och **ServiceAuthentication**. Avse respektive avsnitt under den här tabellen på fler egenskaper och JSON-exempel för dessa typer av autentisering. | Ja |

### <a name="using-user-authentication"></a>Med hjälp av autentisering av användare

Ange egenskapen ”authenticationType” som **UserAuthentication**, och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| ClientId | ID för programmet som används för att generera uppdateringstoken. | Nej |
| ClientSecret | Hemligheten för programmet som används för att generera uppdateringstoken. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| refreshToken | Uppdateringstoken som hämtas från Google som används för att auktorisera åtkomst till BigQuery. Lär dig hur du skaffa ett från [hämta OAuth 2.0-åtkomsttoken](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) och [community-blogg](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |

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

### <a name="using-service-authentication"></a>Med hjälp av tjänst-autentisering

Ange egenskapen ”authenticationType” som **ServiceAuthentication**, och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt. Den här autentiseringstypen kan användas enbart på lokal Integration Runtime.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| e-post | Tjänsten konto e-ID som används för ServiceAuthentication. Den kan användas enbart på lokal Integration Runtime.  | Nej |
| keyFilePath | Den fullständiga sökvägen till den .p12-nyckelfil som används för att autentisera den tjänst e-postadressen. | Nej |
| trustedCertPath | Den fullständiga sökvägen till PEM-fil som innehåller certifikat från betrodda Certifikatutfärdare används för att verifiera servern när du ansluter via SSL. Den här egenskapen kan anges endast när du använder SSL på lokal Integration Runtime. Standardvärdet är filen cacerts.pem installerad med integration runtime.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från arkivet med betrodda system eller från en angiven .pem-fil. Standardvärdet är **FALSKT**.  | Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Google BigQuery-datauppsättningen.

Om du vill kopiera data från Google BigQuery, ange typegenskapen på datauppsättningen till **GoogleBigQueryObject**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till: **GoogleBigQueryObject** | Ja |
| tableName | Namnet på tabellen. | Nej (om ”frågan” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av typen av datakälla Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource som en typ av datakälla

Om du vill kopiera data från Google BigQuery, ange typ av datakälla i kopieringsaktiviteten till **GoogleBigQuerySource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till **GoogleBigQuerySource**. | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

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
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
