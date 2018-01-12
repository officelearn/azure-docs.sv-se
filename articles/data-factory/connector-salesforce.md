---
title: "Kopiera data från/till Salesforce med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från Salesforce till stöds sink datalager (eller) från stöds källa datalager till Salesforce med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 7cd86922b0445fc81766ca54080e2fd3e64a6c61
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-fromto-salesforce-using-azure-data-factory"></a>Kopiera data från/till Salesforce med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-salesforce-connector.md)
> * [Version 2 – förhandsversion](connector-salesforce.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från och till Salesforce. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Salesforce-anslutningsprogrammet i V1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Salesforce till alla mottagare som stöds i datalagret eller eller kopiera data från alla datalager stöds källa till Salesforce. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder denna Salesforce-koppling:

- Följande versioner av Salesforce: **Developer Edition, Professional Edition, Enterprise Edition eller obegränsade Edition**.
- Kopiera data från/till Salesforce **produktion, sandbox och domänen**.

## <a name="prerequisites"></a>Förutsättningar

* API-behörighet måste vara aktiverat i Salesforce. Se [hur aktivera API-åtkomst i Salesforce av behörighetsgrupp?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce begärandebegränsningar

Salesforce har begränsningar för både förfrågningarna API och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begäranden överskrider gränsen, begränsning inträffar och du ser slumpmässiga fel.
- Om det totala antalet begäranden överskrider gränsen, blockeras Salesforce-konto i 24 timmar.

Du kan också få ”REQUEST_LIMIT_EXCEEDED”-fel i båda scenarierna. Se avsnittet ”API Begärandebegränsningar” i den [Salesforce Developer gränser](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) artikeln för information.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Salesforce-anslutningsprogrammet.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Salesforce länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen type måste anges till: **Salesforce**. |Ja |
| environmentUrl | Ange URL: en för Salesforce-instans. <br> -Standard är `"https://login.salesforce.com"`. <br> -Om du vill kopiera data från sandbox, ange `"https://test.salesforce.com"`. <br> -Om du vill kopiera data från domänen, till exempel ange, `"https://[domain].my.salesforce.com"`. |Nej |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| lösenord |Ange ett lösenord för användarkontot.<br/><br/>Du kan välja att markera det här fältet som en SecureString att lagra den säkert i ADF eller lagra lösenord i Azure Key Vault och kan kopiera aktivitet hämtar därifrån vid kopiering av data - mer information från [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md). |Ja |
| securityToken |Ange en säkerhetstoken för användarkontot. Se [hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställa/hämta en säkerhetstoken. Läs om säkerhetstoken i allmänhet i [säkerhets- och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Du kan välja att markera det här fältet som en SecureString att lagra den säkert i ADF eller lagra säkerhetstoken i Azure Key Vault och kan kopiera aktivitet hämtar därifrån vid kopiering av data - mer information från [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om inget anges används standard-Azure Integration Runtime. | Inte för källa och har Ja för sink om källa länkade tjänsten inte IR |

>[!IMPORTANT]
>När du kopierar data **till** Salesforce standard Azure Integration körning inte kan användas för att köra kopia. I andra word om käll-länkad-tjänsten inte har en angiven IR explicit [skapa ett Azure-IR](create-azure-integration-runtime.md#create-azure-ir) med en plats nära dina Salesforce och koppla i Salesforce länkade tjänsten som i följande exempel.

**Exempel: lagra autentiseringsuppgifter i ADF**

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

**Exempel: lagra autentiseringsuppgifter i Azure Key Vault**

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

Ange typegenskapen för dataset för att kopiera data från/till Salesforce, **SalesforceObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **SalesforceObject**  | Ja |
| objectApiName | Objektnamnet Salesforce att hämta data från. | Ingen datakälla Ja för sink |

> [!IMPORTANT]
> ”__C”-delen av API-namnet krävs för alla anpassade objekt.

![Namnet på data Factory - anslutning Salesforce - API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

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
>För den bakre compatibiliy, när du kopierar data från Salesforce, använder tidigare ”RelationalTable” typen dataset kommer fortsätta att arbeta, medan du rekommenderas för att växla till den nya typen ”SalesforceObject”.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i Salesforce. | Nej (om ”fråga” i aktivitetskälla har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce källa och mottagare.

### <a name="salesforce-as-source"></a>Salesforce som källa

Om du vill kopiera data från Salesforce som källtypen i kopieringsaktiviteten till **SalesforceSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **SalesforceSource** | Ja |
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
>För den bakre compatibiliy, när du kopierar data från Salesforce, med hjälp av föregående ”RelationalSource” typ kopieringskälla kommer fortsätta att arbeta, medan du rekommenderas för att växla till den nya typen ”SalesforceSource”.

### <a name="salesforce-as-sink"></a>Salesforce som mottagare

Om du vill kopiera data till Salesforce anger sink i kopieringsaktiviteten till **SalesforceSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till: **SalesforceSink** | Ja |
| WriteBehavior | Skriv beteendet för åtgärden.<br/>Tillåtna värden är: **infoga**, och **Upsert**. | Nej (standard är Insert) |
| externalIdFieldName | Namnet på det externa ID-fältet för upsert igen. Det angivna fältet måste vara definierade som ”externa Id-fältet” i Salesforce-objekt och kan inte ha NULL-värden i motsvarande indata. | Ja för ”Upsert” |
| writeBatchSize | Antalet rader med data som skrivs till Salesforce i varje batch. | Nej (standard är 5 000) |
| ignoreNullValues | Anger om för att ignorera null-värden från indata under skrivåtgärd.<br/>Tillåtna värden är: **SANT**, och **FALSKT**.<br>- **SANT**: lämna data i målet objekt ändras när du gör upsert/uppdateringsåtgärden och infoga definierat standardvärde när gör insert-åtgärden.<br/>- **FALSKT**: uppdatera data i målobjektet till NULL när du gör upsert/uppdateringsåtgärden och infoga NULL-värde när du gör insert-åtgärden. | Nej (standard är FALSKT) |

### <a name="example-salesforce-sink-in-copy-activity"></a>Exempel: Salesforce mottagare i en Kopieringsaktivitet

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

### <a name="retrieving-data-from-salesforce-report"></a>Hämtar data från Salesforce-rapport

Du kan hämta data från Salesforce-rapporter genom att ange frågan som `{call "<report name>"}`. Exempel: `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Hämta borttagna poster från Salesforce-Papperskorgen

Om du vill fråga ej permanent borttagna poster från Salesforce-Papperskorgen, kan du ange **”IsDeleted = 1”** i frågan. Exempel:

* Om du vill fråga endast borttagna poster, ange ”Välj * från MyTable__c **där IsDeleted = 1**”
* Om du vill fråga efter alla poster som inklusive det befintliga och den borttagna, ange ”Välj * från MyTable__c **där IsDeleted = 0 eller IsDeleted = 1**”

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Hämtning av data med hjälp av where-sats för kolumnen datum/tid

När ange SOQL eller SQL-fråga, titta närmare på skillnaden för DateTime-format. Exempel:

* **SOQL exempel**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-exempel**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}"`

## <a name="data-type-mapping-for-salesforce"></a>Mappning för Salesforce-datatyp

När du kopierar data från Salesforce, används följande mappningar från Salesforce-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Salesforce-datatyp | Data factory tillfälliga datatyp |
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
| Webbadress |Sträng |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).