---
title: Kopiera data från och till Salesforce med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Salesforce till stöds sink datalager eller från stöds källa datalager till Salesforce med hjälp av en kopia aktivitet i en data factory-pipelinen.
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
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: 99429b8090eca6d8633abfb1309f02168f1d06fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618318"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Kopiera data från och till Salesforce med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – Allmänt tillgänglig](v1/data-factory-salesforce-connector.md)
> * [Version 2 – förhandsversion](connector-salesforce.md)

Den här artikeln beskrivs hur du använder Kopieringsaktiviteten i Azure Data Factory för att kopiera data från och till Salesforce. Den bygger på den [Kopieringsaktiviteten översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över aktiviteten kopia.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory som är allmänt tillgänglig, se [Salesforce-anslutningsprogrammet i version 1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Salesforce till alla stöds sink-datalagret. Du kan också kopiera data från alla datalager stöds källa till Salesforce. En lista över datalager som stöds som datakällor eller sänkor av aktiviteten kopiera, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder denna Salesforce-koppling:

- Salesforce-utvecklare, Professional, Enterprise eller obegränsad utgåvor.
- Kopiera data från och till Salesforce produktion, sandbox och anpassade domäner.

## <a name="prerequisites"></a>Förutsättningar

API-behörighet måste vara aktiverat i Salesforce. Mer information finns i [aktivera API-åtkomst i Salesforce av behörighetsgrupp](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce begärandebegränsningar

Salesforce har begränsningar för både förfrågningarna API och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begäranden överskrider gränsen, begränsning inträffar och du ser slumpmässiga fel.
- Om det totala antalet begäranden överskrider gränsen, blockeras Salesforce-konto i 24 timmar.

Du kan också få felmeddelandet ”REQUEST_LIMIT_EXCEEDED” i båda scenarierna. Mer information finns i avsnittet ”API-begärandebegränsningar” i [Salesforce developer gränser](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Salesforce-anslutningsprogrammet.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper har stöd för Salesforce länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen type måste anges till **Salesforce**. |Ja |
| environmentUrl | Ange Webbadressen till Salesforce-instans. <br> -Standard är `"https://login.salesforce.com"`. <br> -Om du vill kopiera data från sandbox, ange `"https://test.salesforce.com"`. <br> -Om du vill kopiera data från domänen, till exempel ange, `"https://[domain].my.salesforce.com"`. |Nej |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| lösenord |Ange ett lösenord för användarkontot.<br/><br/>Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| securityToken |Ange en säkerhetstoken för användarkontot. Anvisningar om hur du återställer och hämta en säkerhetstoken finns [hämta en säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Läs om säkerhetstoken i allmänhet i [säkerhets- och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om inget anges används standard-Azure Integration Runtime. | Inte för källa och har Ja för sink om källan länkade tjänsten inte integration runtime |

>[!IMPORTANT]
>Standard Azure Integration Runtime kan inte användas för att köra kopia när du kopierar data till Salesforce. Med andra ord om käll-länkad-tjänsten inte har en angiven integration runtime explicit [skapa en Azure Integration körning](create-azure-integration-runtime.md#create-azure-ir) med en plats nära Salesforce-instans. Associera Salesforce länkade tjänst som i följande exempel.

**Exempel: Lagra autentiseringsuppgifter i Data Factory**

```json
{
    "name": "SalesforceLinkedService",
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
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: Lagra autentiseringsuppgifter i Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce-dataset.

Ange typegenskapen för dataset för att kopiera data från och till Salesforce, **SalesforceObject**. Följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **SalesforceObject**.  | Ja |
| objectApiName | Objektnamnet Salesforce att hämta data från. | Ingen datakälla Ja för sink |

> [!IMPORTANT]
> ”__C” en del av **API-namnet** krävs för alla anpassade objekt.

![Data Factory Salesforce anslutning namnet API: N](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exempel:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>För bakåtkompatibilitet: när du kopierar data från Salesforce, om du använder tidigare ”RelationalTable” typen dataset, behåller den fungerar när du ser ett förslag för att växla till den nya typen ”SalesforceObject”.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till **RelationalTable**. | Ja |
| tableName | Namnet på tabellen i Salesforce. | Nej (om ”fråga” i aktivitetskälla har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce källa och mottagare.

### <a name="salesforce-as-a-source-type"></a>Salesforce som typ av datakälla

Om du vill kopiera data från Salesforce som källtypen i kopieringsaktiviteten till **SalesforceSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till **SalesforceSource**. | Ja |
| DocumentDB |Använd anpassad fråga för att läsa data. Du kan använda en SQL-92-fråga eller [Salesforce objektet Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) frågan. Ett exempel är `select * from MyTable__c`. | Nej (om ”tabellnamn” i datamängden har angetts) |
| readBehavior | Anger om du vill fråga befintliga poster eller fråga efter alla poster, inklusive de borttagna. Om inget anges är standardbeteendet det tidigare. <br>Tillåtna värden: **frågan** (standard), **queryAll**.  | Nej |

> [!IMPORTANT]
> ”__C” en del av **API-namnet** krävs för alla anpassade objekt.

![Data Factory Salesforce anslutning API-namn](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>För bakåtkompatibilitet: när du kopierar data från Salesforce, om du använder tidigare ”RelationalSource” typen kopiera källan håller arbeta när du ser ett förslag för att växla till den nya typen ”SalesforceSource”.

### <a name="salesforce-as-a-sink-type"></a>Salesforce som en Mottagartypen

Om du vill kopiera data till Salesforce anger sink i kopieringsaktiviteten till **SalesforceSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till **SalesforceSink**. | Ja |
| WriteBehavior | Skriv beteendet för åtgärden.<br/>Tillåtna värden är **infoga** och **Upsert**. | Nej (standard är Insert) |
| externalIdFieldName | Namnet på det externa ID-fältet för upsert igen. Det angivna fältet måste definieras som ”externa Id-fältet” i Salesforce-objektet. Den kan inte ha NULL-värden i motsvarande indata. | Ja för ”Upsert” |
| writeBatchSize | Antalet rader med data som skrivs till Salesforce i varje batch. | Nej (standardvärdet är 5 000) |
| ignoreNullValues | Anger om du vill ignorera NULL-värden från indata vid skrivning.<br/>Tillåtna värden är **SANT** och **FALSKT**.<br>- **SANT**: ändra data i målobjektet inte när du gör en upsert eller update-åtgärd. Infoga ett definierat standardvärde när du gör en infogning.<br/>- **FALSKT**: uppdatera data i målobjektet till NULL när du gör en upsert eller update-åtgärd. Infoga värdet NULL när du gör en infogning. | Nej (standard är FALSKT) |

**Exempel: Salesforce mottagare i en Kopieringsaktivitet**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Frågan tips

### <a name="retrieve-data-from-a-salesforce-report"></a>Hämta data från en Salesforce-rapport

Du kan hämta data från Salesforce-rapporter genom att ange en fråga som `{call "<report name>"}`. Ett exempel är `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Hämta borttagna poster från Salesforce-Papperskorgen

Om du vill fråga ej permanent borttagna poster från Salesforce-Papperskorgen, kan du ange **”IsDeleted = 1”** i frågan. Exempel:

* Om du vill fråga endast borttagna poster, ange ”Välj * från MyTable__c **där IsDeleted = 1**”.
* Om du vill fråga alla poster, inklusive den befintliga och den borttagna, ange ”Välj * från MyTable__c **där IsDeleted = 0 eller IsDeleted = 1**”.

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Hämta data med hjälp av en where-sats för kolumnen datum/tid

När du anger SOQL eller SQL-frågan måste du vara uppmärksam på skillnaden för DateTime-format. Exempel:

* **SOQL exempel**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-exempel**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

## <a name="data-type-mapping-for-salesforce"></a>Mappning för Salesforce-datatyp

När du kopierar data från Salesforce används följande mappningar från Salesforce-datatyper till mellanliggande Data Factory-datatyper. Läs om hur kopieringsaktiviteten mappar källtypen schema och data till sink i [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md).

| Salesforce-datatyp | Data Factory tillfälliga datatyp |
|:--- |:--- |
| Automatisk tal |Sträng |
| Kryssruta |Boolesk |
| Valuta |dubbla |
| Date |DateTime |
| Datum/tid |DateTime |
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
| URL |Sträng |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
