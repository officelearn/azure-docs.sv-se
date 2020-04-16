---
title: Kopiera data från och till Salesforce Service Cloud
description: Lär dig hur du kopierar data från Salesforce Service Cloud till sink-datalager som stöds eller från källdatalager som stöds till Salesforce Service Cloud med hjälp av en kopieringsaktivitet i en datafabrikspipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: ec2aa5b1492534908adb55544623110242717609
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416680"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Kopiera data från och till Salesforce Service Cloud med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från och till Salesforce Service Cloud. Den bygger på översiktsartikeln [Kopiera aktivitet](copy-activity-overview.md) som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Salesforce Service Cloud-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Salesforce Service Cloud till alla sink-datalager som stöds. Du kan också kopiera data från alla källdatalager som stöds till Salesforce Service Cloud. En lista över datalager som stöds som källor eller sänkor av aktiviteten Kopiera finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här Salesforce Service Cloud-anslutningen stöder:

- Salesforce Developer, Professional, Enterprise eller Unlimited-utgåvor.
- Kopiera data från och till Salesforce produktion, sandlåda och anpassad domän.

Salesforce-kopplingen är byggd ovanpå Salesforce REST/Bulk API. Som standard använder kopplingen [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) för att kopiera data från Salesforce och använder [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) för att kopiera data till Salesforce. Du kan också uttryckligen ange den API-version [ `apiVersion` ](#linked-service-properties) som används för att läsa/skriva data via egenskap i länkad tjänst.

## <a name="prerequisites"></a>Krav

API-behörighet måste vara aktiverad i Salesforce. Mer information finns [i Aktivera API-åtkomst i Salesforce efter behörighetsgrupp](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Gränser för Salesforce-begäran

Salesforce har gränser för både totala API-begäranden och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begäranden överskrider gränsen sker begränsning och du ser slumpmässiga fel.
- Om det totala antalet begäranden överskrider gränsen blockeras Salesforce-kontot i 24 timmar.

Felmeddelandet "REQUEST_LIMIT_EXCEEDED" kan också visas i båda scenarierna. Mer information finns i avsnittet "API-begärandegränser" i [Salesforce-utvecklargränser .](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Salesforce Service Cloud-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den Salesforce-länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste anges till **SalesforceServiceCloud**. |Ja |
| miljöUrl | Ange URL:en för Salesforce Service Cloud-instansen. <br> - Standard `"https://login.salesforce.com"`är . <br> - Om du vill kopiera `"https://test.salesforce.com"`data från sandlådan anger du . <br> - Om du vill kopiera data från `"https://[domain].my.salesforce.com"`en anpassad domän anger du till exempel . |Inga |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| password |Ange ett lösenord för användarkontot.<br/><br/>Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| säkerhetToken |Ange en säkerhetstoken för användarkontot. <br/><br/>Mer information om säkerhetstoken i allmänhet finns i [Säkerhet och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). Säkerhetstoken kan bara hoppas över om du lägger till Integration Runtimes IP i [listan betrodd IP-adress](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) på Salesforce. När du använder Azure IR läser du [Azure Integration Runtime IP-adresser](azure-integration-runtime-ip-addresses.md).<br/><br/>Instruktioner om hur du hämtar och återställer en säkerhetstoken finns i [Hämta en säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Inga |
| apiVersion | Ange den Salesforce REST/Bulk API-version som `48.0`ska användas, t.ex. Som standard använder kopplingen [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) för att kopiera data från Salesforce och använder [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) för att kopiera data till Salesforce. | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Om det inte anges används standardkörningen för Azure Integration. | Nej för källa, Ja för mottagare om den källlänkade tjänsten inte har integrationskörningtid |

>[!IMPORTANT]
>När du kopierar data till Salesforce Service Cloud kan standardkörningen för Azure Integration Runtime inte användas för att köra kopia. Med andra ord, om din källlänkade tjänst inte har en angiven integrationskörning, skapa uttryckligen [en Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) med en plats nära din Salesforce Service Cloud-instans. Associera den länkade tjänsten Salesforce Service Cloud som i följande exempel.

**Exempel: Lagra autentiseringsuppgifter i Data Factory**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
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
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce Service Cloud-datauppsättningen.

Om du vill kopiera data från och till Salesforce Service Cloud stöds följande egenskaper.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **SalesforceServiceCloudObject**.  | Ja |
| objectApiName | Det Salesforce-objektnamnet som data ska hämtas från. | Nej för källa, Ja för diskho |

> [!IMPORTANT]
> Den "__c" delen av **API-namn** behövs för alla anpassade objekt.

![API-namn för data fabriksförsäljningskraft](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exempel:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för datauppsättningen måste anges till **RelationalTable**. | Ja |
| tableName | Namnet på tabellen i Salesforce Service Cloud. | Nej (om "fråga" i aktivitetskällan har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce Service Cloud källa och diskho.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Salesforce Service Cloud som källtyp

Om du vill kopiera data från Salesforce Service Cloud stöds följande egenskaper i avsnittet kopiera **aktivitetskälla.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till **SalesforceServiceCloudSource**. | Ja |
| DocumentDB |Använd den anpassade frågan för att läsa data. Du kan använda [SOQL-frågan (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) eller SQL-92-frågan. Se fler tips i avsnittet [frågetips.](#query-tips) Om frågan inte anges hämtas alla data för det Salesforce Service Cloud-objekt som anges i "objectApiName" i datauppsättningen. | Nej (om "objectApiName" i datauppsättningen har angetts) |
| läsBeteende | Anger om de befintliga posterna ska frågas eller fråga alla poster, inklusive de borttagna. Om inget anges är standardbeteendet det förstnämnda. <br>Tillåtna värden: **fråga** (standard), **queryAll**.  | Inga |

> [!IMPORTANT]
> Den "__c" delen av **API-namn** behövs för alla anpassade objekt.

![Namnlista för data fabriksförsäljningsfognings-api](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce Service Cloud som en sink-typ

Om du vill kopiera data till Salesforce Service Cloud stöds följande egenskaper i avsnittet kopiera **aktivitetsmottagare.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för copy activity sink måste anges till **SalesforceServiceCloudSink**. | Ja |
| skriverBeteende | Skrivbeteendet för åtgärden.<br/>Tillåtna värden är **Infoga** och **Upsert**. | Nej (standard är Infoga) |
| externtIdFieldName | Namnet på det externa ID-fältet för den senaste åtgärden. Det angivna fältet måste definieras som "Externt ID-fält" i Salesforce Service Cloud-objektet. Det kan inte finnas NULL-värden i motsvarande indata. | Ja för "Upsert" |
| skriverBatchSize | Radantalet data som skrivits till Salesforce Service Cloud i varje batch. | Nej (standard är 5 000) |
| ignoreraNullValues | Anger om NULL-värden ska ignoreras från indata under en skrivåtgärd.<br/>Tillåtna värden är **sanna** och **falska**.<br>- **Sant**: Lämna data i målobjektet oförändrade när du gör en upsert- eller uppdateringsåtgärd. Infoga ett definierat standardvärde när du gör en infogad åtgärd.<br/>- **Falskt**: Uppdatera data i målobjektet till NULL när du gör en upsert- eller uppdateringsåtgärd. Infoga ett NULL-värde när du gör en infogad åtgärd. | Nej (standard är falskt) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Tips för frågor

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Hämta data från en Cloudforce-tjänstmolnsrapport

Du kan hämta data från Salesforce Service Cloud-rapporter genom att ange en fråga som `{call "<report name>"}`. Ett exempel är `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Hämta borttagna poster från Salesforce Service Cloud Recycle Bin

Om du vill fråga de mjuka borttagna posterna från `readBehavior` `queryAll`Salesforce Service Cloud Recycle Bin kan du ange som . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Skillnad mellan SOQL- och SQL-frågesyntax

När du kopierar data från Salesforce Service Cloud kan du använda antingen SOQL-fråga eller SQL-fråga. Observera att dessa två har olika syntax och funktionalitet stöd, inte blanda det. Du föreslås använda SOQL-frågan som stöds internt av Salesforce Service Cloud. I följande tabell visas de viktigaste skillnaderna:

| Syntax | SOQL-läge | SQL-läge |
|:--- |:--- |:--- |
| Kolumnval | Måste räkna upp de fält som ska kopieras i frågan, t.ex.`SELECT field1, filed2 FROM objectname` | `SELECT *`stöds förutom kolumnval. |
| Citattecken | Arkiverade/objektnamn kan inte anges. | Fält-/objektnamn kan anges, t.ex.`SELECT "id" FROM "Account"` |
| Format för datumtid |  Se detaljer [här](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) och exempel i nästa avsnitt. | Se detaljer [här](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) och exempel i nästa avsnitt. |
| Boolesk värden | Representeras `False` som `True`och , `SELECT … WHERE IsDeleted=True`t.ex. | Representerad som 0 eller 1, `SELECT … WHERE IsDeleted=1`t.ex. |
| Kolumnbyte | Stöds inte. | Stöds, t.ex.: `SELECT a AS b FROM …`. |
| Relation | Stöds, t.ex. `Account_vod__r.nvs_Country__c` | Stöds inte. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Hämta data med hjälp av en where-sats i kolumnen DateTime

När du anger SOQL- eller SQL-frågan bör du vara uppmärksam på formatskillnaden datetime. Ett exempel:

* **SOQL exempel:**`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-exempel:**`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Fel på MALFORMED_QUERY:Trunkerad

Om du stöter på fel i "MALFORMED_QUERY: Trunkerad", normalt beror det på att du har JunctionIdList typ kolumn i data och Salesforce har begränsning på att stödja sådana data med stort antal rader. Om du vill minska försöker du utesluta kolumnen JunctionIdList eller begränsa antalet rader som ska kopieras (du kan partitionera till flera kopieringsaktivitetskörningar).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mappning av datatyp för Salesforce Service Cloud

När du kopierar data från Salesforce Service Cloud används följande mappningar från Salesforce Service Cloud-datatyper till interimsdatatyper för Data Factory. Mer information om hur kopieringsaktiviteten mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

| Datatyp för Salesforce Service Cloud | Data Factory interimdatatyp |
|:--- |:--- |
| Auto nummer |Sträng |
| Checkbox |Boolesk |
| Valuta |Decimal |
| Date |DateTime |
| Date/Time |DateTime |
| E-post |Sträng |
| Id |Sträng |
| Uppslagsrelation |Sträng |
| Flervalsplocklista |Sträng |
| Tal |Decimal |
| Procent |Decimal |
| Telefon |Sträng |
| Picklist (Plocklista) |Sträng |
| Text |Sträng |
| Textområde |Sträng |
| Textområde (långt) |Sträng |
| Textområde (rich) |Sträng |
| Text (krypterad) |Sträng |
| URL |Sträng |

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns i [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
