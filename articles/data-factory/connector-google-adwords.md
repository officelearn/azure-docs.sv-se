---
title: Kopiera data från Google AdWords
description: Lär dig hur du kopierar data från Google AdWords till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: 79e277cb3d512ee17da866a61e9d6d66a50cd902
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415028"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Kopiera data från Google AdWords med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från Google AdWords. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Google AdWords-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)


Du kan kopiera data från Google AdWords till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, därför behöver du inte installera någon drivrutin manuellt med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Google AdWords-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten i Google AdWords:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste vara inställd på: **GoogleAdWords** | Ja |
| klientKundID | Kund-ID:t för det AdWords-konto som du vill hämta rapportdata för.  | Ja |
| utvecklareToken | Utvecklartoken som är kopplad till förvaltarkontot som du använder för att bevilja åtkomst till AdWords-API:et.  Du kan välja att markera det här fältet som en SecureString för att lagra det säkert i ADF, eller lagra lösenord i Azure Key Vault och låta ADF kopiera aktivitet hämta därifrån när du utför datakopiering - läs mer från [Store-autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| authenticationType | OAuth 2.0-autentiseringsmekanismen som används för autentisering. ServiceAuthentication kan endast användas på självvärdbaserad IR. <br/>Tillåtna värden är: **ServiceAuthentication**, **UserAuthentication** | Ja |
| uppdateraToken | Uppdateringstoken som hämtats från Google för att ha auktoriserat åtkomst till AdWords för UserAuthentication. Du kan välja att markera det här fältet som en SecureString för att lagra det säkert i ADF, eller lagra lösenord i Azure Key Vault och låta ADF kopiera aktivitet hämta därifrån när du utför datakopiering - läs mer från [Store-autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Inga |
| ClientID | Klient-ID för Google-programmet som används för att hämta uppdateringstoken. Du kan välja att markera det här fältet som en SecureString för att lagra det säkert i ADF, eller lagra lösenord i Azure Key Vault och låta ADF kopiera aktivitet hämta därifrån när du utför datakopiering - läs mer från [Store-autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Inga |
| clientSecret (klientSecret) | Klienthemligheten för google-programmet som används för att hämta uppdateringstoken. Du kan välja att markera det här fältet som en SecureString för att lagra det säkert i ADF, eller lagra lösenord i Azure Key Vault och låta ADF kopiera aktivitet hämta därifrån när du utför datakopiering - läs mer från [Store-autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Inga |
| e-post | E-post-ID för tjänstkontot som används för ServiceAuthentication och kan endast användas på självvärdbaserad IR.  | Inga |
| keyFilePath | Den fullständiga sökvägen till .p12-nyckelfilen som används för att autentisera e-postadressen för tjänstkontot och som endast kan användas på självvärdbaserad IR.  | Inga |
| betroddaCertPath | Den fullständiga sökvägen till PEM-filen som innehåller betrodda certifikatutfärdare för att verifiera servern när du ansluter via TLS. Den här egenskapen kan bara ställas in när du använder TLS på självvärdbaserad IR. The default value is the cacerts.pem file installed with the IR.  | Inga |
| användaSystemTrustStore | Anger om ett CERTIFIKAT FRÅN systemförtroendearkivet eller från en angiven PEM-fil ska användas. Standardvärdet är false.  | Inga |

**Exempel:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Google AdWords-datauppsättningen.

Om du vill kopiera data från Google AdWords anger du datauppsättningens typegenskap på **GoogleAdWordsObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **GoogleAdWordsObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Google AdWords-källan.

### <a name="google-adwords-as-source"></a>Google AdWords som källa

Om du vill kopiera data från Google AdWords anger du källtypen i kopieringsaktiviteten till **GoogleAdWordsSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type property för kopians aktivitet måste anges till: **GoogleAdWordsSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
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
