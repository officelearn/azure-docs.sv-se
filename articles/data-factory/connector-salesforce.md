---
title: "Kopiera data från Salesforce med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från Salesforce till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: c819b3e3e715427632e793ce77d31554914d248e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-salesforce-using-azure-data-factory"></a>Kopiera data från Salesforce med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-salesforce-connector.md)
> * [Version 2 – förhandsversion](connector-salesforce.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en Salesforce-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Salesforce-anslutningsprogrammet i V1](v1/data-factory-salesforce-connector.md).

## <a name="supported-scenarios"></a>Scenarier som stöds

Du kan kopiera data från Salesforce-databasen till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt Salesforce-anslutningsprogrammet stöder följande versioner av Salesforce: **Developer Edition, Professional Edition, Enterprise Edition eller obegränsade Edition**. Den stöder kopiering av data från Salesforce **produktion, sandbox och domänen**.

## <a name="prerequisites"></a>Krav

* API-behörighet måste vara aktiverat i Salesforce. Se [hur aktivera API-åtkomst i Salesforce av behörighetsgrupp?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce begärandebegränsningar

Salesforce har begränsningar för både förfrågningarna API och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begäranden överskrider gränsen, begränsning inträffar och du ser slumpmässiga fel.
- Om det totala antalet begäranden överskrider gränsen, blockeras Salesforce-konto i 24 timmar.

Du kan också få ”REQUEST_LIMIT_EXCEEDED”-fel i båda scenarierna. Se avsnittet ”API Begärandebegränsningar” i den [Salesforce Developer gränser](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) artikeln för information.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Salesforce-anslutningsprogrammet.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Salesforce länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen type måste anges till: **Salesforce**. |Ja |
| environmentUrl | Ange URL: en för Salesforce-instans. <br><br> -Standard är `"https://login.salesforce.com"`. <br> -Om du vill kopiera data från sandbox, ange `"https://test.salesforce.com"`. <br> -Om du vill kopiera data från domänen, till exempel ange, `"https://[domain].my.salesforce.com"`. |Nej |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| lösenord |Ange ett lösenord för användarkontot. |Ja |
| securityToken |Ange en säkerhetstoken för användarkontot. Se [hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställa/hämta en säkerhetstoken. Läs om säkerhetstoken i allmänhet i [säkerhets- och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ja |

**Exempel:**

```json
{
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        }
    },
    "name": "SalesforceLinkedService"
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce-dataset.

Ange typegenskapen för dataset för att kopiera data från Salesforce, **RelationalTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i Salesforce-databasen. | Nej (om ”fråga” i aktivitetskälla har angetts) |

> [!IMPORTANT]
> ”__C”-delen av API-namnet krävs för alla anpassade objekt.

![Namnet på data Factory - anslutning Salesforce - API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exempel:**

```json
{
    "name": "SalesforceDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable__c"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce-källa.

### <a name="salesforce-as-source"></a>Salesforce som källa

Om du vill kopiera data från Salesforce som källtypen i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB |Använd anpassad fråga för att läsa data. Du kan använda en SQL-92-fråga eller [Salesforce objektet Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) frågan. Till exempel: `select * from MyTable__c`. | Nej (om ”tabellnamn” i datamängden har angetts) |

> [!IMPORTANT]
> ”__C”-delen av API-namnet krävs för alla anpassade objekt.

![Namnet på data Factory - anslutning Salesforce - API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="query-tips"></a>Frågan tips

### <a name="retrieving-data-from-salesforce-report"></a>Hämtar data från Salesforce-rapport

Du kan hämta data från Salesforce-rapporter genom att ange frågan som `{call "<report name>"}. Example: `”frågan” ”: {anropa \"TestReport\"}” ”.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Hämta borttagna poster från Salesforce-Papperskorgen

Om du vill fråga ej permanent borttagna poster från Salesforce-Papperskorgen, kan du ange **”IsDeleted = 1”** i frågan. Exempel:

* Om du vill fråga endast borttagna poster, ange ”Välj * från MyTable__c **där IsDeleted = 1**”
* Om du vill fråga efter alla poster som inklusive det befintliga och den borttagna, ange ”Välj * från MyTable__c **där IsDeleted = 0 eller IsDeleted = 1**”

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Hämtning av data med hjälp av where-sats för kolumnen datum/tid

När ange SOQL eller SQL-fråga, titta närmare på skillnaden för DateTime-format. Exempel:

* **SOQL exempel**:`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', <datetime parameter>, <datetime parameter>)`
* **SQL-exempel**:`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="data-type-mapping-for-salesforce"></a>Mappning för Salesforce-datatyp

När du kopierar data från Salesforce, används följande mappningar från Salesforce-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Salesforce-datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| Automatisk tal |Sträng |
| Kryssruta |Booleskt värde |
| Valuta |dubbla |
| Date |Datum och tid |
| Datum/tid |Datum och tid |
| E-post |Sträng |
| Id |Sträng |
| Uppslagsrelation |Sträng |
| Flerval listruta |Sträng |
| Tal |dubbla |
| Procent |dubbla |
| Telefon |Sträng |
| Listruta |Sträng |
| Text |Sträng |
| Textområde |Sträng |
| Textområde (Long) |Sträng |
| Textområde (omfattande) |Sträng |
| Text (krypterade) |Sträng |
| URL: EN |Sträng |


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).