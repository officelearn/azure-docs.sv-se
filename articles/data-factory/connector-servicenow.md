---
title: Kopiera data från ServiceNow med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från ServiceNow till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
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
ms.date: 05/28/2018
ms.author: jingwang
ms.openlocfilehash: c67f6c14dc396367e0179fe5bdb4663fcb7725da
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045974"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopiera data från ServiceNow med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från ServiceNow. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från ServiceNow till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar, måste du därför inte att manuellt installera en drivrutin med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter för ServiceNow-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för ServiceNow länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **ServiceNow** | Ja |
| slutpunkt | Slutpunkten för ServiceNow-server (`http://<instance>.service-now.com`).  | Ja |
| authenticationType | Autentiseringstypen som ska användas. <br/>Tillåtna värden är: **grundläggande**, **OAuth2** | Ja |
| användarnamn | Användarnamnet som används för att ansluta till ServiceNow-server för grundläggande och OAuth2-autentisering.  | Ja |
| lösenord | Det lösenord som motsvarar användarnamn för Basic och OAuth2-autentisering. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| clientId | Klient-ID för OAuth2-autentisering.  | Nej |
| clientSecret | Klienthemlighet för OAuth2-autentisering. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är true.  | Nej |
| useHostVerification | Anger om värdnamnet i servercertifikatet så att den matchar värdnamnet för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |
| usePeerVerification | Anger om att verifiera identiteten för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |

**Exempel:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av ServiceNow dataset.

Ange typegenskapen för dataset för att kopiera data från ServiceNow, **ServiceNowObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

**Exempel**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av ServiceNow-källa.

### <a name="servicenow-as-source"></a>ServiceNow som källa

Om du vill kopiera data från ServiceNow, anger du källa i kopieringsaktiviteten för **ServiceNowSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **ServiceNowSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Actual.alm_asset"`. | Ja |

Observera följande när du anger schemat och kolumnen för ServiceNow i frågan:

- **Schema:** ange scheman som `Actual` eller `Display` i ServiceNow-frågan som du kan se det som parameter för `sysparm_display_value` som SANT eller FALSKT när du anropar [ServiceNow restful-API:](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolumn:** kolumnnamn för faktiska värden under `Actual` schemat är `[columne name]_value`, medan för visningsvärde under `Display` schemat är `[columne name]_display_value`. Observera kolumnnamnet måste mappas till schemat som används i frågan.

**Exempelfråga:** 
 `SELECT col_value FROM Actual.alm_asset` eller `SELECT col_display_value FROM Display.alm_asset`

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
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
