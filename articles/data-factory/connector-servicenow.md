---
title: Kopiera data från ServiceNow med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från ServiceNow till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 1e91bcdf3980b0f03967c09b0b7a75aa3d3a520a
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014632"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopiera data från ServiceNow med Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från ServiceNow. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från ServiceNow till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, måste du därför inte att manuellt installera en drivrutin som använder den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för ServiceNow-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för ServiceNow länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **ServiceNow** | Ja |
| slutpunkt | Slutpunkten för ServiceNow-server (`http://<instance>.service-now.com`).  | Ja |
| authenticationType | Autentiseringstypen som ska användas. <br/>Tillåtna värden är: **Grundläggande**, **OAuth2** | Ja |
| användarnamn | Användarnamnet som används för att ansluta till ServiceNow-servern för Basic och OAuth2-autentisering.  | Ja |
| lösenord | Lösenordet för användarnamnet för Basic- och OAuth2-autentisering. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| ClientId | Klient-ID för OAuth2-autentisering.  | Nej |
| ClientSecret | Klienthemlighet för OAuth2-autentisering. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är sant.  | Nej |
| useHostVerification | Anger om värdnamnet i servercertifikatet så att de matchar värdnamnet för servern när du ansluter via SSL. Standardvärdet är sant.  | Nej |
| usePeerVerification | Anger om du vill kontrollera identiteten på servern när du ansluter via SSL. Standardvärdet är sant.  | Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av ServiceNow-datauppsättningen.

Kopiera data från ServiceNow genom att ange typegenskapen på datauppsättningen till **ServiceNowObject**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **ServiceNowObject** | Ja |
| tableName | Namnet på tabellen. | Nej (om ”frågan” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av ServiceNow-källa.

### <a name="servicenow-as-source"></a>ServiceNow som källa

Om du vill kopiera data från ServiceNow, ange typ av datakälla i kopieringsaktiviteten till **ServiceNowSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **ServiceNowSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Actual.alm_asset"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

Tänk på följande när du anger schemat och kolumnen för ServiceNow i fråga och **avser [prestandatips](#performance-tips) på Kopiera prestanda de**.

- **Schema:** ange scheman som `Actual` eller `Display` i ServiceNow-frågan, vilket du kan titta på det som parameter för `sysparm_display_value` som SANT eller FALSKT när du anropar [ServiceNow restful API: er](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolumn:** kolumnnamn för faktiska värden under `Actual` schemat är `[columne name]_value`, och för visningsvärde under `Display` schemat är `[columne name]_display_value`. Obs kolumnnamnet måste mappas till schemat som används i frågan.

**Exempelfråga:**
`SELECT col_value FROM Actual.alm_asset` OR 
`SELECT col_display_value FROM Display.alm_asset`

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
## <a name="performance-tips"></a>Prestandatips

### <a name="schema-to-use"></a>Schema som ska användas

ServiceNow har 2 olika scheman, en är **”faktiskt”** som returnerar faktiska data, den andra är **”visas”** som returnerar visningsvärden av data. 

Om du har ett filter i frågan, använder du ”faktiskt” schema som har bättre kopiera prestanda. Vid frågor mot ”faktiskt” schema, ServiceNow har inbyggt stöd för filter vid hämtningen av data för att bara returnera filtrerad resultatuppsättning, medan vid frågor till ”Visa” schema ADF hämta alla data och använda filter internt.

### <a name="index"></a>Index

ServiceNow-Tabellindex kan hjälpa att förbättra frågeprestanda, referera till [skapa ett Tabellindex](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
