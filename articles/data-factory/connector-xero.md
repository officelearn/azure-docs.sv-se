---
title: Kopiera data från Xero med Azure Data Factory (förhandsversion) | Microsoft Docs
description: Lär dig hur du kopierar data från Xero till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.openlocfilehash: d9ee9a73f4e88786ca51fe9fac50ce51e25b4dde
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123376"
---
# <a name="copy-data-from-xero-using-azure-data-factory-preview"></a>Kopiera data från Xero med Azure Data Factory (förhandsversion)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Xero. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

> [!IMPORTANT]
> Den här anslutningsappen är för närvarande i förhandsversion. Du kan testa och ge feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Xero till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Xero-anslutningen:

- Xero [privata program](https://developer.xero.com/documentation/getting-started/api-application-types) men inte offentliga program.
- Alla Xero tabeller (API-slutpunkter) förutom ”rapporter”. 

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, måste du därför inte att manuellt installera en drivrutin som använder den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Xero-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Xero länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **Xero** | Ja |
| värd | Slutpunkten för Xero-server (`api.xero.com`).  | Ja |
| consumerKey | Konsumenten nyckeln som associeras med Xero-programmet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| privateKey | Den privata nyckeln från filen .pem som genererades för ditt privata Xero-program finns i [skapa ett offentligt/privat nyckelpar](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Observera att **generera privatekey.pem med numbits 512** med `openssl genrsa -out privatekey.pem 512`; 1024 stöds inte. Inkluderar all text från .pem-filen, inklusive Unix rad endings(\n), finns i exemplet nedan.<br/><br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är sant.  | Nej |
| useHostVerification | Anger om värdnamnet krävs i servercertifikatet så att de matchar värdnamnet för servern när du ansluter via SSL. Standardvärdet är sant.  | Nej |
| usePeerVerification | Anger om du vill kontrollera identiteten på servern när du ansluter via SSL. Standardvärdet är sant.  | Nej |

**Exempel:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Privat nyckel exempelvärde:**

Inkludera all text från .pem-filen, inklusive endings(\n) för Unix-rad.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Xero-datauppsättningen.

Kopiera data från Xero genom att ange typegenskapen på datauppsättningen till **XeroObject**. Det finns ingen ytterligare typspecifika-egenskap i den här typen av datauppsättning.

**Exempel**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Xero-källa.

### <a name="xero-as-source"></a>Xero som källa

För att kopiera data från Xero, ange typ av datakälla i kopieringsaktiviteten till **XeroSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **XeroSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Contacts"`. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Observera följande när du anger Xero-frågan:

- Tabeller med komplexa objekt ska delas i flera tabeller. Till exempel banktransaktioner har en komplex datastruktur ”radobjekt”, så data bank transaktion är mappad till tabellen `Bank_Transaction` och `Bank_Transaction_Line_Items`, med `Bank_Transaction_ID` som sekundärnyckeln att länka dem tillsammans.

- Xero-data är tillgänglig via två scheman: `Minimal` (standard) och `Complete`. Fullständigt schema innehåller nödvändiga anrop tabeller som kräver ytterligare data (t.ex. ID-kolumn) innan du gör önskade frågan.

Följande tabeller innehåller samma information i schemat Minimal och fullständig. För att minska antalet API-anrop, använder du Minimal schema (standard).

- Bank_Transactions
- Contact_Groups 
- Contacts 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Fakturor 
- Invoices_Credit_Notes
- Invoices_ förskottsbetalningar 
- Invoices_Overpayments 
- Manual_Journals 
- Över 
- Overpayments_Allocations 
- Förskottsbetalningar 
- Prepayments_Allocations 
- Kvitton 
- Receipt_Validation_Errors 
- Tracking_Categories

Följande tabeller kan bara frågas med fullständigt schema:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ personer 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ betalningar 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds av Kopieringsaktivitet finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
