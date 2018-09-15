---
title: Kopiera data från PayPal med Azure Data Factory (förhandsversion) | Microsoft Docs
description: Lär dig hur du kopierar data från PayPal till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: dc467d35f1eb05169eee89899f17e7cee4702017
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630504"
---
# <a name="copy-data-from-paypal-using-azure-data-factory"></a>Kopiera data från PayPal med Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från PayPal. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

> [!IMPORTANT]
> Den här anslutningsappen är för närvarande i förhandsversion. Du kan testa och ge oss feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från PayPal till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, måste du därför inte att manuellt installera en drivrutin som använder den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för PayPal connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för PayPal länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **PayPal** | Ja |
| värd | URL till PayPal-instans. (det vill säga api.sandbox.paypal.com)  | Ja |
| ClientId | Klient-ID som är associerade med PayPal programmet.  | Ja |
| ClientSecret | Klienthemlighet som är kopplad till PayPal programmet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är sant.  | Nej |
| useHostVerification | Anger om värdnamnet i servercertifikatet så att de matchar värdnamnet för servern när du ansluter via SSL. Standardvärdet är sant.  | Nej |
| usePeerVerification | Anger om du vill kontrollera identiteten på servern när du ansluter via SSL. Standardvärdet är sant.  | Nej |

**Exempel:**

```json
{
    "name": "PayPalLinkedService",
    "properties": {
        "type": "PayPal",
        "typeProperties": {
            "host" : "api.sandbox.paypal.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av PayPal datauppsättning.

Om du vill kopiera data från PayPal, ange typegenskapen på datauppsättningen till **PayPalObject**. Det finns ingen ytterligare typspecifika-egenskap i den här typen av datauppsättning.

**Exempel**

```json
{
    "name": "PayPalDataset",
    "properties": {
        "type": "PayPalObject",
        "linkedServiceName": {
            "referenceName": "<PayPal linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av PayPal källa.

### <a name="paypalsource-as-source"></a>PayPalSource som källa

Om du vill kopiera data från PayPal, ange typ av datakälla i kopieringsaktiviteten till **PayPalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **PayPalSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Payment_Experience"`. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromPayPal",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PayPal input dataset name>",
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
                "type": "PayPalSource",
                "query": "SELECT * FROM Payment_Experience"
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
