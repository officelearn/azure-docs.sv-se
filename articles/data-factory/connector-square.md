---
title: Kopiera data från Fyrkant (förhandsgranskning)
description: Lär dig hur du kopierar data från Square till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.date: 08/01/2019
ms.openlocfilehash: de795d082069ff5ad895c48b7a507512449d0208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931010"
---
# <a name="copy-data-from-square-using-azure-data-factory-preview"></a>Kopiera data från Square med Azure Data Factory (förhandsversion)

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från Square. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

> [!IMPORTANT]
> Den här kopplingen är för närvarande i förhandsgranskning. Du kan prova det och ge oss feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här fyrkantiga kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)


Du kan kopiera data från Square till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, därför behöver du inte installera någon drivrutin manuellt med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Square-koppling.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Square-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Square** | Ja |
| värd | URL:en för Square-förekomsten. (dvs. mystore.mysquare.com)  | Ja |
| ClientID | Klient-ID som är associerat med square-programmet.  | Ja |
| clientSecret (klientSecret) | Klienthemligheten som är associerad med square-programmet. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| redirectUri | Den omdirigerings-URL som tilldelats på instrumentpanelen för fyrkantiga program. (dvs. http:\//localhost:2500)  | Ja |
| användaKrypteradeEndpoints | Anger om slutpunkterna för datakällan är krypterade med HTTPS. Standardvärdet är True.  | Inga |
| useHostVerification | Anger om värdnamnet i serverns certifikat ska behövas för att matcha serverns värdnamn när den ansluter via SSL. Standardvärdet är True.  | Inga |
| användaUppvering | Anger om serverns identitet ska verifieras när den ansluter via SSL. Standardvärdet är True.  | Inga |

**Exempel:**

```json
{
    "name": "SquareLinkedService",
    "properties": {
        "type": "Square",
        "typeProperties": {
            "host" : "mystore.mysquare.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "redirectUri" : "http://localhost:2500"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Kvadratdatauppsättning.

Om du vill kopiera data från Square anger du egenskapen type för datauppsättningen till **SquareObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **SquareObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "SquareDataset",
    "properties": {
        "type": "SquareObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Square linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Square-källa.

### <a name="square-as-source"></a>Kvadrat som källa

Om du vill kopiera data från Square anger du källtypen i kopieringsaktiviteten till **SquareSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **SquareSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Business"`. | Nej (om "tableName" i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSquare",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Square input dataset name>",
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
                "type": "SquareSource",
                "query": "SELECT * FROM Business"
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
