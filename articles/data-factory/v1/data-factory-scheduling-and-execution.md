---
title: Schemaläggning och körning med Data Factory | Microsoft Docs
description: Lär dig aspekter för schemaläggning och körning av Azure Data Factory-programmodellen.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 2d7fc45faf1fb77c7d9181e5a2419096dd1ad0f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258989"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory schemaläggning och körning
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [pipelinekörning och utlösare](../concepts-pipeline-execution-triggers.md) artikeln.

I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. Den här artikeln förutsätter att du förstår grunderna för Data Factory programmet modellen begrepp, inklusive aktivitet, pipelines, länkade tjänster och datauppsättningar. Grundläggande begrepp för Azure Data Factory finns i följande artiklar:

* [Introduktion till Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Datauppsättningar](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Start- och sluttider för pipeline
En pipeline är aktiv endast mellan dess **starta** tid och **slutet** tid. Då utförs inte före starttiden eller efter sluttiden. Om pipelinen är pausad, utförs då inte oavsett dess start- och tid. För en pipeline kan köras, bör det inte pausas. Du hittar de här inställningarna (start, slut, pausas) i pipeline-definition: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Läs mer om de här egenskaperna [skapa pipelines](data-factory-create-pipelines.md) artikeln. 


## <a name="specify-schedule-for-an-activity"></a>Ange schema för en aktivitet
Det är inte den pipeline som körs. Det är aktiviteterna i pipelinen som körs i kontexten övergripande för pipelinen. Du kan ange ett återkommande schema för en aktivitet med hjälp av den **scheduler** delen av aktivitets-JSON. Exempelvis kan du schemalägga en aktivitet för att köras varje timme på följande sätt:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

I följande diagram visas att ange ett schema för en aktivitet skapar ett antal rullande fönster med i pipelinens start- och sluttider. Rullande fönster är en serie med fast storlek inte överlappar, sammanhängande tidsintervall. Dessa logiska rullande fönster för en aktivitet anropas **aktivitetsfönster**.

![Aktivitet scheduler-exempel](media/data-factory-scheduling-and-execution/scheduler-example.png)

Den **scheduler** egenskapen för en aktivitet är valfri. Om du anger den här egenskapen, måste den matcha det intervall som du anger i definitionen av datauppsättningen för utdata för aktiviteten. Schemat styrs för närvarande av utdatamängd. Därför måste du skapa en utdatauppsättning även om aktiviteten inte producerar några utdata. 

## <a name="specify-schedule-for-a-dataset"></a>Ange schema för en datauppsättning
En aktivitet i en Data Factory-pipeline kan ha noll eller flera **datauppsättningar** och framställer en eller flera datauppsättningar som utdata. För en aktivitet, anger du det intervall då finns indata eller utdata skapas med hjälp av den **tillgänglighet** avsnittet i datauppsättningen definitionerna. 

**Frekvens** i den **tillgänglighet** avsnittet anger tidsenheten. Tillåtna värden för frekvens är: Minut, timme, dag, vecka och månad. Den **intervall** egenskap i avsnittet tillgänglighet anger en multiplikator för frekvensen. Till exempel: om frekvensen är inställd på dag och intervallet är inställd på 1 för en utdatauppsättning, utdata skapas varje dag. Om du anger frekvensen som minut, rekommenderar vi att du ställer in intervall på mindre än 15. 

I följande exempel indata är tillgängliga per timme och de utdata som genereras per timme (`"frequency": "Hour", "interval": 1`). 

**Datauppsättningen för indata:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Datauppsättningen för utdata**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

För närvarande **utdatauppsättningen styr schemat**. Med andra ord används det schema som angetts för datauppsättningen för utdata för att köra en aktivitet vid körning. Därför måste du skapa en utdatauppsättning även om aktiviteten inte producerar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. 

I följande pipelinedefinition på **scheduler** egenskapen används för att ange schemat för aktiviteten. Den här egenskapen är valfri. För närvarande måste schemat för aktiviteten matcha det schema som angetts för datauppsättningen för utdata.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

Aktiviteten körs varje timme mellan start- och sluttider för pipelinen i det här exemplet. De utdata som genereras per timme för tre timmars windows (8 AM - 9 AM, 9: 00 - 10: 00, och 10 AM - 11: 00). 

Varje enhet med data som används eller produceras av en aktivitet som körs kallas en **datasektor**. Följande diagram visar ett exempel på en aktivitet med en indatauppsättning och en utdatauppsättning: 

![Tillgänglighet scheduler](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diagrammet visar timsegment data för datauppsättningen för indata och utdata. Diagrammet visar tre inkommande segment som är klara för bearbetning. 10 – 11 AM-aktivitet pågår, producera utdatasektorn 10-11 kl. 

Du kan komma åt det tidsintervall som är associerade med aktuell sektor i datauppsättningen JSON genom att använda variabler: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) och [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). På samma sätt kan du komma åt det tidsintervall som är associerade med ett aktivitetsfönster med hjälp av WindowStart och WindowEnd. Schemat för en aktivitet måste matcha schemat för datauppsättningen för utdata för aktiviteten. Därför är värdena SliceStart och SliceEnd samma som WindowStart och WindowEnd värden respektive. Mer information om dessa variabler finns i [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md#data-factory-system-variables) artiklar.  

Du kan använda dessa variabler för olika ändamål i din aktivitets-JSON. Exempel: du kan använda dem för att välja data från in- och utdatauppsättningar som representerar time series-data (till exempel: 8 är till 9 AM). Det här exemplet använder även **WindowStart** och **WindowEnd** för att välja relevanta data för en aktivitet körs och kopierar den till en blob med lämplig **folderPath**. Den **folderPath** parameteriserat om du vill ha en separat mapp för varje timme.  

I föregående exempel schemat som angetts för indata och utdata datauppsättningar är de samma (varje timme). Om datauppsättningen för indata för aktiviteten är tillgänglig på en annan frekvens säga att varje kvart körs en gång i timmen fortfarande den aktivitet som genererar den här datauppsättningen för utdata som är det som styr aktivitetsschemat. Mer information finns i [modellera datauppsättningar med olika frekvenser](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Tillgänglighet för datauppsättningar och principer
Du har sett användningen av frekvens och intervall egenskaper i avsnittet tillgänglighet i datauppsättningsdefinitionen. Det finns några andra egenskaper som påverkar schemaläggning och körning av en aktivitet. 

### <a name="dataset-availability"></a>Tillgänglighet för datauppsättningar 
I följande tabell beskrivs egenskaperna som du kan använda i den **tillgänglighet** avsnittet:

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för datauppsättningen sektorn produktion.<br/><br/><b>Stöds frekvens</b>: Minut, timme, dag, vecka, månad |Ja |Ej tillämpligt |
| interval |Anger en multiplikator för frekvensen<br/><br/>”X frekvensintervall” avgör hur ofta sektorn skapas.<br/><br/>Om du behöver datauppsättningen att delas timme kan du ställa in <b>frekvens</b> till <b>timme</b>, och <b>intervall</b> till <b>1</b>.<br/><br/><b>Obs!</b> Om du anger frekvens som minut, rekommenderar vi att du ställer in intervall på mindre än 15 |Ja |Ej tillämpligt |
| stil |Anger om sektorn ska produceras vid start/slut intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Om frekvensen är inställd på månad och format har angetts till EndOfInterval, producerade sektorn på den sista dagen i månaden. Om formatet har angetts till StartOfInterval producerade sektorn på den första dagen i månaden.<br/><br/>Om frekvensen är inställd på dagen och format har angetts till EndOfInterval, producerade sektorn under den senaste timmen på dagen.<br/><br/>Om frekvensen är inställd på timme och format har angetts till EndOfInterval, producerade sektorn i slutet av timmen. För en sektor under PM 1 – 2 PM, till exempel produceras sektorn klockan 2. |Nej |EndOfInterval |
| anchorDateTime |Definierar absolut position i tid som används av scheduler för att beräkna datauppsättning sektorn gränser. <br/><br/><b>Obs!</b> Om AnchorDateTime har datumdelar som är större än frekvensen ignoreras de mer detaljerade delarna. <br/><br/>Till exempel om den <b>intervall</b> är <b>per timme</b> (frequency: hour och interval: (1) och <b>AnchorDateTime</b> innehåller <b>minuter och sekunder</b>, kommer <b>minuter och sekunder</b> delar av AnchorDateTime ignoreras. |Nej |01/01/0001 |
| offset |TimeSpan som början och slutet av alla datauppsättningen sektorer beräkningsarbete. <br/><br/><b>Obs!</b> Om både anchorDateTime och förskjutning anges, skapas det kombinerade skiftet. |Nej |Ej tillämpligt |

### <a name="offset-example"></a>förskjutningen exempel
Som standard varje dag (`"frequency": "Day", "interval": 1`) sektorer som börjar vid 12: 00 UTC-tid (midnatt). Om du vill att starttiden vara 06: 00 UTC-tid i stället ange förskjutningen som visas i följande kodavsnitt: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime exempel
I följande exempel produceras datauppsättningen var 23: e timme. Första sektorn som börjar vid den tid som anges av anchorDateTime som har angetts till `2017-04-19T08:00:00` (UTC-tid).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>förskjutning och formatet exempel
Följande datauppsättningen är en månatlig datauppsättning och skapas på 3: e varje månad kl 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Princip för datauppsättning
En datauppsättning kan ha en verifieringsprincip har definierats som anger hur data som genereras av körning av en sektor kan valideras innan den är redo för användning. I sådana fall när sektorn har slutförts körningen sektorstatus utdata ändras till **väntar på** med en understatus av **verifiering**. Efter segment verifieras sektorstatus ändras till **redo**. Om en datasektor har skapats men klarade inte verifieringen, bearbetas inte aktivitetskörningar för underordnade sektorer som är beroende av den här sektorn. [Övervaka och hantera pipelines](data-factory-monitor-manage-pipelines.md) beskriver de olika lägena för datasektorer i Data Factory.

Den **princip** avsnittet i definitionen av datauppsättningen definierar kriterierna eller villkor som datauppsättning segment måste vara uppfyllda. I följande tabell beskrivs egenskaperna som du kan använda i den **princip** avsnittet:

| Principnamn | Beskrivning | Tillämpas på | Krävs | Standard |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Kontrollerar att data i en **Azure blob** uppfyller minsta storlek (i megabyte). |Azure-blobb |Nej |Ej tillämpligt |
| minimumRows | Kontrollerar att data i en **Azure SQL-databas** eller en **Azure-tabell** innehåller det minsta antalet rader. |<ul><li>Azure SQL Database</li><li>Azure-tabell</li></ul> |Nej |Ej tillämpligt |

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

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Mer information om dessa egenskaper och exempel finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikeln. 

## <a name="activity-policies"></a>Aktivitetsprinciper
Principer påverkar körningsbeteende för en aktivitet, särskilt när sektorn i en tabell har bearbetats. I följande tabell innehåller information.

| Egenskap  | Tillåtna värden | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| samtidighet |Integer <br/><br/>Maxvärde: 10 |1 |Antal samtidiga körningar av aktiviteten.<br/><br/>Den avgör antalet körningar för parallell aktivitet som kan inträffa på olika segment. Till exempel om en aktivitet behöver genomgå påskyndar en stor mängd tillgängliga data, med ett större värde för samtidighet databearbetningen. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Anger sorteringen av datasektorer som bearbetas.<br/><br/>Till exempel om du har 2 skär (en händer klockan 4 och en ny 17: 00) och båda är väntar på att köras. Om du ställer in executionPriorityOrder vara NewestFirst bearbetas sektor 17: 00 först. På samma sätt om du ställer in executionPriorityORder vara OldestFIrst bearbetas sedan sektorn klockan 4. |
| retry |Integer<br/><br/>Max-värdet kan vara 10 |0 |Antal försök innan databearbetningen för sektorn har markerats som ett fel. Körningsmiljön för aktiviteten för en datasektor göras upp till det angivna antalet återförsök. Återförsök sker så snart som möjligt efter felet. |
| timeout |TimeSpan |00:00:00 |Tidsgränsen för aktiviteten. Exempel: 00:10:00 (inbegriper timeout 10 minuter)<br/><br/>Om ett värde inte har angetts eller är 0, är tidsgränsen oändliga.<br/><br/>Om bearbetningstiden som data på en sektor överskrider timeout-värdet, den avbryts och försöker systemet att försök bearbetningen. Antal återförsök beror på egenskapen försök igen. När timeout uppstår är status nådde sin tidsgräns. |
| fördröjning |TimeSpan |00:00:00 |Ange fördröjning före databearbetningen av sektorn startar.<br/><br/>Körningen av aktiviteten för en datasektor startas när fördröjningen har passerat den förväntade tiden för körningen.<br/><br/>Exempel: 00:10:00 (inbegriper fördröjning på 10 minuter) |
| longRetry |Integer<br/><br/>Maxvärde: 10 |1 |Antal långt återförsök innan sektorn körningen misslyckades.<br/><br/>longRetry försök är fördelade av longRetryInterval. Så om du vill ange en tid mellan nya försök använda longRetry. Om både återförsök och longRetry anges varje longRetry försök återförsök det maximala antalet försök används och försök igen * longRetry.<br/><br/>Till exempel, om vi har följande inställningar i aktivitetsprincipen:<br/>Gör om: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Anta att det finns endast en sektor att köra (status väntar) och körningsmiljön för aktiviteten misslyckas varje gång. Det skulle ursprungligen vara 3 körning av på varandra följande försök. Efter varje försök är statusen för sektorn försök igen. När det är första 3 försök över kan är statusen för sektorn LongRetry.<br/><br/>Efter en timme (det vill säga Longretryinteval's värde), skulle det finnas en annan uppsättning 3 körning av på varandra följande försök. Efter det skulle vara misslyckades sektorstatus och inga fler försök skulle göras. Därför har övergripande 6 försök gjorts.<br/><br/>Om alla körningen lyckades visas statusen för sektorn är klar och inga fler försök kommer att göras.<br/><br/>longRetry kan användas i situationer där beroende data kommer till icke-deterministisk gånger eller den övergripande miljön är flaky under vilken databearbetningen sker. I sådana fall kan göra återförsök efter varandra inte kan hjälpa och gör det när du har ett intervall på tid du leder till önskade utdata.<br/><br/>Liten varning: anger inte hög värden för longRetry eller longRetryInterval. Vanligtvis en högre värden andra systemfel problem. |
| longRetryInterval |TimeSpan |00:00:00 |Fördröjningen mellan långt återförsök |

Mer information finns i [Pipelines](data-factory-create-pipelines.md) artikeln. 

## <a name="parallel-processing-of-data-slices"></a>Parallell bearbetning av datasektorer
Du kan ange startdatum för pipelinen i förflutna. När du gör detta beräknas (backend fyller) alla datasektorer tidigare Data Factory automatiskt och börjar bearbeta dem. Till exempel: Om du skapar en pipeline med startdatum 2017-04-01 och det aktuella datumet är 2017-04-10. Om intervall av datauppsättningen för utdata är varje dag och Data Factory startar bearbetning av alla sektorer från 2017-04-01 till 2017-04-09 direkt, eftersom startdatumet är tidigare. Sektorn från 2017-04-10 bearbetas inte ännu eftersom värdet för formatmallegenskapen i avsnittet tillgänglighet är EndOfInterval som standard. Äldsta sektor bearbetas först som standard är värdet för executionPriorityOrder OldestFirst. En beskrivning av egenskapen style finns i [tillgänglighet för datauppsättningar](#dataset-availability) avsnittet. En beskrivning av avsnittet executionPriorityOrder finns i den [aktivitetsprinciper](#activity-policies) avsnittet. 

Du kan konfigurera tillbaka ifyllda datasektorer bearbetas parallellt genom att ange den **samtidighet** -egenskapen i den **princip** delen av aktivitets-JSON. Den här egenskapen anger antalet körningar för parallell aktivitet som kan inträffa på olika segment. Standardvärdet för egenskapen samtidighet är 1. Därför kan bearbetas en sektor samtidigt som standard. Det maximala värdet är 10. När en pipeline måste gå igenom ett stort antal tillgängliga data, med ett större värde för samtidighet påskyndar databearbetningen. 

## <a name="rerun-a-failed-data-slice"></a>Kör en misslyckad datasektor
När ett fel uppstår vid bearbetning av en datasektor, kan du se varför bearbetningen av sektorn misslyckades med hjälp av bladen på Azure portal eller appen övervaka och hantera. Se [övervaka och hantera pipelines med Azure-portalblad](data-factory-monitor-manage-pipelines.md) eller [övervakning och Hanteringsapp](data-factory-monitor-manage-app.md) mer information.

Överväg att i följande exempel som visar två aktiviteter. Activity1 och aktivitet 2. Activity1 använder en sektor av Dataset1 och skapar en sektor av Dataset2 som används som indata av Activity2 att skapa ett segment av den slutgiltiga uppsättningen.

![Misslyckade sektorn](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagrammet visar att utanför tre nyliga sektorer det uppstod ett fel som producerar 9 – 10 AM sektorn för Dataset2. Data Factory spårar automatiskt beroende för time series-datauppsättning. Därför kan startar den inte aktivitetskörningar för underordnade sektorn 9 – 10: 00.

Data Factory övervaknings- och -verktyg kan du öka detaljnivån i diagnostikloggar för misslyckade sektorn enkelt hitta den bakomliggande orsaken för problemet och åtgärda problemet. När du har åtgärdat problemet kan du enkelt starta aktivitetskörning för att skapa den misslyckade sektorn. Läs mer om hur du kör igen och förstå tillståndsövergångar för datasektorer [övervaka och hantera pipelines med Azure-portalblad](data-factory-monitor-manage-pipelines.md) eller [övervakning och Hanteringsapp](data-factory-monitor-manage-app.md).

När du kör 9 – 10 AM sektorn för **Dataset2**, Data Factory startar körningen av 9 – 10 AM beroende sektorn på den slutgiltiga uppsättningen.

![Kör misslyckade sektorn](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Flera aktiviteter i en pipeline
Du kan fler än en aktivitet i en pipeline. Om du har flera aktiviteter i en pipeline och utdata för en aktivitet är inte en indata för en annan aktivitet, kan aktiviteterna köras parallellt om indata segmenten för aktiviteterna är redo.

Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Aktiviteter kan vara i samma pipelinen eller i olika pipelines. Den andra aktiviteten körs bara när den första som har slutförts.

Anta exempelvis att följande om en pipeline har två aktiviteter:

1. Aktivitet A1 som kräver extern indatauppsättning D1 och skapar utdatauppsättningen D2.
2. Utdatauppsättning för aktiviteten A2 som kräver indata från datauppsättningen D2 och producerar D3.

I det här scenariot är aktiviteter A1 och A2 i samma pipelinen. A1 aktivitetskörningar när externa data är tillgängliga och frekvens för schemalagda tillgänglighet har nåtts. A2 aktivitetskörningar när schemalagda segment från D2 bli tillgänglig och frekvens för schemalagda tillgänglighet har nåtts. Om det finns ett fel i en sektorer i datauppsättningen D2, fungerar inte A2 för den sektorn tills den blir tillgänglig.

I diagramvyn med båda aktiviteter i samma pipelinen ser ut som i följande diagram:

![Länkning av aktiviteter i samma pipelinen](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Såsom nämnts tidigare kan aktiviteter befinna sig i olika pipelines. I ett sådant scenario ser diagramvyn ut som i följande diagram:

![Kedjesammansättning av aktiviteter i två pipelines](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Se kopian avsnittet sekventiellt i bilagan ett exempel.

## <a name="model-datasets-with-different-frequencies"></a>Modellen datauppsättningar med olika frekvenser
I exemplen har frekvenserna för in- och utdatauppsättningar och aktiviteten schemafönster samma. Vissa scenarier kräver möjligheten att producera utdata med en frekvens som är annorlunda än frekvenser på en eller flera inmatningar. Data Factory stöder modellering dessa scenarier.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exempel 1: Skapa en daglig utdata-rapport för indata som är tillgänglig varje timme
Tänk dig ett scenario där du har anger mätdata från sensorer som är tillgänglig varje timme i Azure Blob storage. Du vill skapa en daglig sammanställda rapport med statistik, som medelvärde, maximum och minimum för dagen med [hive-aktivitet för Data Factory](data-factory-hive-activity.md).

Här är hur du kan utforma det här scenariot med Data Factory:

**Datauppsättningen för indata**

Per timme indatafilerna ignoreras i mappen för den angivna dagen. Tillgänglighet för indata har ställts in till **timme** (frekvens: Timme, intervall: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Datauppsättningen för utdata**

Varje dag skapas en utdatafil i mappen för den dagen. Tillgängligheten för utdata har ställts in till **dag** (frekvens: Dag och intervall: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Aktivitet: hive-aktivitet i en pipeline**

Hive-skriptet tar emot rätt *DateTime* information som parametrar som använder den **WindowStart** variabeln som du ser i följande kodavsnitt. Hive-skriptet använder den här variabeln för att läsa in data från rätt mapp för dagen och kör sammansättning för att generera utdata.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

Följande diagram visar scenario från en data-beroendet synsätt.

![Databeroende](./media/data-factory-scheduling-and-execution/data-dependency.png)

Utdatasektorn för varje dag beror på 24 timsegment från en indatauppsättning. Data Factory beräknar automatiskt dessa beroenden genom att ta reda på indata sektorer som faller inom samma tidsperiod som utdatasektorn produceras. Om någon av 24 indatasektorerna inte är tillgänglig väntar Data Factory indatasektorn är klar innan du startar den dagliga aktivitetskörning.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exempel 2: Ange beroende med uttryck och Data Factory-funktioner
Anta att du ett annat scenario. Anta att du har en hive-aktivitet som bearbetar två indatauppsättningar. En av dem har nya data varje dag, men en av dem hämtar nya data varje vecka. Anta att du vill göra en koppling mellan de två indatavärdena och producera utdata varje dag.

Enkel metod i vilken Data Factory automatiskt lista ut höger mata in segment för att bearbeta genom att justera utdata data sektortid tidsperiod inte fungerar.

Du måste ange att för varje aktivitet som kör Data Factory ska använda förra veckan datasektor för veckovis datauppsättningen för indata. Du kan använda Azure Data Factory-funktioner som du ser i följande kodavsnitt som du implementerar det här beteendet.

**Indata1: Azure-blob**

Första indata är Azure-blobben håller på att uppdateras dagligen.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Indata2: Azure-blob**

Indata2 är Azure-blobben håller på att uppdateras varje vecka.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Utdata: Azure-blob**

En utdatafilen skapas varje dag i mappen för dagen. Tillgängligheten för utdata har angetts till **dag** (frekvens: Dag, intervall: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Aktivitet: hive-aktivitet i en pipeline**

Hive-aktiviteten tar två indata och genererar en utdatasektor varje dag. Du kan ange utdatasektor med varje dag för att beror på föregående vecka indatasektorn för veckovis indata på följande sätt.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Se [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md) en lista över funktioner och systemvariabler som har stöd för Data Factory.

## <a name="appendix"></a>Bilaga

### <a name="example-copy-sequentially"></a>Exempel: kopiera sekventiellt
Det går att köra flera kopieringsåtgärder efter varandra i ett sekventiella/ordnat sätt. Du kan exempelvis ha två kopieringsaktiviteter i en pipeline (CopyActivity1 och CopyActivity2) med följande utdata datauppsättningar för indata:   

CopyActivity1

Indata: Datauppsättningen. Utdata: Dataset2.

CopyActivity2

Indata: Dataset2.  Utdata: Dataset3.

CopyActivity2 körs bara om CopyActivity1 har kunnat köras och Dataset2 är tillgänglig.

Här är exempel-pipeline JSON:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observera att datamängd för utdata för kopieringsaktiviteten första (Dataset2) i det här exemplet anges som indata för den andra aktiviteten. Därför kan körs den andra aktiviteten endast när datauppsättningen för utdata från den första aktiviteten är klar.  

I det här exemplet CopyActivity2 kan ha en annan inmatning, till exempel Dataset3, men du anger Dataset2 som indata till CopyActivity2, så att aktiviteten inte körs förrän CopyActivity1 är klar. Exempel:

CopyActivity1

Indata: Dataset1. Utdata: Dataset2.

CopyActivity2

Indata: Dataset3, Dataset2. Utdata: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observera att två indatauppsättningar har angetts i det här exemplet för andra kopieringsaktiviteten. När flera inmatningar anges endast den första datauppsättningen för indata används för att kopiera data, men andra datauppsättningar används som beroenden. CopyActivity2 skulle startas endast efter att följande villkor är uppfyllda:

* CopyActivity1 har slutförts och Dataset2 är tillgänglig. Den här datauppsättningen används inte när du kopierar data till Dataset4. Den fungerar bara som ett schemaläggning beroende för CopyActivity2.   
* Dataset3 är tillgänglig. Den här datauppsättningen representerar de data som kopieras till målet. 
