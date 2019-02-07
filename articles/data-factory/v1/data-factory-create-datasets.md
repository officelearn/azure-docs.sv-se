---
title: Skapa datauppsättningar i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar datauppsättningar i Azure Data Factory med exempel som använder egenskaper, till exempel offset och anchorDateTime.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: af90a946f12e11602d45300a2796787f839dcf02
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811094"
---
# <a name="datasets-in-azure-data-factory"></a>Datauppsättningar i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-create-datasets.md)
> * [Version 2 (aktuell version)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [datauppsättningar i V2](../concepts-datasets-linked-services.md).

Den här artikeln beskriver vilka datauppsättningar är, hur de definieras i JSON-format och hur de används i Azure Data Factory-pipelines. Den innehåller information om varje avsnitt (till exempel struktur, tillgänglighet och princip) i JSON-definitionen för datauppsättningen. Artikeln innehåller även exempel för att använda den **offset**, **anchorDateTime**, och **style** egenskaper i en datauppsättning JSON-definition.

> [!NOTE]
> Om du är nybörjare till Data Factory finns i [introduktion till Azure Data Factory](data-factory-introduction.md) en översikt. Om du inte har praktisk erfarenhet med att skapa datafabriker kan du få en bättre förståelse genom att läsa den [självstudien för omvandling av data](data-factory-build-your-first-pipeline.md) och [data movement självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en Kopieringsaktivitet som kopierar data från en lokal SQL Server till Azure Blob storage. Du kan sedan använda en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster att bearbeta data från Blob storage för att producera utdata. Slutligen kan du använda en andra Kopieringsaktivitet för att kopiera utdata till Azure SQL Data Warehouse ovanpå som business intelligence (BI) reporting-lösningarna. Läs mer om pipelines och aktiviteter, [Pipelines och aktiviteter i Azure Data Factory](data-factory-create-pipelines.md).

En aktivitet kan ha noll eller flera **datauppsättningar**, och framställer en eller flera datauppsättningar som utdata. En indatauppsättning representerar indata för en aktivitet i pipelinen och en utdatauppsättning representerar utdata för aktiviteten. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. En Azure Blob-datauppsättning anger till exempel blobbehållaren och mappen i Blob storage som pipelinen ska läsa data.

Innan du skapar en datauppsättning, skapar du en **länkad tjänst** att länka ditt datalager till datafabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Datauppsättningar identifierar data i länkade datalager, till exempel SQL-tabeller, filer, mappar och dokument. Till exempel länkad en Azure Storage-tjänsten länkar ett storage-konto till datafabriken. En Azure Blob-datauppsättning representerar blobbehållaren och mappen som innehåller indatablobbar som ska bearbetas.

Här är ett exempelscenario. Om du vill kopiera data från Blob storage till en SQL-databas, skapar du två länkade tjänster: Azure Storage och Azure SQL Database. Skapa sedan två datauppsättningar: Azure Blob-datauppsättning (som refererar till den länkade Azure Storage-tjänsten) och Azure SQL-tabelldatauppsättning (som refererar till länkad Azure SQL Database-tjänsten). Innehåller anslutningssträngar som Datafabriken använder vid körning för att ansluta till ditt Azure Storage och Azure SQL Database, respektive Azure Storage och länkad Azure SQL Database-tjänster. Azure Blob-datauppsättning anger blobbehållaren och blobbmapp som innehåller indatablobbar i Blob storage. Azure SQL-tabelldatauppsättning ange den SQL-tabellen i SQL-databasen som data ska kopieras.

Följande diagram visar relationerna mellan pipeline, aktivitet, datauppsättning och den länkade tjänsten i Data Factory:

![Förhållandet mellan pipeline, aktivitet, datauppsättning, länkade tjänster](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Datauppsättnings-JSON
En datauppsättning i Data Factory har definierats i JSON-format på följande sätt:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
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
| namn |Namnet på datauppsättningen. Se [Azure Data Factory – namnregler](data-factory-naming-rules.md) för regler för namngivning. |Ja |Ej tillämpligt |
| typ |Typ av datauppsättningen. Ange en av de typer som stöds av Data Factory (till exempel: AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [datauppsättningstypen](#Type). |Ja |Ej tillämpligt |
| struktur |Schemat för datauppsättningen.<br/><br/>Mer information finns i [datauppsättningsstrukturen](#Structure). |Nej |Ej tillämpligt |
| typeProperties | Typegenskaperna är olika för varje typ (till exempel: Azure Blob, Azure SQL-tabell). Mer information om typerna som stöds och deras egenskaper finns [datauppsättningstypen](#Type). |Ja |Ej tillämpligt |
| extern | Boolesk flagga för att ange om en datauppsättning uttryckligen produceras av data factory-pipeline eller inte. Om datauppsättningen för indata för en aktivitet inte skapas av den nuvarande pipelinen, ange den här flaggan till true. Ange den här flaggan till true för indatauppsättningen för den första aktiviteten i pipelinen.  |Nej |false |
| availability | Definierar fönstret bearbetning (till exempel varje timme eller varje dag) eller slicing modellen för produktion för datauppsättningen. Varje enhet med data används och produceras av en aktivitet körs kallas för en datasektor. Om tillgängligheten för en utdatauppsättning anges för varje dag (frekvens - dag, interval - 1), produceras en sektor varje dag. <br/><br/>Mer information finns i tillgänglighet för datauppsättningar. <br/><br/>Mer information om datauppsättningen uppdelning modellen finns det [schemaläggning och körning](data-factory-scheduling-and-execution.md) artikeln. |Ja |Ej tillämpligt |
| policy |Definierar kriterierna eller villkor som datauppsättning segment måste vara uppfyllda. <br/><br/>Mer information finns i [datauppsättning princip](#Policy) avsnittet. |Nej |Ej tillämpligt |

## <a name="dataset-example"></a>Exempel med datauppsättningen
I följande exempel datauppsättningen representerar en tabell med namnet **MyTable** i en SQL-databas.

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

* **typ** är inställd på AzureSqlTable.
* **tableName** typegenskapen (specifikt för AzureSqlTable typ) har angetts till MyTable.
* **linkedServiceName** refererar till en länkad tjänst av typen AzureSqlDatabase som definieras i nästa JSON-kodfragmentet.
* **tillgänglighet frekvens** är inställd på dagen, och **intervall** har angetts till 1. Det innebär att datamängdssektor skapas varje dag.

**AzureSqlLinkedService** definieras så här:

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

I föregående JSON-kodfragmentet:

* **typ** är inställd på AzureSqlDatabase.
* **connectionString** typegenskapen anger information för att ansluta till en SQL-databas.

Som du ser definierar den länkade tjänsten hur du ansluter till en SQL-databas. Datauppsättningen definierar vilken tabell som används som indata och utdata för aktiviteten i en pipeline.

> [!IMPORTANT]
> Om inte en datauppsättningen produceras av pipelinen, bör det markeras som **externa**. Den här inställningen gäller vanligtvis indata för den första aktiviteten i en pipeline.

## <a name="Type"></a> Typ av datauppsättning
Vilken typ av datauppsättningen beror på det datalager som du använder. Se tabellen nedan för en lista över datalager som stöds av Data Factory. Klicka på ett datalager om du vill veta hur du skapar en länkad tjänst och en datauppsättning för det datalagringen.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure-infrastruktur som en tjänst (IaaS). Dessa datalager måste du installera [Data Management Gateway](data-factory-data-management-gateway.md).

I exemplet i föregående avsnitt, vilken typ av datauppsättningen är inställd **AzureSqlTable**. På samma sätt för en Azure Blob-datauppsättning, även typ av datauppsättningen är inställt på **AzureBlob**, enligt följande JSON:

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
Den **struktur** avsnittet är valfritt. Den definierar schemat för datauppsättningen genom som innehåller en uppsättning namn och datatyperna för kolumnerna. Du kan använda avsnittet struktur för att ange av typinformation som används för att konvertera typer och mappa kolumner från källan till målet. I följande exempel har tre kolumner i datauppsättningen: `slicetimestamp`, `projectname`, och `pageviews`. De är av typen String, String och Decimal, respektive.

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
| kultur |. NET-baserade språkmiljö som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset`. Standardvärdet är `en-us`. |Nej |
| Format |Formatera strängen som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset`. |Nej |

Följande riktlinjer hjälper dig att avgöra när du ska inkludera strukturinformation och vad som ska ingå i den **struktur** avsnittet.

* **För strukturerade datakällor**, ange avsnittet struktur endast om du vill mappa källkolumner för att mottagare kolumner och deras namn inte är desamma. Den här typen av datakälla för strukturerade lagrar data schema och ange information tillsammans med själva informationen. Exempel på strukturerade datakällor är SQL Server, Oracle och Azure-tabell.
  
    Så anger du följande information är redan tillgänglig för strukturerade datakällor måste ta du inte anger du följande information när du inkluderar avsnittet struktur.
* **För schema vid läsning datakällor (särskilt Blob storage)**, kan du lagra data utan att behöva lagra någon schema eller typ information med data. Inkludera struktur för dessa typer av datakällor när du vill mappa källkolumner för att kolumner för mottagare. Inkludera även struktur när datauppsättningen utgör indata för en Kopieringsaktivitet och datatyperna för datauppsättningen för källan ska konverteras till inbyggda typer för mottagaren.
    
    Data Factory stöder följande värden för att tillhandahålla anger du följande information i struktur: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], booleskt, sträng, Guid, Datetime, Datetimeoffset och Timespan**. Dessa värden är Common Language Specification (CLS)-kompatibla. Av NET-baserade typvärden.

Data Factory utför typkonverteringar automatiskt när du flyttar data från källans datalager till mottagarens datalager.

## <a name="dataset-availability"></a>Tillgänglighet för datauppsättningar
Den **tillgänglighet** avsnitt i en datauppsättning definierar fönstret bearbetning (till exempel varje timme, varje dag eller varje vecka) för datauppsättningen. Läs mer om aktivitetsfönster [schemaläggning och körning](data-factory-scheduling-and-execution.md).

Tillgänglighet nedan anger att utdatauppsättningen skapas antingen per timme eller indatauppsättningen är tillgänglig per timme:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Om pipelinen har följande starttid och Sluttid:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Datauppsättningen för utdata skapas varje timme i pipeline start- och sluttider. Det finns därför fem datauppsättning sektorer som genereras av denna pipeline, en för varje aktivitetsfönstret (12: 00 - 1 AM, 1 AM - 2 AM, 02: 00 - 3 AM, 3 AM - 4 AM, 4 AM - 05: 00).

I följande tabell beskrivs egenskaperna som du kan använda i avsnittet tillgänglighet:

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för datauppsättningen sektorn produktion.<br/><br/><b>Stöds frekvens</b>: Minut, timme, dag, vecka, månad |Ja |Ej tillämpligt |
| interval |Anger en multiplikator för frekvensen.<br/><br/>”X frekvensintervall” avgör hur ofta sektorn skapas. Till exempel om du behöver datauppsättningen att delas timme kan du ange <b>frekvens</b> till <b>timme</b>, och <b>intervall</b> till <b>1</b>.<br/><br/>Observera att om du anger **frekvens** som **minut**, bör du ange intervallet till mindre än 15. |Ja |Ej tillämpligt |
| stil |Anger om sektorn ska produceras i början eller slutet av intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Om **frekvens** är inställd på **månad**, och **style** är inställd på **EndOfInterval**, sektorn skapas på den sista dagen i månaden. Om **style** är inställd på **StartOfInterval**, sektorn skapas på den första dagen i månaden.<br/><br/>Om **frekvens** är inställd på **dag**, och **style** är inställd på **EndOfInterval**, sektorn skapas under den senaste timmen på dagen.<br/><br/>Om **frekvens** är inställd på **timme**, och **style** är inställd på **EndOfInterval**, sektorn skapas i slutet av timmen. För en sektor under 1 PM - 14: 00, till exempel produceras sektorn klockan 2. |Nej |EndOfInterval |
| anchorDateTime |Definierar absolut position i tid som används av scheduler för att beräkna datauppsättning sektorn gränser. <br/><br/>Observera att de mer detaljerade delarna ignoreras om den här propoerty har datumdelar som är större än den angivna frekvensen. Till exempel om den **intervall** är **per timme** (frequency: hour och interval: 1), och **anchorDateTime** innehåller **minuter och sekunder**, och sedan minuter och sekunder delar av **anchorDateTime** ignoreras. |Nej |01/01/0001 |
| offset |TimeSpan som början och slutet av alla datauppsättningen sektorer beräkningsarbete. <br/><br/>Observera att om båda **anchorDateTime** och **offset** anges, resultatet är kombinerade SKIFT. |Nej |Ej tillämpligt |

### <a name="offset-example"></a>förskjutningen exempel
Som standard varje dag (`"frequency": "Day", "interval": 1`) sektorer som börjar vid 12: 00 (midnatt) Coordinated Universal Time (UTC). Om du vill att starttiden vara 06: 00 UTC-tid i stället ange förskjutningen som visas i följande kodavsnitt:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime exempel
I följande exempel produceras datauppsättningen var 23: e timme. Första sektorn som börjar vid den tid som anges av **anchorDateTime**, som har angetts till `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>förskjutning och formatet exempel
Följande datauppsättningen är varje månad, och skapas på 3: e varje månad kl 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Princip för datauppsättning
Den **princip** avsnittet i definitionen av datauppsättningen definierar kriterierna eller villkor som datauppsättning segment måste vara uppfyllda.

### <a name="validation-policies"></a>Verifieringsprinciper
| Principnamn | Beskrivning | Tillämpas på | Krävs | Standard |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Kontrollerar att data i **Azure Blob storage** uppfyller minsta storlek (i megabyte). |Azure Blob Storage |Nej |Ej tillämpligt |
| minimumRows |Kontrollerar att data i en **Azure SQL-databas** eller en **Azure-tabell** innehåller det minsta antalet rader. |<ul><li>Azure SQL-databas</li><li>Azure-tabell</li></ul> |Nej |Ej tillämpligt |

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
Externa datauppsättningar är de som inte kommer från en aktiv pipeline i datafabriken. Om datauppsättningen är markerad som **externa**, **ExternalData** princip kan definieras för att påverka beteendet för tillgänglighet för datauppsättningar sektorn.

Om inte en datauppsättningen produceras av Data Factory, bör det markeras som **externa**. Den här inställningen gäller vanligtvis indata för den första aktiviteten i en pipeline, såvida inte aktivitet eller länkning av pipelinen som används.

| Namn | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| dataDelay |Tiden att fördröja kontrollera tillgängligheten för externa data för givna sektorn. Du kan till exempel fördröja en kontroll av per timme med den här inställningen.<br/><br/>Inställningen gäller endast för den aktuella tiden. Om det är 1:00 PM just nu och det här värdet är 10 minuter, till exempel startar verifieringen klockan 13:10.<br/><br/>Observera att den här inställningen inte påverkar segment i förflutna. Skär med **sluttid för sektor** + **dataDelay** < **nu** bearbetas utan fördröjning.<br/><br/>Gånger större än 23:59 timmar ska anges med hjälp av den `day.hours:minutes:seconds` format. Till exempel vill ange 24 timmar, Använd inte 24:00:00. Använd i stället 1.00:00:00. Om du använder 24:00:00, behandlas den som 24 dagar (24.00:00:00). För 1 dag och fyra timmar, anger du 1:04:00:00. |Nej |0 |
| retryInterval |Väntetiden mellan ett fel och nästa försök. Den här inställningen gäller för aktuell tid. Om den tidigare misslyckade, nästa försök är efter den **retryInterval** period. <br/><br/>Om den är 1:00 PM just nu kan börja vi första försöket. Om tid att slutföra första valideringskontrollen är 1 minut och åtgärden misslyckades, nästa återförsök var 1:00 + 1 minut (varaktighet) + 1min (återförsöksintervallet) = 1:02 PM. <br/><br/>Det finns ingen fördröjning för segment i förflutna. Återförsök sker omedelbart. |Nej |00:01:00 (1 minute) |
| retryTimeout |Tidsgränsen för varje nytt försök.<br/><br/>Om den här egenskapen anges till 10 minuter, ska verifieringen ha slutförts inom 10 minuter. Om det tar längre tid än 10 minuter att utföra valideringen tidsgränsen återförsök.<br/><br/>Om alla försök för timeout för verifiering sektorn markeras som **nådde sin tidsgräns**. |Nej |00:10:00 (10 minuter) |
| maximumRetry |Hur många gånger för att kontrollera tillgänglighet för externa data. Det högsta tillåtna värdet är 10. |Nej |3 |


## <a name="create-datasets"></a>Skapa datauppsättningar
Du kan skapa datauppsättningar med någon av dessa verktyg och SDK: er:

- Guiden Kopiera
- Azure Portal
- Visual Studio
- PowerShell
- Azure Resource Manager-mall
- REST-API
- .NET-API

Se följande självstudiekurser för stegvisa instruktioner för att skapa pipelines och datauppsättningar med någon av dessa verktyg och SDK: er:

- [Skapa en pipeline med en datatransformeringsaktivitet](data-factory-build-your-first-pipeline.md)
- [Skapa en pipeline med en aktivitet för flytt av data](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

När en pipeline skapas och distribueras, kan du hantera och övervaka dina pipelines med hjälp av Azure-portalblad eller appen övervakning och hantering. Se följande avsnitt hittar du stegvisa instruktioner:

- [Övervaka och hantera pipelines med hjälp av Azure-portalblad](data-factory-monitor-manage-pipelines.md)
- [Övervaka och hantera pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Begränsade datauppsättningar
Du kan skapa datauppsättningar som är begränsade till en pipeline med hjälp av den **datauppsättningar** egenskapen. Dessa datauppsättningar kan bara användas av aktiviteter i denna pipeline, inte av aktiviteter i andra pipeliner. I följande exempel definierar en pipeline med två datauppsättningar (rdc av InputDataset och OutputDataset rdc) som ska användas i pipeline.

> [!IMPORTANT]
> Begränsade datauppsättningar stöds endast med enstaka pipelines (där **pipelineMode** är inställd på **OneTime**). Se [Onetime pipeline](data-factory-create-pipelines.md#onetime-pipeline) mer information.
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
- Läs mer om hur pipelines schemaläggs och körs, [schemaläggning och körning i Azure Data Factory](data-factory-scheduling-and-execution.md).
