---
title: Kopiera data från Concur med Azure Data Factory (för hands version)
description: Lär dig hur du kopierar data från Concur till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jingwang
ms.openlocfilehash: f6d6c830eec8e711e700733a90611c353b68439d
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030806"
---
# <a name="copy-data-from-concur-using-azure-data-factory-preview"></a>Kopiera data från Concur med Azure Data Factory (för hands version)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Concur. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

> [!IMPORTANT]
> Den här kopplingen är för närvarande en för hands version. Du kan prova det och ge oss feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Concur-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Concur till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

> [!NOTE]
> Partner kontot stöds inte för närvarande.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Concur-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Concur:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Concur** | Yes |
| connectionProperties | En grupp egenskaper som definierar hur du ansluter till Concur. | Yes |
| **_Under `connectionProperties` :_* _ | | |
| authenticationType | Tillåtna värden är `OAuth_2.0_Bearer` och `OAuth_2.0` (bakåtkompatibelt). Alternativet OAuth 2,0-autentisering fungerar med det gamla API: t Concur som inaktuell sedan feb 2017. | Yes |
| värd | Slut punkten för Concur-servern, `implementation.concursolutions.com` t. ex..  | Yes |
| baseUrl | Bas-URL: en för din Concur behörighets webb adress. | Ja för `OAuth_2.0_Bearer` autentisering |
| ClientID | Program klient-ID som tillhandahålls av Concur app Management.  | Yes |
| clientSecret | Den klient hemlighet som motsvarar klient-ID: t. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja för `OAuth_2.0_Bearer` autentisering |
| användarnamn | Det användar namn som du använder för att komma åt Concur-tjänsten. | Yes |
| password | Lösen ordet som motsvarar det användar namn som du angav i fältet användar namn. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| useEncryptedEndpoints | Anger om data källans slut punkter krypteras med HTTPS. Standardvärdet är True.  | No |
| useHostVerification | Anger om värd namnet i Server certifikatet måste matcha värd namnet för servern vid anslutning via TLS. Standardvärdet är True.  | No |
| usePeerVerification | Anger om du vill verifiera serverns identitet vid anslutning via TLS. Standardvärdet är True.  | No |

_ *Exempel:**

```json
{ 
    "name": "ConcurLinkedService", 
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "connectionProperties": {
                "host":"<host e.g. implementation.concursolutions.com>",
                "baseUrl": "<base URL for authorization e.g. us-impl.api.concursolutions.com>",
                "authenticationType": "OAuth_2.0_Bearer",
                "clientId": "<client id>",
                "clientSecret": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "username": "fakeUserName",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
} 
```

**Exempel (bakåtkompatibelt):**

Observera att följande är en äldre länkad tjänst modell utan `connectionProperties` och använder `OAuth_2.0` autentisering.

```json
{
    "name": "ConcurLinkedService",
    "properties": {
        "type": "Concur",
        "typeProperties": {
            "clientId" : "<clientId>",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Concur DataSet.

Om du vill kopiera data från Concur anger du egenskapen type för data uppsättningen till **ConcurObject**. Det finns ingen ytterligare typ bestämd egenskap i den här typen av data uppsättning. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **ConcurObject** | Yes |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitets källan har angetts) |


**Exempel**

```json
{
    "name": "ConcurDataset",
    "properties": {
        "type": "ConcurObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Concur linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Concur-källan.

### <a name="concursource-as-source"></a>ConcurSource som källa

Om du vill kopiera data från Concur anger du käll typen i kopierings aktiviteten till **ConcurSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **ConcurSource** | Yes |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Opportunities where Id = xxx "`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromConcur",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Concur input dataset name>",
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
                "type": "ConcurSource",
                "query": "SELECT * FROM Opportunities where Id = xxx"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
