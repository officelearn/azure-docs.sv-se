---
title: Flytta data från Salesforce med Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från Salesforce med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 63fd8f75cb22a4dec888b7765c7515bfb68a4a6a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999020"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Flytta data från Salesforce med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-salesforce-connector.md)
> * [Version 2 (aktuell version)](../connector-salesforce.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Salesforce-anslutning i V2](../connector-salesforce.md).

Den här artikeln beskriver hur du kan använda Kopieringsaktivitet i en Azure-datafabrik för att kopiera data från Salesforce till alla data som anges under kolumnen mottagare i den [stöds källor och mottagare](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Den här artikeln bygger vidare på den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som visar en allmän översikt över dataförflyttning med Kopieringsaktivitet och data som stöds store kombinationer.

Azure Data Factory stöder för närvarande endast flyttar data från Salesforce till [datalager för mottagare som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats), men stöds inte flyttar data från andra data som lagras i Salesforce.

## <a name="supported-versions"></a>Versioner som stöds
Den här anslutningsappen stöder följande versioner av Salesforce: Developer Edition, Professional Edition, Enterprise Edition eller obegränsad Edition. . Den stöder också kopiera från Salesforce produktion, sandbox-miljön och anpassad domän.

## <a name="prerequisites"></a>Förutsättningar
* API-behörighet måste aktiveras. Se [hur gör jag för att aktivera API-åtkomst i Salesforce av behörighetsuppsättning?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* För att kopiera data från Salesforce till lokala datalager, du har Data Management Gateway 2.0 är installerat i din lokala miljö.

## <a name="salesforce-request-limits"></a>Salesforce-begärandebegränsningar
Salesforce har gränser för både Totalt antal API-begäranden och samtidiga API-begäranden. Observera följande punkter:

- Om antalet samtidiga begäranden överskrider gränsen, begränsning inträffar och slumpmässiga fel visas.
- Om det totala antalet begäranden överskrider gränsen, kommer Salesforce-konto att blockeras i 24 timmar.

Du kan också få ”REQUEST_LIMIT_EXCEEDED”-fel i båda scenarierna. Se avsnittet ”API Begärandebegränsningar” i den [Salesforce Developer gränser](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) nedan för information.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från Salesforce med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från Salesforce finns [JSON-exempel: Kopiera data från Salesforce till Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Salesforce:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell innehåller beskrivningar av JSON-element som är specifika för Salesforce-länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **Salesforce**. |Ja |
| environmentUrl | Ange URL: en för Salesforce-instans. <br><br> – Standardvärdet är ”https://login.salesforce.com”. <br> – Om du vill kopiera data från sandbox, ange ”https://test.salesforce.com”. <br> – Om du vill kopiera data från anpassad domän, ange, till exempel ”https://[domain].my.salesforce.com”. |Nej |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| lösenord |Ange ett lösenord för användarkontot. |Ja |
| securityToken |Ange en säkerhetstoken för användarkontot. Se [hämta säkerhetstoken för](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställning/hämta en säkerhetstoken. Läs mer om säkerhetstoken i allmänhet i [säkerhets- och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell och så vidare).

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. TypeProperties avsnittet för en datauppsättning av typen **RelationalTable** har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Salesforce. |Nej (om en **fråga** av **RelationalSource** har angetts) |

> [!IMPORTANT]
> Den ”__c”-delen av API-namn krävs för alla anpassade objekt.

![Factory - Salesforce-connection - API-Datanamn](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper som namn, beskrivning, indata och utdata tabeller och olika principer är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten, å andra sidan varierar med varje aktivitetstyp av. För Kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

I kopieringsaktiviteten när källan är av typen **RelationalSource** (som omfattar Salesforce), följande egenskaper är tillgängliga i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |En SQL-92-fråga eller [Salesforce objektet Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) fråga. Till exempel: `select * from MyTable__c`. |Nej (om den **tableName** av den **datauppsättning** har angetts) |

> [!IMPORTANT]
> Den ”__c”-delen av API-namn krävs för alla anpassade objekt.

![Factory - Salesforce-connection - API-Datanamn](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Frågetips
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Hämta data med where-sats i DateTime-kolumn
Ange när den SOQL eller SQL-frågan, närmare på skillnaden för DateTime-format. Exempel:

* **SOQL exempel**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL-exemplet**:
    * **Använd Kopieringsguiden för att ange frågan:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Med hjälp av JSON-redigering för att ange frågan (escape char korrekt):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Hämtning av data från Salesforce-rapport
Du kan hämta data från Salesforce-rapporter genom att ange frågan som `{call "<report name>"}`, till exempel. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Hämta borttagna poster från Salesforce-Papperskorgen
Om du vill fråga ej permanent borttagna poster från Salesforce-Papperskorgen, kan du ange **”IsDeleted = 1”** i frågan. Exempel:

* Om du vill fråga bara de borttagna posterna, ange ”Välj * från MyTable__c **där IsDeleted = 1**”
* Om du vill fråga efter alla poster, inklusive den befintliga och den har tagits bort, ange ”Välj * från MyTable__c **där IsDeleted = 0 eller IsDeleted = 1**”

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-exempel: Kopiera data från Salesforce till Azure Blob
I följande exempel innehåller exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av den [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från Salesforce till Azure Blob Storage. Dock datan kan kopieras till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

Här följer Data Factory-artefakter som du måste skapa för att implementera scenariot. Avsnitten som följer listan innehåller information om de här stegen.

* En länkad tjänst av typen [Salesforce](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties)
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Salesforce-länkad tjänst**

Det här exemplet används den **Salesforce** länkad tjänst. Se den [Salesforce-länkade tjänst](#linked-service-properties) avsnittet för egenskaper som stöds av den här länkade tjänsten. Se [hämta säkerhetstoken för](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställning/hämta säkerhetstoken.

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
**Indatauppsättning för Salesforce**

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

Ange **externa** till **SANT** informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

> [!IMPORTANT]
> Den ”__c”-delen av API-namn krävs för alla anpassade objekt.

![Factory - Salesforce-connection - API-Datanamn](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Utdatauppsättning för Azure-blobb**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1).

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

**Pipeline med Kopieringsaktivitet**

Pipelinen innehåller Kopieringsaktiviteten som är konfigurerad för att använda uppsättningar för indata och utdata och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **RelationalSource**, och **mottagare** är **BlobSink**.

Se [RelationalSource typegenskaperna](#copy-activity-properties) lista över egenskaper som stöds av RelationalSource.

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
> Den ”__c”-delen av API-namn krävs för alla anpassade objekt.

![Factory - Salesforce-connection - API-Datanamn](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mappning för Salesforce

| Salesforce-typ | . NET-baserade typ |
| --- | --- |
| Automatisk numrering |String |
| Kryssrutan |Boolesk |
| Valuta |Decimal |
| Date |DateTime |
| Datum/tid |DateTime |
| E-post |String |
| Id |String |
| Uppslagsrelation |String |
| Flervalsplocklista |String |
| Tal |Decimal |
| Procent |Decimal |
| Telefon |String |
| Listruta |String |
| Text |String |
| Textområde |String |
| Textområde (Long) |String |
| Textområde (Rich) |String |
| Text (krypterad) |String |
| URL |String |

> [!NOTE]
> Om du vill mappa kolumner från datauppsättningen för källan till kolumner från en datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se den [Kopieringsaktiviteten prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
