---
title: Kopiera data från Google BigQuery med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Google BigQuery till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en pipeline för datafabriker.
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
ms.date: 09/04/2019
ms.openlocfilehash: 9fe6b494398337dd49bd8f0fe53b24666412a1b0
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991594"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopiera data från Google BigQuery med hjälp av Azure Data Factory

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från Google BigQuery. Den bygger på översiktsartikeln [Kopiera aktivitet](copy-activity-overview.md) som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Google BigQuery-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Google BigQuery till alla sink-datalager som stöds. En lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Data Factory tillhandahåller en inbyggd drivrutin för anslutning. Därför behöver du inte installera en drivrutin manuellt för att använda den här anslutningen.

>[!NOTE]
>Den här Google BigQuery-kontakten är byggd ovanpå BigQuery-API:erna. Tänk på att BigQuery begränsar den maximala hastigheten för inkommande begäranden och tillämpar lämpliga kvoter per projekt, se [Kvoter & Gränser - API-begäranden](https://cloud.google.com/bigquery/quotas#api_requests). Se till att du inte utlöser för många samtidiga begäranden till kontot.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Google BigQuery-kopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten Google BigQuery.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste vara inställd på **GoogleBigQuery**. | Ja |
| projekt | Projekt-ID för standard BigQuery-projektet att fråga mot.  | Ja |
| ytterligare projekt | En kommaavgränsad lista över projekt-ID:er för offentliga BigQuery-projekt att komma åt.  | Inga |
| requestGoogleDriveScope | Om du vill begära åtkomst till Google Drive. Genom att tillåta åtkomst till Google Drive kan du få stöd för federerade tabeller som kombinerar BigQuery-data med data från Google Drive. Standardvärdet är **falskt**.  | Inga |
| authenticationType | OAuth 2.0-autentiseringsmekanismen som används för autentisering. ServiceAuthentication kan endast användas på självvärderade integrationskörning. <br/>Tillåtna värden är **UserAuthentication** och **ServiceAuthentication**. Se avsnitt nedan för den här tabellen om fler egenskaper och JSON-exempel för dessa autentiseringstyper. | Ja |

### <a name="using-user-authentication"></a>Använda användarautentisering

Ange egenskapen "authenticationType" till **UserAuthentication**och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| ClientID | ID för programmet som används för att generera uppdateringstoken. | Inga |
| clientSecret (klientSecret) | Hemligheten bakom det program som används för att generera uppdateringstoken. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| uppdateraToken | Uppdateringstoken som hämtats från Google används för att auktorisera åtkomst till BigQuery. Lär dig hur du får en från [att få OAuth 2.0 tillgång tokens](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) och [denna gemenskap blogg](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |

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

### <a name="using-service-authentication"></a>Använda tjänstautentisering

Ange egenskapen "authenticationType" till **ServiceAuthentication**och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt. Den här autentiseringstypen kan endast användas på självvärderade integrationskörning.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| e-post | E-post-ID för tjänstkontot som används för ServiceAuthentication. Den kan endast användas på självvärderade integrationskörning.  | Inga |
| keyFilePath | Den fullständiga sökvägen till nyckelfilen .p12 som används för att autentisera e-postadressen för tjänstkontot. | Inga |
| betroddaCertPath | Den fullständiga sökvägen till PEM-filen som innehåller betrodda certifikatutfärdare som används för att verifiera servern när du ansluter via TLS. Den här egenskapen kan bara ställas in när du använder TLS på självvärdbaserad integrationskörning. Standardvärdet är filen cacerts.pem som är installerad med integrationskörningen.  | Inga |
| användaSystemTrustStore | Anger om ett certifikatutfärdarcertifikat ska användas från systemförtroendearkivet eller från en angiven PEM-fil. Standardvärdet är **falskt**.  | Inga |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Google BigQuery-datauppsättningen.

Om du vill kopiera data från Google BigQuery anger du datauppsättningens typegenskap på **GoogleBigQueryObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **GoogleBigQueryObject** | Ja |
| Datamängd | Namnet på Google BigQuery-datauppsättningen. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tableName | Tabellens namn. Den här egenskapen stöds för bakåtkompatibilitet. För ny arbetsbelastning använder du `dataset` och `table`. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av google bigquery-källtypen.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource som källtyp

Om du vill kopiera data från Google BigQuery anger du källtypen i kopieringsaktiviteten till **GoogleBigQuerySource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopian aktivitetskällan måste anges till **GoogleBigQuerySource**. | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`. | Nej (om "tableName" i datauppsättningen har angetts) |

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

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns i [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
