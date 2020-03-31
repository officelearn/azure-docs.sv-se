---
title: Kopiera data från ServiceNow
description: Lär dig hur du kopierar data från ServiceNow till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: dabcc5afe4a092e4919c854071a698c6e6ebf0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926175"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Kopiera data från ServiceNow med Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från ServiceNow. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här ServiceNow-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från ServiceNow till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, därför behöver du inte installera någon drivrutin manuellt med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för ServiceNow-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för ServiceNow-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **ServiceNow** | Ja |
| slutpunkt | Slutpunkten för ServiceNow-servern`http://<instance>.service-now.com`( ).  | Ja |
| authenticationType | Den autentiseringstyp som ska användas. <br/>Tillåtna värden är: **Grundläggande**, **OAuth2** | Ja |
| användarnamn | Användarnamnet som används för att ansluta till ServiceNow-servern för enkel och OAuth2-autentisering.  | Ja |
| password | Lösenordet som motsvarar användarnamnet för grundläggande autentisering och OAuth2-autentisering. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| ClientID | Klient-ID för OAuth2-autentisering.  | Inga |
| clientSecret (klientSecret) | Klienthemligheten för OAuth2-autentisering. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| användaKrypteradeEndpoints | Anger om slutpunkterna för datakällan är krypterade med HTTPS. Standardvärdet är True.  | Inga |
| useHostVerification | Anger om värdnamnet i serverns certifikat ska behövas för att matcha serverns värdnamn när den ansluter via SSL. Standardvärdet är True.  | Inga |
| användaUppvering | Anger om serverns identitet ska verifieras när den ansluter via SSL. Standardvärdet är True.  | Inga |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av ServiceNow-datauppsättningen.

Om du vill kopiera data från ServiceNow anger du egenskapen typ för datauppsättningen till **ServiceNowObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **ServiceNowObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av ServiceNow-källan.

### <a name="servicenow-as-source"></a>ServiceNow som källa

Om du vill kopiera data från ServiceNow anger du källtypen i kopieringsaktiviteten till **ServiceNowSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **ServiceNowSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Actual.alm_asset"`. | Nej (om "tableName" i datauppsättningen har angetts) |

Observera följande när du anger schemat och kolumnen för ServiceNow i frågan och **se [Prestandatips](#performance-tips) om om hur du får kopiera prestandakonsekvenser**.

- **Schema:** Ange schemat `Actual` `Display` som eller i ServiceNow-frågan, som du `sysparm_display_value` kan se det som parametern som sant eller falskt när du [anropar ServiceNow vilsamma API:er](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Kolumn:** Kolumnnamnet för det `Actual` faktiska `[column name]_value`värdet under schemat `Display` är `[column name]_display_value`, medan för visningsvärde under schemat är . Kolumnnamnet behöver mappas till schemat som används i frågan.

**Exempelfråga:** 
 `SELECT col_value FROM Actual.alm_asset` ELLER 
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

ServiceNow har 2 olika scheman, en är **"Faktisk"** som returnerar faktiska data, den andra är **"Display"** som returnerar visningsvärden för data. 

Om du har ett filter i frågan använder du "Faktiskt" schema som har bättre kopieringsprestanda. När du frågar mot "Faktiskt" schema stöder ServiceNow inbyggt filter när data hämtas för att bara returnera den filtrerade resultatuppsättningen, medan ADF vid frågor om "Display"-schema hämtar alla data och använder filtret internt.

### <a name="index"></a>Index

ServiceNow tabellindex kan bidra till att förbättra frågeprestanda, se [Skapa ett tabellindex](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
