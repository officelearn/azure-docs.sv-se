---
title: Skapa datauppsättningar i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att skapa datauppsättningar i Azure Data Factory med exempel på egenskaper, till exempel offset och anchorDateTime.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f33ff3f588dac49e295a5aa96d71557d32407e46
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046994"
---
# <a name="datasets-in-azure-data-factory"></a>Datauppsättningar i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-create-datasets.md)
> * [Version 2 (aktuell version)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [datauppsättningar i V2](../concepts-datasets-linked-services.md).

Den här artikeln beskriver vilka datauppsättningar är hur de har definierats i JSON-format och hur de används i Azure Data Factory rörledningar. Den innehåller information om varje avsnitt (till exempel struktur, tillgänglighet och princip) i JSON-definitionen för datauppsättning. Artikeln innehåller även exempel för att använda den **offset**, **anchorDateTime**, och **style** egenskaper i en dataset JSON-definition.

> [!NOTE]
> Om du är nybörjare på Data Factory finns [introduktion till Azure Data Factory](data-factory-introduction.md) en översikt. Om du inte har praktisk erfarenhet av att skapa datafabriker kan du få en bättre förståelse genom att läsa den [data transformation kursen](data-factory-build-your-first-pipeline.md) och [data movement kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utföra en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en kopieringsaktiviteten för att kopiera data från en lokal SQL Server till Azure Blob storage. Du kan sedan använda en Hive-aktivitet som kör en Hive-skript i ett Azure HDInsight-kluster för bearbetning av data från Blob storage gav inga utdata. Slutligen kan du använda en andra kopia aktiviteten kopiera utdata till Azure SQL Data Warehouse ovanpå vilka business intelligence (BI) reporting lösningar har skapats. Mer information om pipelines och aktiviteter finns [Pipelines och aktiviteter i Azure Data Factory](data-factory-create-pipelines.md).

En aktivitet kan ta noll eller fler indata **datauppsättningar**, och skapa en eller flera utdata-datauppsättningar. En inkommande datauppsättning representerar indata för en aktivitet i pipelinen, och en datamängd för utdata representerar utdata för aktiviteten. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. Azure-blobbdatauppsättning anger blobbehållaren och mappen i Blob storage från vilken pipelinen ska läsa data. 

Innan du skapar en datamängd, skapa en **länkade tjänsten** länka datalager till datafabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Datauppsättningar identifiera data i länkade datalager, till exempel SQL-tabeller, filer, mappar och dokument. Till exempel kopplad ett Azure Storage tjänst länkar ett lagringskonto till datafabriken. Azure-blobbdatauppsättning som representerar blob-behållaren och den mapp som innehåller den inkommande BLOB-objekt som ska bearbetas. 

Här är ett exempelscenario. Om du vill kopiera data från Blob storage till en SQL-databas, skapar du två länkade tjänster: Azure Storage- och Azure SQL Database. Skapa sedan två datamängder: Azure-blobbdatauppsättning (som refererar till länkad Azure Storage service) och Azure SQL-tabell datauppsättningen (varvid refererar till Azure SQL Database länkade tjänsten). Azure Storage- och Azure SQL Database länkade tjänster innehålla anslutningssträngar som Data Factory använder vid körning för att ansluta till ditt Azure Storage och Azure SQL Database respektive. Azure-blobbdatauppsättning anger blob-behållaren och blob-mapp som innehåller de inkommande blobbarna i Blob storage. Azure SQL-tabell datauppsättningen anger SQL-tabellen i SQL-databasen som är data som ska kopieras.

Följande diagram visar relationerna mellan pipeline, aktivitet, datamängd och länkade tjänsten i Data Factory: 

![Förhållandet mellan pipeline, aktivitet, dataset, länkade tjänster](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Datauppsättnings-JSON
En datamängd i Data Factory har definierats i JSON-format på följande sätt:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

I följande tabell beskrivs egenskaperna i ovanstående JSON:   

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| namn |Namnet på datamängden. Se [Azure Data Factory - namngivningsregler](data-factory-naming-rules.md) för namngivningsregler. |Ja |Ej tillämpligt |
| typ |Typ av datauppsättningen. Ange en av de typer som stöds av Data Factory (till exempel: AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [datauppsättningstypen](#Type). |Ja |Ej tillämpligt |
| struktur |Schemat för datauppsättningen.<br/><br/>Mer information finns i [datauppsättningsstrukturen](#Structure). |Nej |Ej tillämpligt |
| typeProperties | Typegenskaper är olika för varje typ (till exempel: Azure Blob, Azure SQL-tabell). Mer information om typerna som stöds och deras egenskaper finns [datauppsättningstypen](#Type). |Ja |Ej tillämpligt |
| extern | Boolesk flagga som anger om en datamängd uttryckligen produceras av en data factory-pipelinen eller inte. Om den inkommande datamängden för en aktivitet inte produceras av den aktuella pipelinen, kan du ange den här flaggan till true. Ange den här flaggan till true för den första aktiviteten i pipelinen inkommande datauppsättningen.  |Nej |false |
| availability | Definierar fönstret bearbetning (till exempel varje timme eller varje dag) eller slicing modellen för produktion dataset. Varje dataenhet förbrukats och produceras av en aktivitet körs kallas en datasektorn. Om tillgängligheten för en datamängd för utdata är varje dag (frekvens - dag, intervallet - 1) skapas ett segment varje dag. <br/><br/>Mer information finns i [Dataset tillgänglighet](#Availability). <br/><br/>Mer information om datauppsättningen segmentering modellen finns i [schemaläggning och körning](data-factory-scheduling-and-execution.md) artikel. |Ja |Ej tillämpligt |
| policy |Definierar villkoren eller det villkor som dataset-segment måste vara uppfyllda. <br/><br/>Mer information finns i [Dataset princip](#Policy) avsnitt. |Nej |Ej tillämpligt |

## <a name="dataset-example"></a>DataSet-exempel
I följande exempel datauppsättningen representerar en tabell med namnet **mytable prefix** i en SQL-databas.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Observera följande punkter:

* **typen** är inställd på AzureSqlTable.
* **tableName** typ (specifikt för AzureSqlTable typ) egenskapen mytable prefix.
* **linkedServiceName** refererar till en länkad tjänst av typen AzureSqlDatabase som definieras i nästa JSON-fragment. 
* **tillgänglighet frekvens** till dagen, och **intervall** har angetts till 1. Det innebär att dataset sektorn skapas varje dag.  

**AzureSqlLinkedService** definieras enligt följande:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

I den föregående JSON-utdrag:

* **typen** är inställd på AzureSqlDatabase.
* **connectionString** typegenskapen anger information för att ansluta till en SQL-databas.  

Som du ser definierar den länkade tjänsten hur du ansluter till en SQL-databas. Dataset definierar vilka tabellen används som indata och utdata för aktiviteten i en pipeline.   

> [!IMPORTANT]
> Om ett DataSet-objekt produceras av pipelinen, bör det markeras som **externa**. Den här inställningen gäller vanligtvis indata för den första aktiviteten i en pipeline.   


## <a name="Type"></a> Typen av datauppsättning
Typ av datauppsättningen beror på dataarkivet du använder. Se följande tabell för en lista över datakällor som stöds av Data Factory. Klicka på ett datalager för att lära dig hur du skapar en länkad tjänst och en datauppsättning för att lagra data.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Data som lagras med * kan vara lokalt eller på Azure-infrastrukturen som en tjänst (IaaS). Dessa datalager måste du installera [Data Management Gateway](data-factory-data-management-gateway.md).

I exemplet ovan är typ av datamängden har angetts till **AzureSqlTable**. För en Azure-blobbdatauppsättning anges på samma sätt typ av datamängden som **AzureBlob**, enligt följande JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="Structure"></a>DataSet-struktur
Den **struktur** avsnittet är valfritt. Den definierar schemat för datauppsättningen genom som innehåller en samling med namn och datatyperna för kolumnerna. Du kan använda avsnittet strukturen för att ange av typinformation som används för att konvertera typer och mappa kolumner från källan till målet. I följande exempel datamängden har tre kolumner: `slicetimestamp`, `projectname`, och `pageviews`. De är av typen String, String och Decimal, respektive.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Varje kolumn i strukturen innehåller följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på kolumnen. |Ja |
| typ |Datatypen för kolumnen.  |Nej |
| kultur |. NET-baserade kulturen som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset`. Standardvärdet är `en-us`. |Nej |
| Format |Formatsträng som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset`. |Nej |

Följande riktlinjer hjälper dig att avgöra när du ska inkludera strukturinformation och vad som ska ingå i den **struktur** avsnitt.

* **För strukturerade datakällor**, ange struktur avsnittet om du vill mappa källkolumner för sink kolumner och deras namn är inte samma. Den här typen av datakälla för strukturerade lagrar data schema och ange information tillsammans med själva informationen. Exempel på strukturerade datakällor är SQL Server, Oracle och Azure-tabellen. 
  
    Som typen finns redan för strukturerade datakällor, kan inkludera du inte typinformation när du inkluderar avsnittet struktur.
* **För schemat för skrivskyddade datakällor (särskilt Blob storage)**, kan du lagra data utan att spara schemat eller typ information med data. Inkludera struktur för dessa typer av datakällor om du vill mappa källkolumner för sink kolumner. Inkludera även struktur när datauppsättningen utgör indata för en kopieringsaktiviteten och datatyper i källan dataset ska konverteras till inbyggda typer för sink. 
    
    Data Factory stöder följande värden för att ange information i strukturen: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], Boolean, String, Guid, Datetime, Datetimeoffset och Timespan**. Dessa värden är Common Language Specification (CLS)-kompatibel,. NET-baserade typen värden.

Data Factory utför konverteringar automatiskt när data flyttas från ett dataarkiv som källa till datakällan sink. 
  

## <a name="dataset-availability"></a>DataSet-tillgänglighet
Den **tillgänglighet** avsnitt i en dataset definierar fönstret bearbetning (till exempel varje timme, varje dag eller varje vecka) för datauppsättningen. Läs mer om aktiviteten windows [schemaläggning och körning](data-factory-scheduling-and-execution.md).

I följande avsnitt för tillgänglighet anger att datamängd för utdata är antingen produceras per timma eller inkommande dataset finns varje timme:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

Om pipelinen har följande start- och sluttider:  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Datamängd för utdata skapas varje timme i pipeline start- och sluttider. Det finns därför fem dataset-segment som producerats av denna pipeline, en för varje aktivitetsfönstret (12: 00 - 1 AM, 1 AM - 2 AM, 02: 00 - 3 AM, 3 AM - 4 AM, 4 AM - 5 AM). 

Följande tabell beskriver egenskaper som kan användas i avsnittet tillgänglighet:

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för dataset sektorn produktion.<br/><br/><b>Stöd för frekvens</b>: minut, timma, dag, vecka, månad |Ja |Ej tillämpligt |
| interval |Anger en multiplikator för frekvens.<br/><br/>”X frekvensintervall” avgör hur ofta sektorn skapas. Till exempel om du behöver datamängden som segmenterat timme kan du ange <b>frekvens</b> till <b>timme</b>, och <b>intervall</b> till <b>1</b>.<br/><br/>Observera att om du anger **frekvens** som **minut**, bör du ange intervallet till mindre än 15. |Ja |Ej tillämpligt |
| format |Anger om sektorn ska produceras i början eller slutet av intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Om **frekvens** är inställd på **månad**, och **style** är inställd på **EndOfInterval**, sektorn skapas på den sista dagen i månaden. Om **style** är inställd på **StartOfInterval**, sektorn skapas på den första dagen i månaden.<br/><br/>Om **frekvens** är inställd på **dag**, och **style** är inställd på **EndOfInterval**, sektorn skapas under den senaste timmen på dagen.<br/><br/>Om **frekvens** är inställd på **timme**, och **style** är inställd på **EndOfInterval**, sektorn skapas i slutet av en timme. För ett segment för perioden 1 PM - 14: 00, till exempel produceras sektorn klockan 2. |Nej |EndOfInterval |
| anchorDateTime |Definierar absolut placering i tid som används av Schemaläggaren för att beräkna dataset sektorn gränser. <br/><br/>Observera att om den här propoerty har datumdelar som är mer detaljerad än den angivna frekvensen, ignoreras de mer detaljerade delarna. Till exempel om den **intervall** är **varje timme** (frekvens: timme och intervall: 1), och **anchorDateTime** innehåller **minuter och sekunder**, och sedan minuter och sekunder delar av **anchorDateTime** ignoreras. |Nej |01/01/0001 |
| förskjutning |TimeSpan som start- och slutdatum för alla dataset segment flyttat. <br/><br/>Observera att om båda **anchorDateTime** och **offset** anges, resultatet är kombinerade SKIFT. |Nej |Ej tillämpligt |

### <a name="offset-example"></a>Offset exempel
Som standard varje dag (`"frequency": "Day", "interval": 1`) segment som börjar vid 12: 00 (midnatt) Coordinated Universal Time (UTC). Om du vill att starttiden ska 06: 00 UTC-tid i stället ange förskjutningen som visas i följande fragment: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime-exempel
I följande exempel skapas datauppsättningen var 23: e timme. Det första segmentet börjar vid den tid som anges av **anchorDateTime**, som har angetts till `2017-04-19T08:00:00` (UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>förskjutning/style-exempel
Följande DataSet månadsvis, och skapas på 3: e varje månad kl 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>DataSet-princip
Den **princip** avsnitt i datauppsättningsdefinitionen definierar villkoren eller det villkor som dataset-segment måste vara uppfyllda.

### <a name="validation-policies"></a>Verifieringsprinciper
| Principnamn | Beskrivning | Tillämpas på | Krävs | Standard |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Validerar att informationen i **Azure Blob storage** uppfyller minsta storlek (i megabyte). |Azure Blob Storage |Nej |Ej tillämpligt |
| minimumRows |Validerar att data i en **Azure SQL database** eller en **Azure-tabellen** innehåller det minsta antalet rader. |<ul><li>Azure SQL-databas</li><li>Azure-tabell</li></ul> |Nej |Ej tillämpligt |

#### <a name="examples"></a>Exempel
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Externa datauppsättningar
Externa datauppsättningar är de som inte tillverkats av en pipeline som körs i datafabriken. Om datamängden har markerats som **externa**, **ExternalData** princip kan definieras som påverkar beteendet för dataset sektorn tillgänglighet.

Om ett DataSet-objekt produceras av Data Factory, bör det markeras som **externa**. Den här inställningen gäller vanligtvis indata som första aktivitet i pipelinen, om inte aktiviteten eller pipeline länkning används.

| Namn | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| dataDelay |Tid att fördröja kontrollera tillgängligheten för externa data för den angivna sektorn. Du kan till exempel fördröja en kontroll av varje timme med den här inställningen.<br/><br/>Inställningen gäller bara för den aktuella tiden.  Om det är 1:00 PM just nu och det här värdet är 10 minuter, till exempel startas valideringen klockan 13:10.<br/><br/>Observera att den här inställningen inte påverkar segment i förflutna. Sektorer med **sektorn sluttid** + **dataDelay** < **nu** bearbetas utan fördröjning.<br/><br/>Gånger större än 23:59 timmar måste anges med hjälp av den `day.hours:minutes:seconds` format. Till exempel vill ange 24 timmar, Använd inte 24:00:00. Använd i stället 1.00:00:00. Om du använder 24:00:00, behandlas den som 24 dagar (24.00:00:00). Ange 1:04:00:00 för 1 dag och 4 timmar. |Nej |0 |
| retryInterval |Väntetiden mellan ett fel och nästa försök. Den här inställningen gäller för närvarande. Om den tidigare misslyckade, nästa försök är efter den **retryInterval** period. <br/><br/>Om den är 1:00 PM just nu kan börja vi första försöket. Om tid att slutföra den första verifieringen är 1 minut och åtgärden misslyckades, nästa försök görs 1:00 + 1 min. (varaktighet) + 1 min. (Återförsöksintervall) = 1:02 PM. <br/><br/>Segment tidigare finns det ingen fördröjning. Den här gången sker omedelbart. |Nej |00:01:00 (1 minut) |
| retryTimeout |Tidsgräns för varje nytt försök.<br/><br/>Om den här egenskapen har angetts till 10 minuter ska verifieringen ha slutförts inom 10 minuter. Om det tar längre tid än 10 minuter att utföra valideringen timeout för och försök igen.<br/><br/>Om alla försök för timeout validering sektorn är markerat som **orsakade**. |Nej |00:10:00 (10 minuter) |
| maximumRetry |Antal gånger för att kontrollera tillgänglighet för externa data. Det högsta tillåtna värdet är 10. |Nej |3 |


## <a name="create-datasets"></a>Skapa datauppsättningar
Du kan skapa datauppsättningar på något av dessa verktyg och SDK: 

- Guiden Kopiera 
- Azure Portal
- Visual Studio
- PowerShell
- Azure Resource Manager-mall
- REST-API
- .NET-API

Se följande kurser stegvisa instruktioner för att skapa pipelines och datauppsättningar på något av dessa verktyg och SDK:
 
- [Skapa en pipeline med en datatransformeringsaktivitet](data-factory-build-your-first-pipeline.md)
- [Skapa en pipeline med en aktivitet för flytt av data](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

När en pipeline skapas och distribueras, kan du hantera och övervaka din pipelines med hjälp av Azure portal bladen eller appen övervakning och hantering. Finns i följande avsnitt instruktioner steg för steg: 

- [Övervaka och hantera pipelines med hjälp av Azure portal blad](data-factory-monitor-manage-pipelines.md)
- [Övervaka och hantera pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>Begränsade datauppsättningar
Du kan skapa datauppsättningar som är begränsade till en pipeline med hjälp av den **datauppsättningar** egenskapen. Dessa data kan endast användas av aktiviteter i denna pipeline, inte av aktiviteter i andra pipelines. I följande exempel definieras en pipeline med två datamängder (InputDataset rdc och OutputDataset rdc) som ska användas i pipelinen.  

> [!IMPORTANT]
> Begränsade datauppsättningar kan bara användas med enstaka pipelines (där **pipelineMode** är inställd på **OneTime**). Se [Onetime pipeline](data-factory-create-pipelines.md#onetime-pipeline) mer information.
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om pipelines [skapa pipelines](data-factory-create-pipelines.md). 
- Mer information om hur pipelines schemaläggs och körs finns [schemaläggning och körning i Azure Data Factory](data-factory-scheduling-and-execution.md). 
