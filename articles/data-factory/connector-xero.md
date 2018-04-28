---
title: Kopiera data från Xero med hjälp av Azure Data Factory (Beta) | Microsoft Docs
description: Lär dig hur du kopierar data från Xero till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 131f147e2c445e8cfef12288d4d0d29f6fd7fe01
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Kopiera data från Xero med hjälp av Azure Data Factory (Beta)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från Xero. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Den här anslutningen är för närvarande i betaversion. Du kan prova och ge feedback. Använd den inte i produktionsmiljöer.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Xero till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Xero-anslutningen:

- Xero [privata programmet](https://developer.xero.com/documentation/getting-started/api-application-types) men inte offentliga program.
- Alla Xero tabeller (API-slutpunkter) utom ”rapporter”. 

Azure Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar, måste du därför inte att manuellt installera en drivrutin med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Xero-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Xero länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Xero** | Ja |
| värd | Slutpunkten för Xero-server (`api.xero.com`).  | Ja |
| consumerKey | Konsumenten nyckeln som associeras med Xero-programmet. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| privateKey | Den privata nyckeln från PEM-filen som har genererats för tillämpningsprogrammet Xero privata finns [skapa ett offentligt/privat nyckelpar](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Observera att **generera privatekey.pem med numbits på 512** med `openssl genrsa -out privatekey.pem 512`; 1024 stöds inte. Inkludera alla text från filen .pem inklusive Unix rad endings(\n), se exemplet nedan.<br/><br/>Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om käll-slutpunkter data krypteras med HTTPS. Standardvärdet är true.  | Nej |
| useHostVerification | Anger om värdnamnet krävs i servercertifikatet så att den matchar värdnamnet för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |
| usePeerVerification | Anger om att verifiera identiteten för servern när du ansluter via SSL. Standardvärdet är true.  | Nej |

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

**Exempel privata nyckel/värde:**

Inkludera alla text från filen .pem inklusive endings(\n) för Unix-rad.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Xero dataset.

Ange typegenskapen för dataset för att kopiera data från Xero, **XeroObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Xero källa.

### <a name="xero-as-source"></a>Xero som källa

Om du vill kopiera data från Xero, anger du källa i kopieringsaktiviteten till **XeroSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **XeroSource** | Ja |
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

- Tabeller med komplexa objekt delar upp till flera tabeller. Transaktioner har till exempel en komplex datastruktur ”LineItems”, så att data för banktransaktion är mappad till tabellen `Bank_Transaction` och `Bank_Transaction_Line_Items`, med `Bank_Transaction_ID` som sekundärnyckel länka dem tillsammans.

- Xero data är tillgängliga via två scheman: `Minimal` (standard) och `Complete`. Fullständig schemat innehåller nödvändiga anropet tabeller som kräver ytterligare data (t.ex. ID-kolumnen) innan du gör önskade frågan.

Följande tabeller innehåller samma information i schemat Minimal och fullständig. Använda Minimal schemat (standard) för att minska antalet API-anrop.

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
- Inleveranser 
- Receipt_Validation_Errors 
- Tracking_Categories

Följande tabeller kan endast efterfrågas med fullständigt schema:

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
En lista över datakällor som stöds av kopieringsaktiviteten finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
