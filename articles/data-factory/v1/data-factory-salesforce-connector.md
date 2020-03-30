---
title: Flytta data från Salesforce med hjälp av Data Factory
description: Lär dig mer om hur du flyttar data från Salesforce med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b94f6388d77cca2ef74c802aec7648091172775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281137"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Flytta data från Salesforce med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-salesforce-connector.md)
> * [Version 2 (aktuell version)](../connector-salesforce.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Salesforce-anslutningsappen i V2](../connector-salesforce.md).

I den här artikeln beskrivs hur du kan använda Kopiera aktivitet i en Azure-datafabrik för att kopiera data från Salesforce till alla datalager som visas under kolumnen Sink i tabellen [Källor och sänkor som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Den här artikeln bygger på [dataförflyttningsaktiviteter,](data-factory-data-movement-activities.md) som ger en allmän översikt över dataförflyttning med Kopiera aktivitet och datalagerkombinationer som stöds.

Azure Data Factory stöder för närvarande endast flytta data från Salesforce till [sink-datalager som stöds,](data-factory-data-movement-activities.md#supported-data-stores-and-formats)men stöder inte att flytta data från andra datalager till Salesforce.

## <a name="supported-versions"></a>Versioner som stöds
Den här kopplingen stöder följande versioner av Salesforce: Developer Edition, Professional Edition, Enterprise Edition eller Unlimited Edition. Och det stöder kopiering från Salesforce produktion, sandlåda och anpassad domän.

## <a name="prerequisites"></a>Krav
* API-behörighet måste vara aktiverad. Se [Hur aktiverar jag API-åtkomst i Salesforce efter behörighetsgrupp?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Om du vill kopiera data från Salesforce till lokala datalager måste du ha minst Data Management Gateway 2.0 installerat i din lokala miljö.

## <a name="salesforce-request-limits"></a>Gränser för Salesforce-begäran
Salesforce har gränser för både totala API-begäranden och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begäranden överskrider gränsen sker begränsning och du kommer att se slumpmässiga fel.
- Om det totala antalet begäranden överskrider gränsen blockeras Salesforce-kontot i 24 timmar.

Du kan också få felet "REQUEST_LIMIT_EXCEEDED" i båda scenarierna. Mer information finns i avsnittet "Begränsningar för API-begäranden" i artikeln [Salesforce Developer Limits.](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från Salesforce med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. Ett exempel på ett exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data från Salesforce finns i [JSON-exempel: Kopiera data från Salesforce till Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för Salesforce:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell finns beskrivningar av JSON-element som är specifika för den Salesforce-länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **Salesforce**. |Ja |
| miljöUrl | Ange URL:en för Salesforce-instansen. <br><br> - Standard är\/"https: /login.salesforce.com". <br> - Om du vill kopierahttps://test.salesforce.comdata från sandlådan anger du " ". <br> - Om du vill kopiera data från en anpassad domän anger du till exempel "https://[domain].my.salesforce.com". |Inga |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| password |Ange ett lösenord för användarkontot. |Ja |
| säkerhetToken |Ange en säkerhetstoken för användarkontot. Se [Hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställer/hämtar en säkerhetstoken. Mer information om säkerhetstoken i allmänhet finns i [Säkerhet och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell och så vidare).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet typeProperties för en datauppsättning av typen **RelationalTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Salesforce. |Nej (om en **fråga om** **RelationalSource** har angetts) |

> [!IMPORTANT]
> Den "__c" delen av API-namnet behövs för alla anpassade objekt.

![Data Factory - Salesforce-anslutning - API-namn](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata och utdatatabeller och olika principer är tillgängliga för alla typer av aktiviteter.

Vilka egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar däremot med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När källan är av typen **RelationalSource** (som inkluderar Salesforce) i kopieringsaktiviteten är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |En SQL-92-fråga eller [SOQL-fråga (Salesforce Object Query Language).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Till exempel: `select * from MyTable__c`. |Nej (om **datauppsättningens** **tabellNamn** har angetts) |

> [!IMPORTANT]
> Den "__c" delen av API-namnet behövs för alla anpassade objekt.

![Data Factory - Salesforce-anslutning - API-namn](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Tips för frågor
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Hämtar data med hjälp av var sats i DateTime-kolumnen
När du anger SOQL- eller SQL-frågan bör du vara uppmärksam på formatskillnaden datetime. Ett exempel:

* **SOQL exempel:**`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL-exempel:**
    * **Använda kopieringsguiden för att ange frågan:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Använda JSON-redigering för att ange frågan (escape char properly):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Hämta data från Salesforce-rapport
Du kan hämta data från Salesforce-rapporter genom att ange fråga som `{call "<report name>"}`till exempel. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Hämta borttagna poster från Salesforce-papperskorgen
Om du vill fråga efter de mjuka borttagna posterna från Salesforce-papperskorgen kan du ange **"IsDeleted = 1"** i frågan. Exempel:

* Om du bara vill fråga de borttagna posterna anger du "välj * från MyTable__c **där IsDeleted= 1**"
* Om du vill fråga alla poster, inklusive befintliga och borttagna, anger du "välj * från MyTable__c **där IsDeleted = 0 eller IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-exempel: Kopiera data från Salesforce till Azure Blob
I följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från Salesforce till Azure Blob Storage. Data kan dock kopieras till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

Här är de datafabriksartefakter som du måste skapa för att implementera scenariot. De avsnitt som följer listan innehåller information om dessa steg.

* En länkad tjänst av typen [Salesforce](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* En [indatauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties)
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Salesforce länkad tjänst**

I det här exemplet används den **Salesforce-länkade** tjänsten. Se avsnittet [Salesforce-länkad tjänst](#linked-service-properties) för de egenskaper som stöds av den här länkade tjänsten. Se [Hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställer/hämtar säkerhetstoken.

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
**Azure Storage-länkad tjänst**

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
**Salesforce indatauppsättning**

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

Om du ställer **in externt** för **true** informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

> [!IMPORTANT]
> Den "__c" delen av API-namnet behövs för alla anpassade objekt.

![Data Factory - Salesforce-anslutning - API-namn](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure blob utdatauppsättning**

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

**Pipeline med kopieringsaktivitet**

Pipelinen innehåller kopieringsaktivitet, som är konfigurerad för att använda in- och utdatauppsättningar, och är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **RelationalSource**och **sink-typen** är inställd på **BlobSink**.

Se Egenskaper för [RelationsSource-typ](#copy-activity-properties) för listan över egenskaper som stöds av Relationskällan.

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
> Den "__c" delen av API-namnet behövs för alla anpassade objekt.

![Data Factory - Salesforce-anslutning - API-namn](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Typmappning för Salesforce

| Salesforce typ | . NET-baserad typ |
| --- | --- |
| Auto nummer |String |
| Checkbox |Boolean |
| Valuta |Decimal |
| Datum |DateTime |
| Date/Time |DateTime |
| E-post |String |
| Id |String |
| Uppslagsrelation |String |
| Flervalsplocklista |String |
| Tal |Decimal |
| Procent |Decimal |
| Telefon |String |
| Picklist (Plocklista) |String |
| Text |String |
| Textområde |String |
| Textområde (långt) |String |
| Textområde (rich) |String |
| Text (krypterad) |String |
| URL |String |

> [!NOTE]
> Information om hur du mappar kolumner från källdatauppsättning till kolumner från sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [prestanda- och justeringsguiden för kopieringsaktivitet](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
