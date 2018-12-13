---
title: Kopiera data från Google AdWords med Azure Data Factory (förhandsversion) | Microsoft Docs
description: Lär dig hur du kopierar data från Google AdWords till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 2a17bca6f7b95850f84781b5838719617d45bdd2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077825"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Kopiera data från Google AdWords med Azure Data Factory (förhandsversion)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Google AdWords. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

> [!IMPORTANT]
> Den här anslutningsappen är för närvarande i förhandsversion. Du kan testa och ge feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Google AdWords till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, måste du därför inte att manuellt installera en drivrutin som använder den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Google AdWords connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Google AdWords länkad tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **GoogleAdWords** | Ja |
| clientCustomerID | Klient-kund-ID för AdWords-konto som du vill hämta rapportdata för.  | Ja |
| developerToken | Developer token som är associerade med det konto som används för att ge åtkomst till AdWords-API.  Du kan välja att markera det här fältet som en SecureString att lagra den på ett säkert sätt i ADF eller lagra lösenord i Azure Key Vault och låt ADF kopiera acitivty pull därifrån när du utför kopiering av data – Lär dig mer från [Store autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| authenticationType | OAuth 2.0 autentiseringsmekanism som används för autentisering. ServiceAuthentication kan bara användas på lokal IR. <br/>Tillåtna värden är: **ServiceAuthentication**, **UserAuthentication** | Ja |
| refreshToken | Uppdateringstoken som hämtas från Google för att auktorisera åtkomst till AdWords för UserAuthentication. Du kan välja att markera det här fältet som en SecureString att lagra den på ett säkert sätt i ADF eller lagra lösenord i Azure Key Vault och låt ADF kopiera acitivty pull därifrån när du utför kopiering av data – Lär dig mer från [Store autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Nej |
| ClientId | Klient-id för google-programmet används för att få uppdateringstoken. Du kan välja att markera det här fältet som en SecureString att lagra den på ett säkert sätt i ADF eller lagra lösenord i Azure Key Vault och låt ADF kopiera acitivty pull därifrån när du utför kopiering av data – Lär dig mer från [Store autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Nej |
| ClientSecret | Klienthemlighet för google-programmet används för att få uppdateringstoken. Du kan välja att markera det här fältet som en SecureString att lagra den på ett säkert sätt i ADF eller lagra lösenord i Azure Key Vault och låt ADF kopiera acitivty pull därifrån när du utför kopiering av data – Lär dig mer från [Store autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Nej |
| e-post | Den servicekonto e-post-ID som används för ServiceAuthentication och kan bara användas på lokal IR.  | Nej |
| keyFilePath | Den fullständiga sökvägen till den .p12-nyckelfil som används för att autentisera den tjänst e-postadressen och kan bara användas på lokal IR.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till filen .pem som innehåller certifikat från betrodda Certifikatutfärdare för att verifiera servern när du ansluter via SSL. Den här egenskapen kan bara anges när du använder SSL på lokal IR. Standardvärdet är filen cacerts.pem installerad med i IR.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från arkivet med betrodda system eller från en angiven PEM-fil. Standardvärdet är FALSKT.  | Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Google AdWords datauppsättning.

Kopiera data från Google AdWords genom att ange typegenskapen på datauppsättningen till **GoogleAdWordsObject**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **GoogleAdWordsObject** | Ja |
| tableName | Namnet på tabellen. | Nej (om ”frågan” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}

```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Google AdWords källa.

### <a name="google-adwords-as-source"></a>Google AdWords som källa

För att kopiera data från Google AdWords, ange typ av datakälla i kopieringsaktiviteten till **GoogleAdWordsSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **GoogleAdWordsSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

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

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
