---
title: "Schemaläggning och körning med Data Factory | Microsoft Docs"
description: "Lär dig schemaläggning och körning av aspekter av Azure Data Factory programmodell."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: d1cd846418b0e65b978971526af7b918bd03a7a1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory schemaläggning och körning
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [pipeline körning och utlösare](../concepts-pipeline-execution-triggers.md) artikel.

I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. Den här artikeln förutsätter att du förstår grunderna för Data Factory programmet modellen begrepp, inklusive aktivitet, rörledningar, länkade tjänster och datauppsättningar. Grundläggande begrepp för Azure Data Factory finns i följande artiklar:

* [Introduktion till Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Datauppsättningar](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Start- och sluttider för pipeline
En pipeline är aktiv endast mellan dess **starta** tid och **end** tid. Det utförs inte före starttiden eller efter sluttid. Om pipeline är pausad körs den inte oavsett dess start- och tid. För en rörledning för att köra, bör det inte pausas. Du hittar dessa inställningar (start, slut, pausats) i pipeline-definition: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Mer information finns i de här egenskaperna [skapa pipelines](data-factory-create-pipelines.md) artikel. 


## <a name="specify-schedule-for-an-activity"></a>Ange schemat för en aktivitet
Det är inte den pipeline som körs. Det är aktiviteterna i pipeline som körs i kontexten övergripande av pipeline. Du kan ange ett återkommande schema för en aktivitet med hjälp av den **scheduler** delen av aktivitets-JSON. Exempelvis kan du schemalägga en aktivitet för att köra varje timme på följande sätt:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

I följande diagram visas att ange ett schema för en aktivitet skapas en serie rullande fönster med i pipeline-start- och sluttider. Rullande fönster är en serie med fast storlek inte överlappar, sammanhängande intervall. Dessa logiska rullande fönster för en aktivitet kallas **aktivitet windows**.

![Aktiviteten scheduler-exempel](media/data-factory-scheduling-and-execution/scheduler-example.png)

Den **scheduler** -egenskapen för en aktivitet är valfria. Om du anger den här egenskapen, måste den matcha det intervall som du anger i definitionen av datamängd för utdata för aktiviteten. Schemat styrs för närvarande av utdatamängd. Därför måste du skapa en datamängd för utdata, även om aktiviteten inte producerar några utdata. 

## <a name="specify-schedule-for-a-dataset"></a>Ange schemat för en datamängd
En aktivitet i en Data Factory-pipelinen har noll eller fler indata **datauppsättningar** och skapa en eller flera utdata-datauppsättningar. För en aktivitet kan du ange det intervall då finns indata eller utdata skapas med hjälp av den **tillgänglighet** avsnitt i dataset-definitioner. 

**Frekvensen** i den **tillgänglighet** avsnittet anger tidsenheten. Tillåtna värden för frekvens är: minut, timma, dag, vecka och månad. Den **intervall** egenskap i avsnittet tillgänglighet anger en multiplikator för frekvens. Exempel: om frekvensen är inställd på dagen och intervallet anges till 1 för en datamängd för utdata, utdata skapas varje dag. Om du anger frekvensen som minut, rekommenderar vi att du anger intervallet till mindre än 15. 

I följande exempel indata finns varje timme och utdata skapas varje timme (`"frequency": "Hour", "interval": 1`). 

**Indatauppsättning:** 

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


**Datamängd för utdata**

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

För närvarande **utdatauppsättningen enheter schemat**. Det schema som angetts för datamängd för utdata är med andra ord används för att köra en aktivitet vid körning. Därför måste du skapa en datamängd för utdata, även om aktiviteten inte producerar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. 

I följande pipeline-definition i **scheduler** egenskapen används för att ange schemat för aktiviteten. Den här egenskapen är valfri. För närvarande måste schemat för aktiviteten matcha det schema som angetts för datamängd för utdata.
 
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

I det här exemplet körs aktiviteten varje timme mellan start- och sluttider för pipeline. Utdata skapas varje timme för tre timmars windows (8: 00 – 9 AM, 9: 00 – 10: 00, och 10 AM - 11: 00). 

Varje dataenhet förbrukas eller produceras av en aktivitet som körs kallas en **datasektorn**. Följande diagram visar ett exempel på en aktivitet med en inkommande datauppsättning och en utdatauppsättning: 

![Tillgänglighet Schemaläggaren](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diagrammet visar timvis datasektorer för inkommande och utgående dataset. Diagrammet visar tre inkommande segment som är klara för bearbetning. Aktiviteten 10 11 AM pågår, producerar 10 11 AM utdata sektorn. 

Du kan komma åt det tidsintervall som är associerade med det aktuella segmentet i datauppsättningen JSON med hjälp av variabler: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) och [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). På liknande sätt kan du komma åt det tidsintervall som är associerad med ett fönster med en aktivitet med hjälp av WindowStart och WindowEnd. Schemat för en aktivitet måste stämma överens med schemat för datamängd för utdata för aktiviteten. Därför är värdena SliceStart och SliceEnd samma som WindowStart och WindowEnd värden respektive. Mer information om dessa variabler finns [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md#data-factory-system-variables) artiklar.  

Du kan använda dessa variabler för olika ändamål i din aktivitets-JSON. Du kan till exempel använda dem för att hämta data från inkommande och utgående datauppsättningar som representerar tid series-data (till exempel: 8: 00 till 9: 00). Det här exemplet använder också **WindowStart** och **WindowEnd** för att välja relevanta data för en aktivitet körs och kopierar den till en blob med lämplig **folderPath**. Den **folderPath** parametriserade om du vill ha en separat mapp för varje timme.  

I föregående exempel schemat som angetts för indata och utdata-datauppsättningar är den samma (varje timme). Om den inkommande datamängden för aktiviteten är tillgängligt på en annan frekvens Säg var 15: e minut körs aktiviteten som producerar datauppsättningen utdata fortfarande en gång i timmen som datamängd för utdata är styr aktiviteten schemat. Mer information finns i [modell datauppsättningar med olika frekvenser](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>DataSet tillgänglighet och principer
Du har sett användning av frekvensen och intervall egenskaper i avsnittet tillgänglighet i datauppsättningsdefinitionen. Det finns några andra egenskaper som påverkar schemaläggningen och körning av en aktivitet. 

### <a name="dataset-availability"></a>DataSet-tillgänglighet 
Följande tabell beskriver egenskaper som kan användas i den **tillgänglighet** avsnitt:

| Egenskap | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för dataset sektorn produktion.<br/><br/><b>Stöd för frekvens</b>: minut, timma, dag, vecka, månad |Ja |Ej tillämpligt |
| interval |Anger en multiplikator för frekvens<br/><br/>”X frekvensintervall” avgör hur ofta sektorn skapas.<br/><br/>Om du behöver datamängden som segmenterat timme kan du ange <b>frekvens</b> till <b>timme</b>, och <b>intervall</b> till <b>1</b>.<br/><br/><b>Obs</b>: Om du anger frekvens som minut, rekommenderar vi att du anger intervallet till mindre än 15 |Ja |Ej tillämpligt |
| format |Anger om sektorn ska produceras start/slutet av intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Om frekvensen är inställd på månad och stil anges till EndOfInterval producerade sektorn på den sista dagen i månaden. Om formatet som har angetts till StartOfInterval producerade sektorn på den första dagen i månaden.<br/><br/>Om frekvensen är inställd på dagen och stil anges till EndOfInterval producerade sektorn under den senaste timmen på dagen.<br/><br/>Om frekvensen är inställd på timme och stil anges till EndOfInterval producerade sektorn i slutet av en timme. För ett segment för PM 1 – 2 PM period, till exempel produceras sektorn klockan 2. |Nej |EndOfInterval |
| anchorDateTime |Definierar absolut placering i tid som används av Schemaläggaren för att beräkna dataset sektorn gränser. <br/><br/><b>Obs</b>: om AnchorDateTime har datumdelar som är mer detaljerad än frekvensen sedan mer detaljerade delar ignoreras. <br/><br/>Till exempel om den <b>intervall</b> är <b>varje timme</b> (frekvens: timme och intervall: 1) och <b>AnchorDateTime</b> innehåller <b>minuter och sekunder</b>, sedan <b>minuter och sekunder</b> delar av AnchorDateTime ignoreras. |Nej |01/01/0001 |
| förskjutning |TimeSpan som start- och slutdatum för alla dataset segment flyttat. <br/><br/><b>Obs</b>: om både anchorDateTime och offset anges, resultatet är kombinerade SKIFT. |Nej |Ej tillämpligt |

### <a name="offset-example"></a>Offset exempel
Som standard varje dag (`"frequency": "Day", "interval": 1`) segment som börjar vid 12: 00 UTC-tid (midnatt). Om du vill att starttiden ska 06: 00 UTC-tid i stället ange förskjutningen som visas i följande fragment: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime-exempel
I följande exempel skapas datauppsättningen var 23: e timme. Det första segmentet börjar vid den tid som anges av anchorDateTime som har angetts till `2017-04-19T08:00:00` (UTC-tid).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>förskjutning/stil exempel
Följande datauppsättningen månatliga datauppsättning och skapas på 3: e varje månad kl 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>DataSet-princip
En dataset kan ha en verifieringsprincip har definierats som anger hur data som genereras av en sektor körning kan valideras innan den är klar för användning. I sådana fall när sektorn är klar körning och utdata sektorn statusen ändras till **väntar på** med en substatus av **validering**. När segmenten verifieras sektorn status ändras till **klar**. Om en datasektorn har producerats men klarade inte valideringen, bearbetas inte aktiviteten körs för underordnade segment som är beroende av det här segmentet. [Övervaka och hantera pipelines](data-factory-monitor-manage-pipelines.md) beskriver de olika lägena för datasektorer i Data Factory.

Den **princip** avsnitt i datauppsättningsdefinitionen definierar villkoren eller det villkor som dataset-segment måste vara uppfyllda. Följande tabell beskriver egenskaper som kan användas i den **princip** avsnitt:

| Principnamn | Beskrivning | Tillämpas på | Krävs | Standard |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Validerar att data i en **Azure blob** uppfyller minsta storlek (i megabyte). |Azure-blobb |Nej |Ej tillämpligt |
| minimumRows | Validerar att data i en **Azure SQL database** eller en **Azure-tabellen** innehåller det minsta antalet rader. |<ul><li>Azure SQL Database</li><li>Azure-tabellen</li></ul> |Nej |Ej tillämpligt |

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

Mer information om dessa egenskaper och exempel finns [skapa datauppsättningar](data-factory-create-datasets.md) artikel. 

## <a name="activity-policies"></a>Principer för användaraktivitet
Principer påverkar beteendet körning av en aktivitet, särskilt när segment i en tabell som har bearbetats. Följande tabell innehåller information.

| Egenskap | Tillåtna värden | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| Concurrency |Integer <br/><br/>Värdet för maximalt antal: 10 |1 |Antal samtidiga körningar av aktiviteten.<br/><br/>Den avgör antalet ParallellAktivitet körningar som kan inträffa på olika segment. Till exempel om en aktivitet behöver gå igenom snabbare en stor mängd tillgänglig data, med ett större värde för samtidighet behandling. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Anger den sorteringen av datasektorer som bearbetas.<br/><br/>Till exempel om du har 2 sektorer (en inträffar klockan 4, och en annan kl) och båda finns väntande körning. Om du ställer in executionPriorityOrder ska NewestFirst bearbetas sektorn kl först. På liknande sätt om du ställer in executionPriorityORder ska OldestFIrst bearbetas sedan sektorn klockan 4. |
| retry |Integer<br/><br/>Värdet för maximalt antal kan vara 10 |0 |Antal försök innan databearbetning för sektorn som har markerats som ett fel. Aktivitetskörningen för en datasektorn försöks upp till antalet angivna försök igen. Det nya försöket görs så snart som möjligt efter fel. |
| timeout |TimeSpan |00:00:00 |Tidsgräns för aktiviteten. Exempel: 00:10:00 (inbegriper timeout 10 minuter)<br/><br/>Om ett värde har angetts eller är 0, är tidsgränsen obegränsad.<br/><br/>Om databearbetningstiden på ett segment överskrider timeout-värdet, det avbryts och försöker systemet att försök bearbetning. Antalet försök beror på egenskapen försök igen. När timeout uppstår är status för lång tid. |
| Fördröjning |TimeSpan |00:00:00 |Ange fördröjning innan databearbetningen av sektorn startar.<br/><br/>Körningen av aktiviteten för en datasektorn startas efter fördröjningen har passerat den förväntade tiden för körningen.<br/><br/>Exempel: 00:10:00 (inbegriper fördröjning på 10 minuter) |
| longRetry |Integer<br/><br/>Värdet för maximalt antal: 10 |1 |Antal lång nya försök innan körningen sektorn misslyckades.<br/><br/>longRetry försök fördelade av longRetryInterval. Så om du behöver ange en tid mellan nya försök använda longRetry. Om både försök och longRetry anges varje longRetry försök omförsök max antal försök används och försök igen * longRetry.<br/><br/>Till exempel om vi har följande inställningar i principen för aktiviteten:<br/>Försök: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Anta att det finns endast en sektor att köra (status väntar) och aktivitetskörningen misslyckas varje gång. Det skulle inledningsvis 3 körning på varandra följande försök. Efter varje försök är sektorn status försök igen. Efter första 3 försök över är statusen för sektorn LongRetry.<br/><br/>Efter en timme (det vill säga Longretryinteval's värde), skulle det finnas en annan uppsättning 3 körning på varandra följande försök. Efter detta segment status skulle misslyckades och inga fler försök kan inte genomföras. Därför har övergripande 6 försök gjorts.<br/><br/>Om alla körning lyckas, statusen för sektorn är klar och inga fler försök görs.<br/><br/>longRetry får användas i situationer där beroende data kommer till icke-deterministiska gånger eller hela miljön är flaky under vilken databearbetningen sker. I sådana fall kan detta återförsök efter varandra inte kan hjälpa och gör att när ett intervall på tid som resulterar i önskad utdata.<br/><br/>Liten varning: Ange inte höga värden för longRetry eller longRetryInterval. Normalt en högre värden andra systemfel problem. |
| longRetryInterval |TimeSpan |00:00:00 |Fördröjning mellan försök har lång |

Mer information finns i [Pipelines](data-factory-create-pipelines.md) artikel. 

## <a name="parallel-processing-of-data-slices"></a>Parallell bearbetning av datasektorer
Du kan ange startdatum för pipeline tidigare. När du gör det beräknas (tillbaka fyllning) alla datasektorer tidigare Data Factory automatiskt och börjar bearbeta dem. Exempel: Om du skapar en pipeline med startdatum 2017-04-01 och det aktuella datumet infaller 2017-04-10. Om intervall som datamängd för utdata är varje dag och sedan Data Factory startar bearbetning av alla segment från 2017-04-01 till 2017-04-09 omedelbart eftersom startdatum har passerats. Sektorn från 2017-04-10 bearbetas inte ännu eftersom värdet för style-egenskapen i avsnittet tillgänglighet är EndOfInterval som standard. Äldsta sektorn bearbetas först som standard är värdet för executionPriorityOrder OldestFirst. En beskrivning av egenskapen style finns [dataset tillgänglighet](#dataset-availability) avsnitt. En beskrivning av executionPriorityOrder-avsnittet, finns det [aktivitetsprinciper](#activity-policies) avsnitt. 

Du kan konfigurera tillbaka ifylld datasektorer bearbetas parallellt genom att ange den **samtidighet** egenskap i den **princip** delen av aktivitets-JSON. Den här egenskapen anger antalet ParallellAktivitet körningar som kan inträffa på olika segment. Standardvärdet för egenskapen samtidighet är 1. Därför bearbetas en sektor samtidigt som standard. Det maximala värdet är 10. När en pipeline behöver gå igenom en stor mängd tillgänglig data, med ett större värde för samtidighet snabbare behandling. 

## <a name="rerun-a-failed-data-slice"></a>Kör en misslyckad datasektorn
När ett fel uppstår under bearbetning av en datasektorn du reda på varför bearbetning av ett segment misslyckats med hjälp av Azure portal blad eller övervaka och hantera appen. Se [övervakning och hantering av pipelines med hjälp av Azure portal blad](data-factory-monitor-manage-pipelines.md) eller [övervakning och hantering av](data-factory-monitor-manage-app.md) mer information.

Överväg följande exempel som visar två aktiviteter. Activity1 och aktivitet 2. Activity1 använder ett segment av Dataset1 och genererar ett segment av Dataset2 som används som indata av Activity2 att skapa en sektor i den slutliga Dataset.

![Misslyckade sektorn](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagrammet visar att utanför tre senaste segment det uppstod ett fel som producerar 9 10 AM sektorn för Dataset2. Data Factory spårar automatiskt beroende för tid serien dataset. Därför kan startar den inte aktiviteten körs för den underordnade sektorn 9-10: 00.

Data Factory övervakning och hantering av verktygen kan du detaljerat diagnostikloggar att enkelt hitta den bakomliggande orsaken till problemet och åtgärda den misslyckade sektorn. När problemet har åtgärdats kan du enkelt starta aktiviteten kör för att skapa den misslyckade sektorn. Läs mer om hur du kör och förstå tillståndsövergångar för datasektorer [övervakning och hantering av pipelines med hjälp av Azure portal blad](data-factory-monitor-manage-pipelines.md) eller [övervakning och hantering av](data-factory-monitor-manage-app.md).

När du kör 9 10 AM sektorn för **Dataset2**, Data Factory startar kör för sektorn 9 10 AM beroende på den sista datamängden.

![Kör misslyckade sektorn](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Flera aktiviteter i en pipeline
Du kan fler än en aktivitet i en pipeline. Om du har flera aktiviteter i en pipeline och utdata för en aktivitet inte är indata för en annan aktivitet, kan aktiviteter köras parallellt om indata segment för aktiviteter är klar.

Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Aktiviteter kan vara i samma rörledning eller i olika pipelines. Den andra aktiviteten körs bara när den första som har slutförts.

Tänk dig följande om en pipeline har två aktiviteter:

1. Aktiviteten A1 som kräver indata externa datauppsättningen D1 och producerar utdatauppsättningen D2.
2. Aktiviteten A2 som kräver indata från dataset D2 och producerar utdatauppsättningen D3.

I det här scenariot finns aktiviteter A1 och A2 i samma rörledning. Aktiviteten A1 körs när externa data är tillgängliga och hur ofta schemalagd tillgänglighet har uppnåtts. Aktiviteten A2 körs när från D2 schemalagda segment bli tillgänglig och frekvens för schemalagd tillgänglighet har uppnåtts. Om det finns ett fel i en sektorer i dataset D2, körs inte A2 för den sektorn förrän den blir tillgänglig.

Diagramvy med både aktiviteter i samma rörledning ser ut som i följande diagram:

![Aktiviteter i samma pipeline-länkning](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Som nämnts tidigare kan aktiviteter vara i olika pipelines. I ett sådant scenario ser diagramvyn ut som i följande diagram:

![Länkning aktiviteter i två rörledningar](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Finns det [kopiera sekventiellt](#copy-sequentially) avsnitt i tillägget för ett exempel.

## <a name="model-datasets-with-different-frequencies"></a>Modellen datauppsättningar med olika frekvenser
I prover har frekvenser för inkommande och utgående datauppsättningar och schemafönstret aktivitet samma. Vissa scenarier kräver möjlighet att resultat med en frekvens som skiljer sig frekvenser på en eller flera inmatningar. Data Factory stöder modeling dessa scenarier.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exempel 1: Skapa en daglig utdata-rapport för inkommande data som är tillgängliga i timmen
Föreställ dig ett scenario där du har definierat mätdata från sensorer som är tillgängliga i timmen i Azure Blob storage. Du vill skapa en daglig sammanställda rapport med statistik som medelvärde, högsta och lägsta för dag med [Data Factory hive aktiviteten](data-factory-hive-activity.md).

Här visas hur du kan utforma det här scenariot med Data Factory:

**Indatauppsättning**

Varje timme indatafilerna bort i mappen för den angivna dagen. Tillgänglighet för indata är inställt på **timme** (frekvens: timme, intervall: 1).

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
**Datamängd för utdata**

En utdatafil skapas varje dag i dagens mapp. Tillgängligheten för utdata är inställt på **dag** (frekvens: dag och intervall: 1).

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

**Aktiviteten: hive aktivitet i en pipeline**

Hive-skript tar emot rätt *DateTime* information som parametrar som använder den **WindowStart** variabeln enligt följande kodavsnitt. Hive-skriptet använder den här variabeln för att läsa in data från rätt mapp för dag och kör sammanställning för att generera utdata.

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

Följande diagram illustrerar scenario ur en data-beroendet.

![Beroendet av data](./media/data-factory-scheduling-and-execution/data-dependency.png)

Sektorn utdata för varje dag beror på 24 timvis segment från en datamängd som indata. Data Factory beräknar automatiskt dessa beroenden genom att ta reda på indata segment som faller inom samma tidsperiod som utdata sektorn ska produceras. Om någon av de 24 inkommande segment inte är tillgänglig väntar Data Factory på indata sektorn ska bli klar innan du startar daglig aktivitet körs.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exempel 2: Ange samband med uttryck och Data Factory-funktioner
Nu ska vi titta ett annat scenario. Anta att du har en hive-aktivitet som bearbetar två inkommande datauppsättningar. En av dem har nya data varje dag, men en av dem hämtar nya data varje vecka. Anta att du vill göra en koppling mellan de två indatavärdena och skapar ett utgående varje dag.

Enkel metod i vilken Data Factory automatiskt siffrorna ut höger mata in segment att bearbeta genom att justera till utgående data tidsintervallet tidsperiod inte fungerar.

Du måste ange att för varje aktivitet som kör Data Factory ska använda föregående vecka datasektorn för varje vecka inkommande dataset. Du kan använda Azure Data Factory-funktioner som visas i följande fragment för att genomföra det här beteendet.

**Input1: Azure blob**

Första indata är Azure blob som uppdateras dagligen.

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

**Input2: Azure blob**

Input2 är Azure-blobben uppdateras varje vecka.

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

**Utdata: Azure blob**

En utdatafil skapas varje dag i mappen för dagen. Tillgängligheten för utdata är inställd på **dag** (frekvens: Day, intervall: 1).

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

**Aktiviteten: hive aktivitet i en pipeline**

Aktiviteten hive tar de två indatavärdena och genererar ett utdata segment varje dag. Du kan ange varje dag utdata sektion så att den är beroende av föregående vecka inkommande segment för varje vecka indata på följande sätt.

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
Det är möjligt att köra flera kopieringsåtgärder efter varandra på ett sätt som sekventiella/sorterade. Du kan till exempel ha två kopiera aktiviteter i en pipeline (CopyActivity1 och CopyActivity2) med följande indata utdata datauppsättningar:   

CopyActivity1

Indata: Dataset. Utdata: Dataset2.

CopyActivity2

Indata: Dataset2.  Utdata: Dataset3.

CopyActivity2 körs bara om CopyActivity1 har körts och Dataset2 är tillgänglig.

Här är exempel pipeline-JSON:

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

Observera att datamängd för utdata för den första kopia aktiviteten (Dataset2) har angetts i det här exemplet som indata för den andra aktiviteten. Därför kan körs den andra aktiviteten bara datamängd för utdata från den första aktiviteten är klar.  

I det här exemplet CopyActivity2 kan ha en annan inmatning, till exempel Dataset3, men du anger Dataset2 som indata till CopyActivity2, så att aktiviteten inte körs förrän CopyActivity1 har slutförts. Exempel:

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

Observera att två indatauppsättningar har angetts i det här exemplet för andra kopieringsaktiviteten. När flera inmatningar anges endast den första inkommande datauppsättningen används för att kopiera data, men andra datauppsättningar som används som beroenden. CopyActivity2 skulle startas endast efter att följande villkor är uppfyllda:

* CopyActivity1 har slutförts och Dataset2 är tillgänglig. Den här datauppsättningen används inte när du kopierar data till Dataset4. Det fungerar bara som ett schemaläggning beroende för CopyActivity2.   
* Dataset3 är tillgänglig. Den här datauppsättningen representerar de data som kopieras till målet. 