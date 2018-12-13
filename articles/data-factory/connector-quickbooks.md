---
title: Kopiera data från QuickBooks Online med Azure Data Factory (förhandsversion) | Microsoft Docs
description: Lär dig hur du kopierar data från QuickBooks Online till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.openlocfilehash: 51a48576b56413e0e779a49829a6eccaa0266a57
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076115"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Kopiera data från QuickBooks Online med Azure Data Factory (förhandsversion)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från QuickBooks Online. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

> [!IMPORTANT]
> Den här anslutningsappen är för närvarande i förhandsversion. Du kan testa och ge oss feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från QuickBooks Online till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, måste du därför inte att manuellt installera en drivrutin som använder den här anslutningen.

Den här anslutningsappen stöder för närvarande endast 1.0a, vilket innebär att du måste ha ett utvecklarkonto med appar som skapats före den 17 juli 2017.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för QuickBooks connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för QuickBooks länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **QuickBooks** | Ja |
| slutpunkt | Slutpunkt för QuickBooks Online-servern. (det vill säga quickbooks.api.intuit.com)  | Ja |
| companyId | ID för företag på QuickBooks företaget att godkänna. Information om hur du hittar ID för företag finns i [hur hittar jag mitt företags-ID?](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551). | Ja |
| consumerKey | Konsumentnyckel för autentisering för OAuth 1.0. | Ja |
| consumerSecret | Konsumenthemligheten för OAuth 1.0-autentisering. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| accessToken | Åtkomsttoken för autentisering för OAuth 1.0. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| accessTokenSecret | Åtkomsttokenhemligheten för OAuth 1.0-autentisering. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är sant.  | Nej |

**Exempel:**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "endpoint" : "quickbooks.api.intuit.com",
            "companyId" : "<companyId>",
            "consumerKey": "<consumerKey>",
            "consumerSecret": {
                "type": "SecureString",
                "value": "<consumerSecret>"
            },
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "accessTokenSecret": {
                 "type": "SecureString",
                 "value": "<accessTokenSecret>"
            },
            "useEncryptedEndpoints" : true
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av QuickBooks datauppsättning.

Kopiera data från QuickBooks Online genom att ange typegenskapen på datauppsättningen till **QuickBooksObject**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **QuickBooksObject** | Ja |
| tableName | Namnet på tabellen. | Nej (om ”frågan” i aktivitetskälla har angetts) |
**Exempel**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av QuickBooks källa.

### <a name="quickbooks-as-source"></a>QuickBooks som källa

För att kopiera data från QuickBooks Online, ange typ av datakälla i kopieringsaktiviteten till **QuickBooksSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **QuickBooksSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromQuickBooks",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<QuickBooks input dataset name>",
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
                "type": "QuickBooksSource",
                "query": "SELECT * FROM \"Bill\" WHERE Id = '123' "
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="copy-data-from-quickbooks-desktop"></a>Kopiera data från Quickbooks Desktop

Kopieringsaktivitet i Azure Data Factory kan inte kopiera data direkt från Quickbooks Desktop. Exportera dina Quickbooks-data till en fil med kommaavgränsade värden (CSV)-fil för att kopiera data från Quickbooks Desktop och sedan överföra filen till Azure Blob Storage. Därifrån kan använda du Data Factory för att kopiera data till mottagare valfri.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
