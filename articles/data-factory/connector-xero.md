---
title: Kopiera data från Xero med Azure Data Factory
description: Lär dig hur du kopierar data från Xero till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: 1f6404da163e075b63a99a1d8474cdba4e064b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930886"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Kopiera data från Xero med Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från Xero. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Denna Xero-koppling stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Xero till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Specifikt stöder denna Xero-kontakt:

- Xero [privat ansökan](https://developer.xero.com/documentation/getting-started/api-application-types) men inte offentlig ansökan.
- Alla Xero-tabeller (API-slutpunkter) utom "Rapporter". 

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, därför behöver du inte installera någon drivrutin manuellt med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Xero-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Xero-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste ställas in på: **Xero** | Ja |
| värd | Slutpunkten för Xero-servern`api.xero.com`( ).  | Ja |
| consumerKey (consumerKey) | Konsumentnyckeln som är associerad med Xero-programmet. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| privateKey (privateKey) | Den privata nyckeln från .pem-filen som genererades för ditt privata Xero-program finns i [Skapa ett offentligt/privat nyckelpar](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Observera att **generera privatekey.pem med domningar av 512** med `openssl genrsa -out privatekey.pem 512`; 1024 stöds inte. Inkludera all text från PEM-filen inklusive Unix-radändelser(\n), se exemplet nedan.<br/><br/>Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| användaKrypteradeEndpoints | Anger om slutpunkterna för datakällan är krypterade med HTTPS. Standardvärdet är True.  | Inga |
| useHostVerification | Anger om värdnamnet krävs i serverns certifikat för att matcha serverns värdnamn när den ansluter via SSL. Standardvärdet är True.  | Inga |
| användaUppvering | Anger om serverns identitet ska verifieras när den ansluter via SSL. Standardvärdet är True.  | Inga |

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

**Exempel på privat nyckelvärde:**

Inkludera all text från PEM-filen inklusive Unix-radändelsen(\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Xero-datauppsättningen.

Om du vill kopiera data från Xero anger du typegenskapen för datauppsättningen till **XeroObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **XeroObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Xero-källan.

### <a name="xero-as-source"></a>Xero som källa

Om du vill kopiera data från Xero anger du källtypen i kopieringsaktiviteten till **XeroSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type property för kopieringsaktivitetskällan måste ställas in på: **XeroSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM Contacts"`. | Nej (om "tableName" i datauppsättningen har angetts) |

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

- Tabeller med komplexa objekt delas upp i flera tabeller. Banktransaktioner har till exempel en komplex datastruktur "LineItems", så data `Bank_Transaction` `Bank_Transaction_Line_Items`från `Bank_Transaction_ID` banktransaktionen mappas till tabell och , med som sekundärnyckel för att länka ihop dem.

- Xero-data är tillgängliga via `Minimal` två scheman: (standard) och `Complete`. Schemat Slutför innehåller nödvändiga anropstabeller som kräver ytterligare data (t.ex. ID-kolumn) innan du gör önskad fråga.

Följande tabeller har samma information i schemat Minimal och Fullständig. Om du vill minska antalet API-anrop använder du Minimal schema (standard).

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
- Invoices_ Förskottsbetalningar 
- Invoices_Overpayments 
- Manual_Journals 
- Överbetalningar 
- Overpayments_Allocations 
- Förskottsbetalningar 
- Prepayments_Allocations 
- Kvitton 
- Receipt_Validation_Errors 
- Tracking_Categories

Följande tabeller kan bara efterfrågas med ett fullständigt schema:

- Komplett.Bank_Transaction_Line_Items 
- Komplett.Bank_Transaction_Line_Item_Tracking 
- Komplett.Contact_Group_Contacts 
- Complete.Contacts_Contact_ Personer 
- Komplett.Credit_Note_Line_Items 
- Komplett.Credit_Notes_Line_Items_Tracking 
- Komplett.Expense_Claim_ Betalningar 
- Komplett.Expense_Claim_Receipts 
- Komplett.Invoice_Line_Items 
- Komplett.Invoices_Line_Items_Tracking
- Komplett.Manual_Journal_Lines 
- Komplett.Manual_Journal_Line_Tracking 
- Komplett.Overpayment_Line_Items 
- Komplett.Overpayment_Line_Items_Tracking 
- Komplett.Prepayment_Line_Items 
- Komplett.Prepayment_Line_Item_Tracking 
- Komplett.Receipt_Line_Items 
- Komplett.Receipt_Line_Item_Tracking 
- Komplett.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds av kopieringsaktiviteten finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
