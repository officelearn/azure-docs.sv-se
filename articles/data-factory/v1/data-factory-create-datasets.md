---
title: Skapa datauppsättningar i Azure Data Factory
description: Lär dig hur du skapar datauppsättningar i Azure Data Factory, med exempel som använder egenskaper som offset och anchorDateTime.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 18a5e11d2341fb020fc442d2f9ce7c1d44de9d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260441"
---
# <a name="datasets-in-azure-data-factory"></a>Datauppsättningar i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-create-datasets.md)
> * [Version 2 (aktuell version)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Datauppsättningar i V2](../concepts-datasets-linked-services.md).

I den här artikeln beskrivs vilka datauppsättningar som är, hur de definieras i JSON-format och hur de används i Azure Data Factory-pipelines. Den innehåller information om varje avsnitt (till exempel struktur, tillgänglighet och princip) i den datauppsättning JSON-definitionen. Artikeln innehåller också exempel på hur du använder egenskaperna **för offset**, **anchorDateTime**och **format** i en JSON-definition för datauppsättning.

> [!NOTE]
> Om du inte har något nytt för Data Factory läser [du Introduktion till Azure Data Factory](data-factory-introduction.md) för en översikt. Om du inte har praktisk erfarenhet av att skapa datafabriker kan du få en bättre förståelse genom att läsa [dataomvandlingshandledningen](data-factory-build-your-first-pipeline.md) och [dataförflyttningshandledningen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från en lokal SQL Server till Azure Blob-lagring. Sedan kan du använda en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster för att bearbeta data från Blob-lagring för att producera utdata. Slutligen kan du använda en andra kopieringsaktivitet för att kopiera utdata till Azure SQL Data Warehouse, ovanpå vilka business intelligence -rapporteringslösningar (BI) skapas. Mer information om pipelines och aktiviteter finns [i Pipelines och aktiviteter i Azure Data Factory](data-factory-create-pipelines.md).

En aktivitet kan ta noll eller fler **indatauppsättningar**och producera en eller flera utdatauppsättningar. En indatauppsättning representerar indata för en aktivitet i pipelinen och en utdatauppsättning representerar utdata för aktiviteten. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. En Azure Blob-datauppsättning anger till exempel blob-behållaren och mappen i Blob-lagring från vilken pipelinen ska läsa data.

Innan du skapar en datauppsättning skapar du en **länkad tjänst** för att länka datalagret till datafabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Datauppsättningar identifierar data i länkade datalager, till exempel SQL-tabeller, filer, mappar och dokument. En Azure Storage-länkad tjänst länkar till exempel ett lagringskonto till datafabriken. En Azure Blob-datauppsättning representerar blob-behållaren och mappen som innehåller de indatablobar som ska bearbetas.

Här är ett exempelscenario. Om du vill kopiera data från Blob-lagring till en SQL-databas skapar du två länkade tjänster: Azure Storage och Azure SQL Database. Skapa sedan två datauppsättningar: Azure Blob-datauppsättning (som refererar till azure storage-länkade tjänsten) och Azure SQL Table-datauppsättningen (som refererar till azure SQL Database-länkade tjänsten). Azure Storage- och Azure SQL Database-länkade tjänster innehåller anslutningssträngar som Data Factory använder vid körning för att ansluta till din Azure Storage respektive Azure SQL Database. Azure Blob-datauppsättningen anger blob-behållaren och blob-mappen som innehåller indatablobar i blob-lagringen. Datauppsättningen för Azure SQL Table anger SQL-tabellen i SQL-databasen som data ska kopieras till.

I följande diagram visas relationerna mellan pipeline, aktivitet, datauppsättning och länkad tjänst i Data Factory:

![Förhållandet mellan pipeline, aktivitet, datauppsättning, länkade tjänster](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Dataset JSON
En datauppsättning i Data Factory definieras i JSON-format på följande sätt:

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

I följande tabell beskrivs egenskaper i ovanstående JSON:

| Egenskap | Beskrivning | Krävs | Default |
| --- | --- | --- | --- |
| namn |Datauppsättningens namn. Se [Azure Data Factory - Namngivningsregler](data-factory-naming-rules.md) för namngivningsregler. |Ja |Ej tillämpligt |
| typ |Typ av datauppsättning. Ange en av de typer som stöds av Data Factory (till exempel AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [Datauppsättningstyp](#Type). |Ja |Ej tillämpligt |
| Struktur |Schema för datauppsättningen.<br/><br/>Mer information finns i [Datauppsättningsstruktur](#Structure). |Inga |Ej tillämpligt |
| typeProperties | Typegenskaperna är olika för varje typ (till exempel Azure Blob, Azure SQL-tabell). Mer information om vilka typer som stöds och deras egenskaper finns i [Datauppsättningstyp](#Type). |Ja |Ej tillämpligt |
| extern | Boolesk flagga för att ange om en datauppsättning uttryckligen produceras av en datafabrikspipeline eller inte. Om indatauppsättningen för en aktivitet inte produceras av den aktuella pipelinen anger du flaggan som true. Ange den här flaggan till true för indatauppsättningen för den första aktiviteten i pipelinen.  |Inga |false |
| availability | Definierar bearbetningsfönstret (till exempel varje timme eller dagligen) eller skivningsmodellen för datauppsättningsproduktionen. Varje enhet med data som förbrukas och produceras av en aktivitetskörning kallas ett datasegment. Om tillgängligheten för en utdatauppsättning är inställd på daglig (frekvens - Dag, intervall - 1), produceras ett segment dagligen. <br/><br/>Mer information finns i Dataset-tillgänglighet. <br/><br/>Mer information om datauppsättningsuppsättningsmodellen finns i artikeln [Schemaläggning och körning.](data-factory-scheduling-and-execution.md) |Ja |Ej tillämpligt |
| policy |Definierar villkoret eller villkoret som datauppsättningssegmenten måste uppfylla. <br/><br/>Mer information finns i avsnittet [Datauppsättningsprincip.](#Policy) |Inga |Ej tillämpligt |

## <a name="dataset-example"></a>Exempel på datauppsättning
I följande exempel representerar datauppsättningen en tabell med namnet **MyTable** i en SQL-databas.

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

* **är** inställd på AzureSqlTable.
* **egenskapen tableName** type (specifik för AzureSqlTable-typen) är inställd på MyTable.
* **linkedServiceName** refererar till en länkad tjänst av typen AzureSqlDatabase, som definieras i nästa JSON-kodavsnitt.
* **tillgänglighetsfrekvensen** är inställd på Dag och **intervallet** är inställt på 1. Det innebär att datauppsättningssegmentet produceras dagligen.

**AzureSqlLinkedService** definieras på följande sätt:

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

I föregående JSON-utdrag:

* **är** inställd på AzureSqlDatabase.
* **egenskapen connectionString** type anger information som ska anslutas till en SQL-databas.

Som du kan se definierar den länkade tjänsten hur du ansluter till en SQL-databas. Datauppsättningen definierar vilken tabell som används som indata och utdata för aktiviteten i en pipeline.

> [!IMPORTANT]
> Om inte en datauppsättning produceras av pipelinen bör den markeras som **extern**. Den här inställningen gäller vanligtvis för indata från första aktiviteten i en pipeline.

## <a name="dataset-type"></a><a name="Type"></a>Datauppsättningstyp
Vilken typ av datauppsättning beror på vilket datalager du använder. Se följande tabell för en lista över datalager som stöds av Data Factory. Klicka på ett datalager om du vill lära dig hur du skapar en länkad tjänst och en datauppsättning för det datalagret.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan vara lokalt eller på Azure-infrastruktur som en tjänst (IaaS). Dessa datalager kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md).

I exemplet i föregående avsnitt anges typen av datauppsättning till **AzureSqlTable**. På samma sätt, för en Azure Blob-datauppsättning, är typen av datauppsättning inställd på **AzureBlob**, som visas i följande JSON:

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

## <a name="dataset-structure"></a><a name="Structure"></a>Datauppsättningsstruktur
**Strukturavsnittet** är valfritt. Den definierar schemat för datauppsättningen genom att innehålla en samling namn och datatyper av kolumner. Du kan använda strukturavsnittet för att ange typinformation som används för att konvertera typer och mappa kolumner från källan till målet. I följande exempel har datauppsättningen tre `slicetimestamp` `projectname`kolumner: `pageviews`, och . De är av typen Sträng, Sträng respektive Decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Varje kolumn i strukturen innehåller följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Kolumnens namn. |Ja |
| typ |Datatyp för kolumnen.  |Inga |
| Kultur |. NET-baserad odling som ska användas när typen `Datetime` `Datetimeoffset`är en .NET-typ: eller . Standardvärdet är `en-us`. |Inga |
| format |Formatera sträng som ska användas när typen `Datetime` är `Datetimeoffset`en .NET-typ: eller . |Inga |

Följande riktlinjer hjälper dig att avgöra när du ska **structure** inkludera strukturinformation och vad som ska ingå i strukturavsnittet.

* **För strukturerade datakällor**anger du bara strukturavsnittet om du vill att kartklumnerna ska sänka kolumnerna och deras namn inte är desamma. Den här typen av strukturerad datakälla lagrar dataschema och typinformation tillsammans med själva datan. Exempel på strukturerade datakällor är SQL Server, Oracle och Azure-tabellen.
  
    Eftersom typinformation redan är tillgänglig för strukturerade datakällor bör du inte inkludera typinformation när du inkluderar strukturavsnittet.
* **För schema på läsdatakällor (särskilt Blob-lagring)** kan du välja att lagra data utan att lagra schema- eller typinformation med data. För dessa typer av datakällor, inkludera struktur när du vill mappa källkolumner till sinkkolumner. Inkludera även struktur när datauppsättningen är en indata för en kopieringsaktivitet, och datatyper av källdatauppsättning bör konverteras till ursprungliga typer för diskhon.
    
    Data Factory stöder följande värden för att tillhandahålla typinformation i struktur: **Int16, Int32, Int64, Enkel, Dubbel, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset och Timespan**. Dessa värden är CLS-kompatibla (Common Language Specification),. NET-baserade typvärden.

Data Factory utför automatiskt typkonverteringar när data flyttas från ett källdatalager till ett sink-datalager.

## <a name="dataset-availability"></a>Tillgänglighet för datauppsättning
**Tillgänglighetsavsnittet** i en datauppsättning definierar bearbetningsfönstret (till exempel varje timme, dag eller vecka) för datauppsättningen. Mer information om aktivitetsfönster finns i [Schemaläggning och körning](data-factory-scheduling-and-execution.md).

I följande tillgänglighetsavsnitt anges att utdatauppsättningen antingen produceras per timme eller att indatauppsättningen är tillgänglig varje timme:

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

Utdatauppsättningen produceras varje timme inom pipelinens start- och sluttider. Därför finns det fem datamängdssegment som produceras av denna pipeline, en för varje aktivitetsfönster (12:00 - 01:00, 01:00 - 02:00, 02:00 - 03:00 till 04:00, 04:00 - 05:00).

I följande tabell beskrivs egenskaper som du kan använda i tillgänglighetsavsnittet:

| Egenskap | Beskrivning | Krävs | Default |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för datauppsättningssegmentproduktion.<br/><br/><b>Frekvens som stöds</b>: Minut, Timme, Dag, Vecka, Månad |Ja |Ej tillämpligt |
| interval |Anger en multiplikator för frekvens.<br/><br/>"Frekvens x intervall" avgör hur ofta segmentet produceras. Om du till exempel behöver datauppsättningen som ska segmenteras per timme anger du <b>frekvensen</b> till <b>Timme</b>och <b>intervall</b> till <b>1</b>.<br/><br/>Observera att om du anger **frekvensen** som **Minut**bör du ställa in intervallet till inte mindre än 15. |Ja |Ej tillämpligt |
| stil |Anger om segmentet ska produceras i början eller slutet av intervallet.<ul><li>StartofInterval</li><li>Slutavinföring</li></ul>Om **frekvensen** är inställd på **Månad**och **formatet** är inställt på **EndOfInterval**produceras segmentet den sista dagen i månaden. Om **formatet** är inställt på **StartOfInterval**produceras segmentet den första dagen i månaden.<br/><br/>Om **frekvensen** är inställd på **Dag**och **formatet** är inställt på **EndOfInterval**visas segmentet under den sista timmen på dagen.<br/><br/>Om **frekvensen** är inställd på **Timme**och **formatet** är inställt på **EndOfInterval**produceras segmentet i slutet av timmen. För ett segment för perioden 13.00-14.00 produceras segmentet klockan 14.00. |Inga |Slutavinföring |
| anchorDateTime |Definierar den absoluta positionen i tid som används av schemaläggaren för att beräkna datauppsättningssegmentgränser. <br/><br/>Observera att om den här egenskapen har datumdelar som är mer detaljerade än den angivna frekvensen ignoreras de mer detaljerade delarna. Om **intervallet** till exempel är **varje timme** (frekvens: timme och intervall: 1) och **anchorDateTime** innehåller **minuter och sekunder**ignoreras minuterna och sekunderna av **anchorDateTime.** |Inga |01/01/0001 |
| offset |Tidsintervall med vilket början och slutet av alla datauppsättningssegment flyttas. <br/><br/>Observera att om både **anchorDateTime** och **offset** anges, är resultatet det kombinerade skiftet. |Inga |Ej tillämpligt |

### <a name="offset-example"></a>exempel på förskjutning
Som standard börjar`"frequency": "Day", "interval": 1`dagliga ( ) segment vid 12 AM (midnatt) Coordinated Universal Time (UTC). Om du vill att starttiden ska vara 06:00 UTC-tid i stället ställer du in förskjutningen enligt följande utdrag:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime exempel
I följande exempel produceras datauppsättningen en gång var 23:e timme. Det första segmentet startar vid den tidpunkt som anges `2017-04-19T08:00:00` av **anchorDateTime**, som är inställt på (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>exempel på förskjutning/format
Följande datauppsättning är månadsvis och produceras den 3:e varje månad klockan 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Datauppsättningsprincip
**Principavsnittet** i datauppsättningsdefinitionen definierar villkoren eller villkoret som datauppsättningssegmenten måste uppfylla.

### <a name="validation-policies"></a>Valideringsprinciper
| Principnamn | Beskrivning | Tillämpas på | Krävs | Default |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Verifierar att data i **Azure Blob-lagring** uppfyller minimikraven för storlek (i megabyte). |Azure Blob Storage |Inga |Ej tillämpligt |
| minimumRows |Verifierar att data i en **Azure SQL-databas** eller en **Azure-tabell** innehåller det minsta antalet rader. |<ul><li>Azure SQL-databas</li><li>Azure-tabell</li></ul> |Inga |Ej tillämpligt |

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
Externa datauppsättningar är de som inte produceras av en pipeline som körs i datafabriken. Om datauppsättningen är markerad som **extern**kan principen **Externadata** definieras för att påverka datauppsättningens tillgänglighet.

Om inte en datauppsättning produceras av Data Factory ska den markeras som **extern**. Den här inställningen gäller vanligtvis för indata från första aktiviteten i en pipeline, såvida inte aktivitet eller pipeline-kedja används.

| Namn | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| dataDelay |Tiden för att fördröja kontrollen av tillgängligheten för externa data för det angivna segmentet. Du kan till exempel fördröja en timkontroll med den här inställningen.<br/><br/>Inställningen gäller endast för den aktuella tiden. Om det till exempel är 13:00 just nu och det här värdet är 10 minuter börjar valideringen 13:10.<br/><br/>Observera att den här inställningen inte påverkar segment tidigare. Segment med **segmentsluttidsdataLägg** + **dataDelay** < **nu** bearbetas utan dröjsmål.<br/><br/>Tider som är större än 23:59 `day.hours:minutes:seconds` timmar bör anges med hjälp av formatet. Om du till exempel vill ange 24 timmar ska du inte använda 24:00:00. Använd i stället 1.00:00:00. Om du använder 24:00:00 behandlas den som 24 dagar (24.00:00:00). För 1 dag och 4 timmar anger du 1:04:00:00. |Inga |0 |
| retryInterval |Väntetiden mellan ett fel och nästa försök. Den här inställningen gäller för närvarande. Om det föregående försöket misslyckades är nästa försök efter **återförsökIntervalperioden.** <br/><br/>Om det är 13:00 just nu, börjar vi första försöket. Om varaktigheten för att slutföra den första valideringskontrollen är 1 minut och åtgärden misslyckades, är nästa nytt försök på 1:00 + 1min (varaktighet) + 1min (återförsöksintervall) = 1:02 PM. <br/><br/>För segment i det förflutna finns det ingen fördröjning. Återförsöket sker omedelbart. |Inga |00:01:00 (1 minut) |
| försöktimeout igen |Tidsgränsen för varje försök till nytt försök.<br/><br/>Om den här egenskapen är inställd på 10 minuter ska valideringen slutföras inom 10 minuter. Om det tar längre tid än 10 minuter att utföra valideringen, time out.If it takes longer than 10 minutes to perform the validation, the retry time out.<br/><br/>Om alla försök till timeout för validering markeras segmentet som **TimedOut**. |Inga |00:10:00 (10 minuter) |
| maximumRetry |Antalet gånger för att kontrollera om de externa data finns tillgängliga. Det högsta tillåtna värdet är 10. |Inga |3 |


## <a name="create-datasets"></a>Skapa datauppsättningar
Du kan skapa datauppsättningar med något av följande verktyg eller SDK:er:

- Guiden Kopiera
- Visual Studio
- PowerShell
- Azure Resource Manager-mall
- REST API
- .NET-API

Se följande självstudier för steg-för-steg-instruktioner för att skapa pipelines och datauppsättningar med hjälp av något av dessa verktyg eller SDK:er:

- [Skapa en pipeline med en datatransformeringsaktivitet](data-factory-build-your-first-pipeline.md)
- [Skapa en pipeline med en dataförflyttningsaktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

När en pipeline har skapats och distribuerats kan du hantera och övervaka dina pipelines med hjälp av Azure-portalbladen eller appen Övervakning och hantering. Se följande avsnitt för steg-för-steg-instruktioner:

- [Övervaka och hantera pipelines med hjälp av Azure portalblad](data-factory-monitor-manage-pipelines.md)
- [Övervaka och hantera pipelines med hjälp av appen Övervakning och hantering](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Begränsade datauppsättningar
Du kan skapa datauppsättningar som är begränsade till en pipeline med hjälp av egenskapen **datauppsättningar.** Dessa datamängder kan endast användas av aktiviteter inom den här rörledningen, inte av aktiviteter i andra rörledningar. I följande exempel definieras en pipeline med två datauppsättningar (InputDataset-rdc och OutputDataset-rdc) som ska användas i pipelinen.

> [!IMPORTANT]
> Begränsade datauppsättningar stöds endast med engångspipeljor (där **pipelineMode** är inställt på **OneTime**). Mer information finns i [Onetime pipeline.](data-factory-create-pipelines.md#onetime-pipeline)
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
- Mer information om pipelines finns i [Skapa pipelines](data-factory-create-pipelines.md).
- Mer information om hur pipelines schemaläggs och körs finns [i Schemaläggning och körning i Azure Data Factory](data-factory-scheduling-and-execution.md).
