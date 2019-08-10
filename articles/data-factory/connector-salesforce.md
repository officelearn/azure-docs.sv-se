---
title: Kopiera data från och till Salesforce genom att använda Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Salesforce till mottagar data lager eller från käll data lager som stöds till Salesforce genom att använda en kopierings aktivitet i en Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 625f31252942c3d8dea9ca9b4772af19f60e17ab
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720716"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Kopiera data från och till Salesforce med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-salesforce-connector.md)
> * [Aktuell version](connector-salesforce.md)

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Salesforce. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Salesforce till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till Salesforce. En lista över data lager som stöds som källor eller mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här Salesforce-anslutningen:

- Salesforce-utvecklare, Professional, Enterprise eller obegränsade versioner.
- Kopiera data från och till Salesforce-produktion, Sandbox och anpassad domän.

Salesforce-anslutningen bygger på Salesforce REST/bulk-API: et, med [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) för kopierings data från och [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) för att kopiera data till.

## <a name="prerequisites"></a>Förutsättningar

API-behörighet måste vara aktiverat i Salesforce. Mer information finns i [Aktivera API-åtkomst i Salesforce med behörighets uppsättning](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Begränsningar för Salesforce-begäran

Salesforce har gränser för både totalt antal API-begäranden och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begär Anden överskrider gränsen inträffar begränsningen och slumpmässiga fel visas.
- Om det totala antalet begär Anden överskrider gränsen blockeras Salesforce-kontot i 24 timmar.

Du kan också få fel meddelandet "REQUEST_LIMIT_EXCEEDED" i båda scenarierna. Mer information finns i avsnittet "begränsningar för API-begäranden" i [gränser för Salesforce-utvecklare](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Salesforce-kopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade Salesforce-tjänsten.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type |Egenskapen Type måste anges till **Salesforce**. |Ja |
| environmentUrl | Ange URL: en för Salesforce-instansen. <br> – Standardvärdet är `"https://login.salesforce.com"`. <br> – Om du vill kopiera data från sandbox `"https://test.salesforce.com"`anger du. <br> – Om du vill kopiera data från en anpassad domän anger du till `"https://[domain].my.salesforce.com"`exempel. |Nej |
| username |Ange ett användar namn för användar kontot. |Ja |
| password |Ange ett lösen ord för användar kontot.<br/><br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| securityToken |Ange en säkerhetstoken för användar kontot. Instruktioner för hur du återställer och hämtar en säkerhetstoken finns i [Hämta en säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Mer information om säkerhetstoken i allmänhet finns i [säkerhet och API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om den inte anges används standard Azure Integration Runtime. | Nej för källa, Ja för mottagare om den länkade käll tjänsten inte har integration runtime |

>[!IMPORTANT]
>När du kopierar data till Salesforce kan du inte använda standard Azure Integration Runtime för att köra kopiering. Med andra ord, om den länkade käll tjänsten inte har en angiven integration runtime, skapar du uttryckligen [en Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) med en plats nära din Salesforce-instans. Associera Salesforce-länkade tjänst som i följande exempel.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce-datauppsättningen.

Om du vill kopiera data från och till Salesforce anger du egenskapen type för data uppsättningen till **SalesforceObject**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till **SalesforceObject**.  | Ja |
| objectApiName | Salesforce-objektets namn att hämta data från. | Nej för källa, Ja för mottagare |

> [!IMPORTANT]
> Delen "__c" i **API-namn** krävs för alla anpassade objekt.

![Data Factory API-namn för Salesforce-anslutning](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exempel:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>För bakåtkompatibilitet: När du kopierar data från Salesforce, om du använder den tidigare data uppsättningen av typen "RelationalTable", fortsätter den att fungera medan du ser ett förslag för att växla till den nya typen "SalesforceObject".

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till **RelationalTable**. | Ja |
| tableName | Namnet på tabellen i Salesforce. | Nej (om "fråga" i aktivitets källan har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce-källa och mottagare.

### <a name="salesforce-as-a-source-type"></a>Salesforce som typ av källa

Om du vill kopiera data från Salesforce anger du käll typen i kopierings aktiviteten till **SalesforceSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för kopierings aktivitets källan måste anges till **SalesforceSource**. | Ja |
| query |Använd den anpassade frågan för att läsa data. Du kan använda [SOQL-](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) fråga eller SQL-92-fråga för Salesforce-objekt frågor. Se fler tips i avsnittet [tips](#query-tips) . Om fråga inte anges hämtas alla data för Salesforce-objektet som anges i "objectApiName" i data uppsättningen. | Nej (om "objectApiName" i data uppsättningen har angetts) |
| readBehavior | Indikerar om du vill fråga befintliga poster eller fråga alla poster inklusive borttagna. Om inget värde anges är standard beteendet det tidigare. <br>Tillåtna värden: **fråga** (standard), **queryAll**.  | Nej |

> [!IMPORTANT]
> Delen "__c" i **API-namn** krävs för alla anpassade objekt.

![Lista med Data Factory API-namn för Salesforce-anslutning](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>För bakåtkompatibilitet: När du kopierar data från Salesforce, och använder den tidigare kopian av typen "RelationalSource", fortsätter källan att fungera medan du ser ett förslag för att växla till den nya typen "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce som mottagar typ

Om du vill kopiera data till Salesforce ställer du in mottagar typen i kopierings aktiviteten till **SalesforceSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type för kopierings aktivitetens Sink måste anges till **SalesforceSink**. | Ja |
| writeBehavior | Skriv beteendet för åtgärden.<br/>Tillåtna värden är **insert** -och **upsert**. | Nej (standard är Insert) |
| externalIdFieldName | Namnet på det externa ID-fältet för upsert-åtgärden. Det angivna fältet måste definieras som "externt ID-fält" i Salesforce-objektet. Det får inte ha NULL-värden i motsvarande indata. | Ja för "upsert" |
| writeBatchSize | Rad antalet data som skrivs till Salesforce i varje batch. | Nej (standard är 5 000) |
| ignoreNullValues | Anger om NULL-värden ska ignoreras från indata under en Skriv åtgärd.<br/>Tillåtna värden är **True** och **false**.<br>- **Sant**: Lämna kvar data i målobjektet oförändrade när du gör en upsert-eller uppdaterings åtgärd. Infoga ett definierat standardvärde när du infogar en åtgärd.<br/>- **Falskt**: Uppdatera data i målobjektet till NULL när du gör en upsert-eller uppdaterings åtgärd. Infoga ett NULL-värde när du gör en infognings åtgärd. | Nej (standard är falskt) |

**Exempel: Salesforce-mottagare i en kopierings aktivitet**

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

## <a name="query-tips"></a>Tips om frågor

### <a name="retrieve-data-from-a-salesforce-report"></a>Hämta data från en Salesforce-rapport

Du kan hämta data från Salesforce-rapporter genom att ange en `{call "<report name>"}`fråga som. Ett exempel är `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Hämta borttagna poster från pappers korgen i Salesforce

Om du vill fråga de Soft borttagna posterna från pappers korgen i Salesforce kan du `readBehavior` ange `queryAll`som. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Skillnad mellan SOQL och SQL-frågesyntax

När du kopierar data från Salesforce kan du använda antingen SOQL Query eller SQL-fråga. Observera att dessa två har olika funktioner för syntax och funktioner, blanda inte. Du rekommenderas att använda SOQL-frågan som stöds internt av Salesforce. I följande tabell visas de viktigaste skillnaderna:

| Syntax | SOQL-läge | SQL-läge |
|:--- |:--- |:--- |
| Kolumn val | Du måste räkna upp fälten som ska kopieras i frågan, t. ex.`SELECT field1, filed2 FROM objectname` | `SELECT *`stöds utöver val av kolumn. |
| Citat tecken | Namn på arkiverade/objekt får inte anges i citat tecken. | Namn på fält/objekt kan anges i citat tecken, t. ex.`SELECT "id" FROM "Account"` |
| Datetime-format |  Mer information finns [här](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) och exempel i nästa avsnitt. | Mer information finns [här](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) och exempel i nästa avsnitt. |
| Booleska värden | `False` Representerat `True`som och, `SELECT … WHERE IsDeleted=True`t. ex. | Representeras som 0 eller 1, `SELECT … WHERE IsDeleted=1`t. ex. |
| Ändra kolumn namn | Stöds ej. | Stöds, t. ex `SELECT a AS b FROM …`.:. |
| Relation | Stöds, t. `Account_vod__r.nvs_Country__c`ex. | Stöds ej. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Hämta data med hjälp av en WHERE-sats i kolumnen DateTime

När du anger SOQL eller SQL-frågan bör du tänka på DateTime-formatets skillnad. Exempel:

* **SOQL-exempel**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-exempel**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformedquerytruncated"></a>Fel i MALFORMED_QUERY: trunkerad

Om du trycker på fel "MALFORMED_QUERY: Trunkerad ", vanligt vis på grund av att du har JunctionIdList typ kolumn i data och Salesforce har begränsningar för att stödja sådana data med ett stort antal rader. Du kan undvika detta genom att försöka undanta JunctionIdList-kolumnen eller begränsa antalet rader som ska kopieras (du kan partitionera till flera kopierings aktivitets körningar).

## <a name="data-type-mapping-for-salesforce"></a>Data typs mappning för Salesforce

När du kopierar data från Salesforce används följande mappningar från Salesforce-datatyper för att Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren finns i [schema-och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| Salesforce-datatyp | Data Factory tillfälliga datatyp |
|:--- |:--- |
| Auto Number |String |
| Checkbox |Boolean |
| Currency |Decimal |
| Date |DateTime |
| Date/Time |DateTime |
| Email |String |
| Id |String |
| Lookup Relationship |String |
| Multi-Select Picklist |String |
| Number |Decimal |
| Percent |Decimal |
| Phone |String |
| Picklist |String |
| Text |String |
| Text Area |String |
| Text Area (Long) |String |
| Text Area (Rich) |String |
| Text (Encrypted) |String |
| URL |String |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
