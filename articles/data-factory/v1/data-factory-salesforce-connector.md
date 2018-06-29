---
title: Flytta data från Salesforce med hjälp av Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från Salesforce med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 98f7494c87683eda858da5970e12073ce2204303
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048295"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Flytta data från Salesforce med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-salesforce-connector.md)
> * [Version 2 (aktuell version)](../connector-salesforce.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [Salesforce-anslutningsprogrammet i V2](../connector-salesforce.md).


Den här artikeln beskrivs hur du kan använda Kopieringsaktiviteten i ett Azure data factory för att kopiera data från Salesforce till alla datalager som anges under kolumnen mottagare i den [källor och sänkor stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Den här artikeln bygger på den [data movement aktiviteter](data-factory-data-movement-activities.md) artikel som ger en allmän översikt över dataflyttning stöds data store kombinationer och Kopieringsaktivitet.

Azure Data Factory stöder för närvarande endast flytta data från Salesforce till [stöds sink datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats), men inte stöder att flytta data från andra data lagras till Salesforce.

## <a name="supported-versions"></a>Versioner som stöds
Den här anslutningen har stöd för följande versioner av Salesforce: Developer Edition, Professional Edition, Enterprise Edition eller obegränsade Edition. Och kopiera från Salesforce produktion, sandbox och anpassade domäner.

## <a name="prerequisites"></a>Förutsättningar
* API-behörighet måste aktiveras. Se [hur aktivera API-åtkomst i Salesforce av behörighetsgrupp?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Om du vill kopiera data från Salesforce till lokala datalager, du har Data Management Gateway 2.0 är installerat i din lokala miljö.

## <a name="salesforce-request-limits"></a>Salesforce begärandebegränsningar
Salesforce har begränsningar för både förfrågningarna API och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begäranden överskrider gränsen, begränsning inträffar och slumpmässiga fel visas.
- Om det totala antalet begäranden överskrider gränsen, blockeras Salesforce-konto i 24 timmar.

Du kan också få ”REQUEST_LIMIT_EXCEEDED”-fel i båda scenarierna. Se avsnittet ”API Begärandebegränsningar” i den [Salesforce Developer gränser](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) artikeln för information.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från Salesforce med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall** , **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare: 

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från Salesforce finns [JSON-exempel: kopiera data från Salesforce till Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till Salesforce: 

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller beskrivningar för JSON-element som är specifika för Salesforce länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **Salesforce**. |Ja |
| environmentUrl | Ange URL: en för Salesforce-instans. <br><br> – Standardvärdet är ”https://login.salesforce.com”. <br> -Om du vill kopiera data från sandbox, ange ”https://test.salesforce.com”. <br> -Om du vill kopiera data från domänen, ange, till exempel ”https://[domain].my.salesforce.com”. |Nej |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| lösenord |Ange ett lösenord för användarkontot. |Ja |
| securityToken |Ange en säkerhetstoken för användarkontot. Se [hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställa/hämta en säkerhetstoken. Läs om säkerhetstoken i allmänhet i [säkerhets- och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningar och avsnitt, finns det [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen (Azure SQL Azure blob, Azure-tabellen och så vidare).

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. TypeProperties avsnittet för en dataset av typen **RelationalTable** har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Salesforce. |Nej (om en **frågan** av **RelationalSource** har angetts) |

> [!IMPORTANT]
> ”__C”-delen av API-namnet krävs för alla anpassade objekt.

![Namnet på data Factory - anslutning Salesforce - API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, indata och utdata tabeller och olika principer är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties aktiviteten å andra sidan varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

I en Kopieringsaktivitet när källan är av typen **RelationalSource** (som omfattar Salesforce), följande egenskaper finns i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |En SQL-92-fråga eller [Salesforce objektet Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) frågan. Till exempel: `select * from MyTable__c`. |Nej (om den **tableName** av den **dataset** har angetts) |

> [!IMPORTANT]
> ”__C”-delen av API-namnet krävs för alla anpassade objekt.

![Namnet på data Factory - anslutning Salesforce - API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Frågan tips
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Hämtning av data med hjälp av where-sats för kolumnen datum/tid
När ange SOQL eller SQL-fråga, titta närmare på skillnaden för DateTime-format. Exempel:

* **SOQL exempel**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL-exempel**:
    * **Använd guiden Kopiera för att ange frågan:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Med JSON Redigera om du vill ange frågan (escape-tecken korrekt):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Hämtar data från Salesforce-rapport
Du kan hämta data från Salesforce-rapporter genom att ange frågan som `{call "<report name>"}`, till exempel. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Hämta borttagna poster från Salesforce-Papperskorgen
Om du vill fråga ej permanent borttagna poster från Salesforce-Papperskorgen, kan du ange **”IsDeleted = 1”** i frågan. Exempel:

* Om du vill fråga endast borttagna poster, ange ”Välj * från MyTable__c **där IsDeleted = 1**”
* Om du vill fråga efter alla poster som inklusive det befintliga och den borttagna, ange ”Välj * från MyTable__c **där IsDeleted = 0 eller IsDeleted = 1**”

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-exempel: kopiera data från Salesforce till Azure-Blob
I följande exempel innehåller exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från Salesforce till Azure Blob Storage. Dock datan kan kopieras till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.   

Här är Data Factory-artefakter som du behöver skapa för att implementera scenariot. Avsnitten som följer en lista innehåller information om de här stegen.

* En länkad tjänst av typen [Salesforce](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Indata [dataset](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties)
* Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Salesforce länkad tjänst**

Det här exemplet används den **Salesforce** länkade tjänsten. Finns det [Salesforce länkade tjänsten](#linked-service-properties) avsnittet för egenskaper som stöds av den här länkade tjänsten.  Se [hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställa/hämta säkerhetstoken.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Länkad Azure Storage-tjänst**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
    }
}
```
**Inkommande Salesforce-dataset**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"  
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Ange **externa** till **SANT** informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

> [!IMPORTANT]
> ”__C”-delen av API-namnet krävs för alla anpassade objekt.

![Namnet på data Factory - anslutning Salesforce - API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Utdatauppsättning för Azure-blobb**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Rörledningen med Kopieringsaktiviteten**

Pipelinen innehåller Kopieringsaktiviteten som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **RelationalSource**, och **sink** är inställd på **BlobSink**.

Se [RelationalSource Typegenskaper](#copy-activity-properties) lista över egenskaper som stöds av RelationalSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"                
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> ”__C”-delen av API-namnet krävs för alla anpassade objekt.

![Namnet på data Factory - anslutning Salesforce - API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mappning för Salesforce
| Salesforce-typ | . NET-baserade typ |
| --- | --- |
| Automatisk tal |Sträng |
| Kryssruta |Boolesk |
| Valuta |Dubbel |
| Date |DateTime |
| Datum/tid |DateTime |
| E-post |Sträng |
| Id |Sträng |
| Uppslagsrelation |Sträng |
| Flerval listruta |Sträng |
| Tal |Dubbel |
| Procent |Dubbel |
| Telefon |Sträng |
| Listruta |Sträng |
| Text |Sträng |
| Textområde |Sträng |
| Textområde (Long) |Sträng |
| Textområde (omfattande) |Sträng |
| Text (krypterade) |Sträng |
| URL |Sträng |

> [!NOTE]
> Om du vill mappa kolumner från källan dataset till kolumner från sink dataset finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Prestanda- och justering
Finns det [prestandajustering guide och Kopieringsaktivitet prestanda](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
