---
title: Kopiera data från och till Salesforce med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Salesforce till mottagarens datalager eller datalager som stöds till Salesforce med en Kopieringsaktivitet i en data factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: jingwang
ms.openlocfilehash: 7550eac600f5b504d80bcc6b5465e24e8d423d2a
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015091"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Kopiera data från och till Salesforce med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](v1/data-factory-salesforce-connector.md)
> * [Aktuell version](connector-salesforce.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från och till Salesforce. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Salesforce till alla datalager för mottagare som stöds. Du kan också kopiera data från alla dataarkiv till Salesforce. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Salesforce-anslutningen:

- Salesforce-utvecklare, Professional, Enterprise eller obegränsad-utgåvor.
- Kopiera data från och till Salesforce produktion, sandbox-miljön och anpassad domän.

Salesforce-anslutningen har byggts ovanpå Salesforce REST-API med [v39](https://developer.salesforce.com/docs/atlas.en-us.206.0.api_rest.meta/api_rest/resources_list.htm) för kopieringsdata från och [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) för kopieringsdata till.

## <a name="prerequisites"></a>Förutsättningar

API-behörighet måste vara aktiverat i Salesforce. Mer information finns i [aktivera API-åtkomst i Salesforce av behörighetsuppsättning](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce-begärandebegränsningar

Salesforce har gränser för både Totalt antal API-begäranden och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begäranden överskrider gränsen, begränsning inträffar och du ser slumpmässiga fel.
- Om det totala antalet begäranden överskrider gränsen, blockeras Salesforce-konto i 24 timmar.

Du kan också få felmeddelandet ”REQUEST_LIMIT_EXCEEDED” i båda scenarierna. Mer information finns i avsnittet ”API-begärandebegränsningar” i [Salesforce developer gränser](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Salesforce-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Salesforce-länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Type-egenskapen måste anges till **Salesforce**. |Ja |
| environmentUrl | Ange URL: en för Salesforce-instans. <br> – Standardvärdet är `"https://login.salesforce.com"`. <br> – Om du vill kopiera data från sandbox, ange `"https://test.salesforce.com"`. <br> – Om du vill kopiera data från anpassad domän, ange, till exempel `"https://[domain].my.salesforce.com"`. |Nej |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| lösenord |Ange ett lösenord för användarkontot.<br/><br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| securityToken |Ange en säkerhetstoken för användarkontot. Instruktioner om hur du återställer och hämta en säkerhetstoken finns [hämta en säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Läs mer om säkerhetstoken i allmänhet i [säkerhets- och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om den inte anges används standard Azure Integration Runtime. | Inte för källa har Ja för kanalmottagare om källan länkade tjänsten inte integreringskörning |

>[!IMPORTANT]
>Standard Azure Integration Runtime kan inte användas för att köra kopia när du kopierar data till Salesforce. Med andra ord om källan länkade tjänsten har inte en angiven integreringskörning uttryckligen [skapa en Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) med en plats nära din Salesforce-instans. Associera Salesforce länkade tjänsten som i följande exempel.

**Exempel: Store-autentiseringsuppgifter i Data Factory**

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

**Exempel: Store-autentiseringsuppgifter i Key Vault**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce-datauppsättning.

För att kopiera data från och till Salesforce, ange typegenskapen på datauppsättningen till **SalesforceObject**. Följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **SalesforceObject**.  | Ja |
| objectApiName | Salesforce-objektnamn att hämta data från. | Nej för källa, Ja för mottagare |

> [!IMPORTANT]
> Den ”__c”-delen av **API-namn** krävs för alla anpassade objekt.

![Data Factory Salesforce-anslutning API-namn](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

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
>För bakåtkompatibilitet: När du kopierar data från Salesforce, om du använder den tidigare ”RelationalTable” typ av datauppsättningen ser till att det fungerar även om du ser ett förslag att växla till den nya ”SalesforceObject”-typen.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till **RelationalTable**. | Ja |
| tableName | Namnet på tabellen i Salesforce. | Nej (om ”query” i källan för aktiviteten har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce-källan och mottagaren.

### <a name="salesforce-as-a-source-type"></a>Salesforce som en typ av datakälla

För att kopiera data från Salesforce, ange typ av datakälla i kopieringsaktiviteten till **SalesforceSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till **SalesforceSource**. | Ja |
| DocumentDB |Använd anpassad fråga för att läsa data. Du kan använda [Salesforce objektet Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) fråge- eller SQL-92 fråga. Se fler tips i [fråga tips](#query-tips) avsnittet. Om frågan inte anges, hämtas alla data i Salesforce-objekt som angetts i ”objectApiName” i datauppsättningen. | Nej (om ”objectApiName” i datauppsättningen har angetts) |
| readBehavior | Anger om du vill fråga efter befintliga poster eller fråga efter alla poster, inklusive de har tagits bort. Om inte anges är standardbeteendet för den tidigare versionen. <br>Tillåtna värden: **fråga** (standard), **queryAll**.  | Nej |

> [!IMPORTANT]
> Den ”__c”-delen av **API-namn** krävs för alla anpassade objekt.

![Data Factory Salesforce-anslutning API namnlistan](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>För bakåtkompatibilitet: När du kopierar data från Salesforce, om du använder den föregående ”RelationalSource” typ kopian håller källan fungerar även om du ser ett förslag att växla till den nya ”SalesforceSource”-typen.

### <a name="salesforce-as-a-sink-type"></a>Salesforce som en Mottagartyp

För att kopiera data till Salesforce, ange Mottagartyp i kopieringsaktiviteten till **SalesforceSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **SalesforceSink**. | Ja |
| WriteBehavior | Skriv beteendet för åtgärden.<br/>Tillåtna värden är **infoga** och **Upsert**. | Nej (standard är Insert) |
| externalIdFieldName | Namnet på det externa ID-fältet för upsert-åtgärden. Det angivna fältet måste definieras som ”externa Id-fältet” i Salesforce-objekt. Det kan inte ha NULL-värden i motsvarande indata. | Ja för ”Upsert” |
| WriteBatchSize | Antal rader för data som skrivs till Salesforce i varje batch. | Nej (standardvärdet är 5 000) |
| ignoreNullValues | Anger om du vill ignorera NULL-värden från indata vid skrivning.<br/>Tillåtna värden är **SANT** och **FALSKT**.<br>- **SANT**: Lämna data i målobjektet oförändrade när du gör en upsert eller update-åtgärd. Infoga ett definierat standardvärde när du gör en insert-åtgärd.<br/>- **FALSKT**: Uppdatera data i målobjektet till NULL när du gör en upsert eller update-åtgärd. Infoga värdet NULL när du gör en insert-åtgärd. | Nej (standard är FALSKT) |

**Exempel: Salesforce-mottagare i en Kopieringsaktivitet**

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

## <a name="query-tips"></a>Frågetips

### <a name="retrieve-data-from-a-salesforce-report"></a>Hämta data från en Salesforce-rapport

Du kan hämta data från Salesforce-rapporter genom att ange en fråga som `{call "<report name>"}`. Ett exempel är `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Hämta borttagna poster från Salesforce-Papperskorgen

Om du vill fråga de ej permanent borttagna posterna från Salesforce-Papperskorgen, kan du ange `readBehavior` som `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Skillnaden mellan SOQL och SQL-frågesyntaxen

När du kopierar data från Salesforce, kan du använda antingen SOQL frågan eller SQL-fråga. Observera att dessa två har olika syntax och stöd för funktioner, inte blanda. Du rekommenderas för att använda SOQL frågan som stöds internt av Salesforce. I följande tabell visas de viktigaste skillnaderna:

| Syntax | SOQL läge | SQL-läge |
|:--- |:--- |:--- |
| Kolumnurval | Måste du ange de fält som ska kopieras i frågan, t.ex. `SELECT field1, filed2 FROM objectname` | `SELECT *` stöds både kolumnen. |
| Citattecken | Arkiverat/objektnamn kan inte innehålla citattecken. | Fältet/objektnamn kan innehålla citattecken, t.ex. `SELECT "id" FROM "Account"` |
| DateTime-format |  Finns [här](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) och exempel i nästa avsnitt. | Finns [här](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) och exempel i nästa avsnitt. |
| Booleska värden | Visas som `False` och `True`, t.ex. `SELECT … WHERE IsDeleted=True`. | Visas som 0 eller 1, t.ex. `SELECT … WHERE IsDeleted=1`. |
| Byta namn på kolumn | Stöds ej. | Stöds, t.ex.: `SELECT a AS b FROM …`. |
| Relation | Stöds, t.ex. `Account_vod__r.nvs_Country__c`. | Stöds ej. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Hämta data med hjälp av en where-sats i DateTime-kolumn

När du anger SOQL eller SQL-frågan kan du vara uppmärksam på skillnaden för DateTime-format. Exempel:

* **SOQL exempel**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-exemplet**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

## <a name="data-type-mapping-for-salesforce"></a>Datatypen mappning för Salesforce

När du kopierar data från Salesforce, används följande mappningar från Salesforce-datatyper till Data Factory tillfälliga-datatyper. Mer information om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren, se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

| Salesforce-datatypen | Data Factory tillfälliga datatyp |
|:--- |:--- |
| Automatisk numrering |Sträng |
| Kryssrutan |Boolesk |
| Valuta |Decimal |
| Date |DateTime |
| Datum/tid |DateTime |
| E-post |Sträng |
| Id |Sträng |
| Uppslagsrelation |Sträng |
| Flervalsplocklista |Sträng |
| Tal |Decimal |
| Procent |Decimal |
| Telefon |Sträng |
| Listruta |Sträng |
| Text |Sträng |
| Textområde |Sträng |
| Textområde (Long) |Sträng |
| Textområde (Rich) |Sträng |
| Text (krypterad) |Sträng |
| URL |Sträng |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
