---
title: Schemaläggning och körning med Data Factory
description: Lär dig schemaläggning och körning aspekter av Azure Data Factory programmodell.
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
ms.openlocfilehash: 15a2d6ae5d8b80468ffcdd00d60b1f36843ed677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281072"
---
# <a name="data-factory-scheduling-and-execution"></a>Schemaläggning och körning av datafabrik
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du artikeln [pipeline-körning och utlöser.](../concepts-pipeline-execution-triggers.md)

I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. Den här artikeln förutsätter att du förstår grunderna i datafabrikens programmodellbegrepp, inklusive aktivitet, pipelines, länkade tjänster och datauppsättningar. Grundläggande begrepp i Azure Data Factory finns i följande artiklar:

* [Introduktion till Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Datamängder](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Start- och sluttider för pipeline
En pipeline är bara aktiv mellan **starttiden** och **sluttiden.** Den körs inte före starttiden eller efter sluttiden. Om pipelinen pausas körs den inte oberoende av start- och sluttid. För att en pipeline ska köras bör den inte pausas. Du hittar de här inställningarna (start,, pausad) i pipelinedefinitionen: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Mer information om dessa egenskaper finns i [artikeln skapa pipelines.](data-factory-create-pipelines.md) 


## <a name="specify-schedule-for-an-activity"></a>Ange schema för en aktivitet
Det är inte pipelinen som körs. Det är de aktiviteter som är på gång som utförs i den övergripande kontexten för pipelinen. Du kan ange ett återkommande schema för en aktivitet med hjälp av **schemaavsnittet** för aktivitetsavsnittet JSON. Du kan till exempel schemalägga en aktivitet så att den körs varje timme enligt följande:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Som visas i följande diagram, om du anger ett schema för en aktivitet skapas en serie tumlande fönster med i pipeline-start- och sluttider. Tumlande fönster är en serie av icke-överlappande, sammanhängande tidsintervall. Dessa logiska tumlande fönster för en aktivitet kallas **aktivitetsfönster**.

![Exempel på aktivitetsschemaläggare](media/data-factory-scheduling-and-execution/scheduler-example.png)

Egenskapen **scheduler** för en aktivitet är valfri. Om du anger den här egenskapen måste den matcha den kadens som du anger i definitionen av utdatauppsättning för aktiviteten. Schemat styrs för närvarande av utdatamängd. Därför måste du skapa en utdatauppsättning även om aktiviteten inte ger någon utdata. 

## <a name="specify-schedule-for-a-dataset"></a>Ange schema för en datauppsättning
En aktivitet i en Data Factory-pipeline kan ta noll eller fler **indatauppsättningar** och producera en eller flera utdatauppsättningar. För en aktivitet kan du ange vilken kadens som indata är tillgängliga **availability** vid eller så produceras utdata med hjälp av tillgänglighetsavsnittet i datauppsättningsdefinitionerna. 

**Frekvens** i **tillgänglighetsavsnittet** anger tidsenheten. De tillåtna värdena för frekvens är: Minut, Timme, Dag, Vecka och Månad. **Intervallegenskapen** i tillgänglighetsavsnittet anger en multiplikator för frekvens. Till exempel: om frekvensen är inställd på Dag och intervallet är inställt på 1 för en utdatauppsättning, produceras utdata dagligen. Om du anger frekvensen som minut rekommenderar vi att du ställer in intervallet till inte mindre än 15. 

I följande exempel är indata tillgängliga varje timme och utdata`"frequency": "Hour", "interval": 1`produceras varje timme ( ). 

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


**Utdatauppsättning**

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

För närvarande **driver utdatauppsättningen schemat**. Med andra ord används det schema som angetts för utdatauppsättningen för att köra en aktivitet vid körning. Därför måste du skapa en utdatauppsättning även om aktiviteten inte ger någon utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. 

I följande pipelinedefinition används **egenskapen scheduler** för att ange schema för aktiviteten. Den här egenskapen är valfri. För närvarande måste schemat för aktiviteten matcha det schema som angetts för utdatauppsättningen.
 
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

I det här exemplet körs aktiviteten varje timme mellan start- och sluttiderna för pipelinen. Utdata produceras per timme för tre timmars fönster (08:00 till 09:00, 09:00 till 10:00 och 10:00 till 11:00). 

Varje enhet med data som förbrukas eller produceras av en aktivitetskörning kallas ett **datasegment**. I följande diagram visas ett exempel på en aktivitet med en indatauppsättning och en utdatauppsättning: 

![Schemaläggaren för tillgänglighet](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diagrammet visar timdatasegmenten för indata- och utdatauppsättningen. Diagrammet visar tre indatasegment som är klara för bearbetning. Aktiviteten 10-11 AM pågår och producerar utdatasegmentet 10–11. 

Du kan komma åt tidsintervallet som är associerat med det aktuella segmentet i datauppsättningen JSON med hjälp av variabler: [SegmentStart](data-factory-functions-variables.md#data-factory-system-variables) och [SegmentEnd](data-factory-functions-variables.md#data-factory-system-variables). På samma sätt kan du komma åt tidsintervallet som är associerat med ett aktivitetsfönster med hjälp av WindowStart och WindowEnd. Schemat för en aktivitet måste matcha schemat för utdatauppsättningen för aktiviteten. Därför är segmentstart- och segmentend-värdena desamma som WindowStart- respektive WindowEnd-värdena. Mer information om dessa variabler finns i artiklar om [datafabriksfunktioner och systemvariabler.](data-factory-functions-variables.md#data-factory-system-variables)  

Du kan använda dessa variabler för olika ändamål i din aktivitet JSON. Du kan till exempel använda dem för att välja data från indata- och utdatauppsättningar som representerar tidsseriedata (till exempel: 08:00 till 09:00). I det här exemplet används även **WindowStart** och **WindowEnd** för att välja relevanta data för en aktivitetskörning och kopiera dem till en blob med lämplig **folderPath**. **FolderPath** är parameteriserad för att ha en separat mapp för varje timme.  

I föregående exempel är schemat som angetts för indata- och utdatauppsättningar detsamma (varje timme). Om indatauppsättningen för aktiviteten är tillgänglig med en annan frekvens, säg var 15:e minut, körs aktiviteten som producerar den här utdatauppsättningen fortfarande en gång i timmen eftersom utdatauppsättningen är det som driver aktivitetsschemat. Mer information finns i [Modelldatauppsättningar med olika frekvenser](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Tillgänglighet och principer för datamängd
Du har sett användningen av frekvens- och intervallegenskaper i tillgänglighetsavsnittet i datauppsättningsdefinitionen. Det finns några andra egenskaper som påverkar schemaläggning och körning av en aktivitet. 

### <a name="dataset-availability"></a>Tillgänglighet för datauppsättning 
I följande tabell beskrivs egenskaper **availability** som du kan använda i tillgänglighetsavsnittet:

| Egenskap | Beskrivning | Krävs | Default |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för datauppsättningssegmentproduktion.<br/><br/><b>Frekvens som stöds</b>: Minut, Timme, Dag, Vecka, Månad |Ja |Ej tillämpligt |
| interval |Anger en multiplikator för frekvens<br/><br/>"Frekvens x intervall" avgör hur ofta segmentet produceras.<br/><br/>Om du behöver datauppsättningen som ska segmenteras per timme anger du <b>Frekvens</b> till <b>Timme</b>och <b>intervall</b> till <b>1</b>.<br/><br/><b>Obs:</b>Om du anger Frekvens som minut rekommenderar vi att du ställer in intervallet till inte mindre än 15 |Ja |Ej tillämpligt |
| stil |Anger om segmentet ska produceras i början/slutet av intervallet.<ul><li>StartofInterval</li><li>Slutavinföring</li></ul><br/><br/>Om Frekvens är inställt på Månad och format är inställt på EndOfInterval, produceras segmentet den sista dagen i månaden. Om formatet är inställt på StartOfInterval produceras segmentet den första dagen i månaden.<br/><br/>Om Frekvens är inställt på Dag och formatet är inställt på EndOfInterval, produceras segmentet under den sista timmen på dagen.<br/><br/>Om Frekvens är inställt på Timme och formatet är inställt på EndOfInterval, produceras segmentet i slutet av timmen. För ett segment för 13:00 till 14:00 period, är segmentet produceras vid 2 PM. |Inga |Slutavinföring |
| anchorDateTime |Definierar den absoluta positionen i tid som används av schemaläggaren för att beräkna datauppsättningssegmentgränser. <br/><br/><b>Obs:</b>Om AnchorDateTime har datumdelar som är mer detaljerade än frekvensen ignoreras de mer detaljerade delarna. <br/><br/>Om <b>intervallet</b> till exempel är <b>varje timme</b> (frekvens: timme och intervall: 1) och <b>AnchorDateTime</b> innehåller <b>minuter och sekunder</b>ignoreras de minuter <b>och sekunder</b> som delar av AnchorDateTime. |Inga |01/01/0001 |
| offset |Tidsintervall med vilket början och slutet av alla datauppsättningssegment flyttas. <br/><br/><b>Obs:</b>Om både anchorDateTime och offset anges, blir resultatet det kombinerade skiftet. |Inga |Ej tillämpligt |

### <a name="offset-example"></a>exempel på förskjutning
Som standard börjar`"frequency": "Day", "interval": 1`dagliga ( ) segment vid 12 AM UTC-tid (midnatt). Om du vill att starttiden ska vara 06:00 UTC-tid i stället ställer du in förskjutningen enligt följande utdrag: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime exempel
I följande exempel produceras datauppsättningen en gång var 23:e timme. Det första segmentet startar vid den tidpunkt som anges av `2017-04-19T08:00:00` anchorDateTime, som är inställt på (UTC-tid).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>förskjutnings-/formatexempel
Följande datauppsättning är en månatlig datauppsättning och produceras den 3:e varje månad klockan`3.08:00:00`8:00 ( ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Datauppsättningsprincip
En datauppsättning kan ha en verifieringsprincip definierad som anger hur data som genereras av en segmentkörning kan valideras innan den är klar för förbrukning. I sådana fall ändras utdatasegmentets status till **Väntar** med en understatus **för validering**. När segmenten har validerats ändras segmentstatusen till **Klar**. Om ett datasegment har producerats men inte passerade valideringen bearbetas inte aktiviteten för nedströmssegment som är beroende av det här segmentet. [Övervaka och hantera pipelines](data-factory-monitor-manage-pipelines.md) täcker de olika tillstånden för datasegment i Data Factory.

**Principavsnittet** i datauppsättningsdefinitionen definierar villkoren eller villkoret som datauppsättningssegmenten måste uppfylla. I följande tabell beskrivs egenskaper **policy** som du kan använda i principavsnittet:

| Principnamn | Beskrivning | Tillämpas på | Krävs | Default |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Verifierar att data i en **Azure-blob** uppfyller minimikraven för storlek (i megabyte). |Azure-blobb |Inga |Ej tillämpligt |
| minimumRows | Verifierar att data i en **Azure SQL-databas** eller en **Azure-tabell** innehåller det minsta antalet rader. |<ul><li>Azure SQL Database</li><li>Azure-tabell</li></ul> |Inga |Ej tillämpligt |

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

Mer information om dessa egenskaper och exempel finns i [artikeln Skapa datauppsättningar.](data-factory-create-datasets.md) 

## <a name="activity-policies"></a>Aktivitetsprinciper
Principer påverkar körningsbeteendet för en aktivitet, särskilt när segmentet i en tabell bearbetas. Följande tabell innehåller information.

| Egenskap | Tillåtna värden | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| samtidighet |Integer <br/><br/>Maxvärde: 10 |1 |Antal samtidiga körningar av aktiviteten.<br/><br/>Det avgör antalet parallella aktivitetskörningar som kan hända på olika segment. Om en aktivitet till exempel behöver gå igenom en stor uppsättning tillgängliga data, snabbar databearbetningen upp databearbetningen om du har ett större samtidighetsvärde. |
| körningPriorityOrder |NyasteFörsta<br/><br/>ÄldstaFörsta |ÄldstaFörsta |Bestämmer ordningen på datasegment som bearbetas.<br/><br/>Om du till exempel har 2 segment (en som händer klockan 16.00 och en annan klockan 17.00) och båda väntar på körning. Om du ställer in körningenPriorityOrder till nyast först bearbetas segmentet vid 17:00 först. På samma sätt om du anger att körningenPriorityORder ska vara OldestFIrst bearbetas segmentet vid 16:00. |
| retry |Integer<br/><br/>Maxvärde kan vara 10 |0 |Antal återförsök före databearbetningen för segmentet markeras som Fel. Aktivitetskörning för ett datasegment görs om upp till det angivna antalet försök. Återförsöket görs så snart som möjligt efter felet. |
| timeout |TimeSpan |00:00:00 |Timeout för aktiviteten. Exempel: 00:10:00 (innebär timeout 10 minuter)<br/><br/>Om ett värde inte anges eller är 0 är timeout oändlig.<br/><br/>Om databearbetningstiden för ett segment överskrider timeout-värdet avbryts det och systemet försöker försöka bearbeta igen. Antalet återförsök beror på egenskapen för återförsök. När timeout inträffar anges statusen till TimedOut. |
| Försening |TimeSpan |00:00:00 |Ange fördröjningen innan databearbetningen av segmentet startar.<br/><br/>Körningen av aktivitet för ett datasegment startas när fördröjningen har passerat den förväntade körningstiden.<br/><br/>Exempel: 00:10:00 (innebär fördröjning på 10 minuter) |
| longRetry |Integer<br/><br/>Maxvärde: 10 |1 |Antalet långa försök att försöka igen innan segmentkörningen misslyckas.<br/><br/>longRetry-försök är fördelade efter longRetryInterval. Så om du behöver ange en tid mellan försök att försöka igen använder du longRetry. If both Retry and longRetry are specified, each longRetry attempt includes Retry attempts and the max number of attempts is Retry * longRetry.<br/><br/>Om vi till exempel har följande inställningar i aktivitetspolicyn:<br/>Försök igen: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Anta att det bara finns ett segment att köra (status väntar) och aktivitetskörningen misslyckas varje gång. Inledningsvis skulle det finnas 3 på varandra följande avrättningsförsök. Efter varje försök skulle segmentstatusen vara Försök igen. Efter första 3 försök är över, skulle segmentet status LongRetry.<br/><br/>Efter en timme (det vill vara longRetryIntevals värde) skulle det finnas ytterligare en uppsättning med 3 på varandra följande körningsförsök. Därefter skulle segmentstatusen misslyckas och inga fler försök skulle göras. Därför gjordes totalt 6 försök.<br/><br/>Om någon körning lyckas, skulle segmentstatus vara Klar och inga fler försök görs.<br/><br/>longRetry kan användas i situationer där beroende data anländer till icke-deterministiska tider eller den övergripande miljön är flagnande under vilken databehandling sker. I sådana fall kan det hända att du inte hjälper till att göra försök igen efter varandra och att göra det efter ett tidsintervall resulterar i önskad utgång.<br/><br/>Varningens ord: ange inte höga värden för longRetry eller longRetryInterval. Vanligtvis innebär högre värden andra systemproblem. |
| longRetryInterval |TimeSpan |00:00:00 |Fördröjningen mellan långa försök att försöka igen |

Mer information finns i artikel [Pipelines.](data-factory-create-pipelines.md) 

## <a name="parallel-processing-of-data-slices"></a>Parallell bearbetning av datasegment
Du kan ange startdatum för pipelinen tidigare. När du gör det beräknar Data Factory automatiskt (tillbaka fyllningar) alla datasegment tidigare och börjar bearbeta dem. Till exempel: om du skapar en pipeline med startdatum 2017-04-01 och det aktuella datumet är 2017-04-10. Om kadensen för utdatauppsättningen är daglig börjar Data Factory bearbeta alla segment från 2017-04-01 till 2017-04-09 omedelbart eftersom startdatumet är tidigare. Segmentet från 2017-04-10 bearbetas inte ännu eftersom värdet för formategenskapen i tillgänglighetsavsnittet är EndOfInterval som standard. Det äldsta segmentet bearbetas först eftersom standardvärdet för körningPriorityOrder är OldestFirst. En beskrivning av formategenskapen finns i [avsnittet tillgänglighet för datauppsättning.](#dataset-availability) En beskrivning av avsnittet exekveringpriorityOrder finns i avsnittet [aktivitetsprinciper.](#activity-policies) 

Du kan konfigurera bakåtfyllda datasegment som ska bearbetas parallellt genom att ange egenskapen **samtidighet** i **principavsnittet** i aktiviteten JSON. Den här egenskapen bestämmer antalet parallella aktivitetskörningar som kan hända på olika segment. Standardvärdet för egenskapen samtidighet är 1. Därför bearbetas ett segment i taget som standard. Det maximala värdet är 10. När en pipeline behöver gå igenom en stor uppsättning tillgängliga data, med ett större samtidighetsvärde, snabbar databearbetningen upp. 

## <a name="rerun-a-failed-data-slice"></a>Köra ett misslyckat datasegment igen
När ett fel inträffar när du bearbetar ett datasegment kan du ta reda på varför bearbetningen av ett segment misslyckades med hjälp av Azure-portalblad eller Övervaka och hantera app. Mer information finns [i Övervakning och hantering av pipelines med hjälp av Azure-portalblad](data-factory-monitor-manage-pipelines.md) eller appen Övervakning och [hantering.](data-factory-monitor-manage-app.md)

Tänk på följande exempel, som visar två aktiviteter. Aktivitet1 och Aktivitet 2. Activity1 förbrukar en del datauppsättning1 och producerar en del datauppsättning2, som förbrukas som en indata av Activity2 för att skapa en del av den slutliga datauppsättningen.

![Det gick inte att dela upp](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagrammet visar att av tre senaste segment, det var ett fel producerar 9-10 AM segmentet för Dataset2. Data Factory spårar automatiskt beroende för tidsseriedatauppsättningen. Därför startar den inte aktivitetskörningen för segmentet 9-10 NEDströms.

Med övervaknings- och hanteringsverktygen för datafabrik kan du öka detaljnivån för den felaktiga segmentet för att enkelt hitta orsaken till problemet och åtgärda det. När du har åtgärdat problemet kan du enkelt starta aktivitetskörningen för att skapa det misslyckade segmentet. Mer information om hur du kör om och förstår tillståndsövergångar för datasegment finns i [Övervaka och hantera pipelines med hjälp av Azure-portalblad](data-factory-monitor-manage-pipelines.md) eller [övervaknings- och hanteringsapp](data-factory-monitor-manage-app.md).

När du kör segmentet 9-10 am för **Dataset2**startar Data Factory körningen för segmentet 9-10 AM beroende på den slutliga datauppsättningen.

![Det gick inte att köra igen segmentet](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Flera aktiviteter i en pipeline
Du kan fler än en aktivitet i en pipeline. Om du har flera aktiviteter i en pipeline och utdata från en aktivitet inte är en indata för en annan aktivitet, kan aktiviteterna köras parallellt om indatasegmenten för aktiviteterna är klara.

Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Aktiviteterna kan vara i samma pipeline eller i olika pipelines. Den andra aktiviteten körs bara när den första avslutas.

Tänk till exempel på följande fall där en pipeline har två aktiviteter:

1. Aktivitet A1 som kräver extern indatauppsättning D1 och producerar utdatauppsättning D2.
2. Aktivitet A2 som kräver indata från datauppsättningen D2 och producerar utdatadatauppsättning D3.

I det här fallet aktiviteter A1 och A2 är i samma pipeline. Aktiviteten A1 körs när externa data är tillgängliga och den schemalagda tillgänglighetsfrekvensen har uppnåtts. Aktiviteten A2 körs när de schemalagda segmenten från D2 blir tillgängliga och den schemalagda tillgänglighetsfrekvensen har uppnåtts. Om det finns ett fel i något av segmenten i datauppsättningen D2 körs A2 inte för det segmentet förrän det blir tillgängligt.

Diagramvyn med båda aktiviteterna i samma pipeline skulle se ut så här:

![Kedja verksamhet i samma rörledning](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Som tidigare nämnts kan verksamheten vara på olika gångpipelines. I ett sådant scenario skulle diagramvyn se ut så här:

![Kedja verksamhet i två rörledningar](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Se avsnittet kopia sekventiellt i bilagan för ett exempel.

## <a name="model-datasets-with-different-frequencies"></a>Modelldatauppsättningar med olika frekvenser
I exemplen var frekvenserna för indata- och utdatauppsättningar och aktivitetsschemafönstret desamma. Vissa scenarier kräver möjligheten att producera utdata med en annan frekvens än frekvenserna för en eller flera indata. Data Factory stöder modellering av dessa scenarier.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exempel 1: Ta fram en daglig utdatarapport för indata som är tillgängliga varje timme
Tänk dig ett scenario där du har indatamätningsdata från sensorer tillgängliga varje timme i Azure Blob-lagring. Du vill ta fram en daglig aggregerad rapport med statistik som medelvärde, högsta och lägsta för dagen med [Data Factory hive-aktivitet](data-factory-hive-activity.md).

Så här kan du modellera det här scenariot med Data Factory:

**Indatauppsättning**

De indatafiler per timme tas bort i mappen för den angivna dagen. Tillgänglighet för indata ställs in på **Timme** (frekvens: Timme, intervall: 1).

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
**Utdatauppsättning**

En utdatafil skapas varje dag i dagens mapp. Tillgängligheten för utdata ställs in på **Dag** (frekvens: Dag och intervall: 1).

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

Registreringsdatafilen tar emot lämplig *DateTime-information* som parametrar som använder **windowstart-variabeln** enligt följande utdrag. Registreringsdatafilen använder den här variabeln för att läsa in data från rätt mapp för dagen och köra aggregeringen för att generera utdata.

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

Följande diagram visar scenariot från en databeroende synvinkel.

![Databeroende](./media/data-factory-scheduling-and-execution/data-dependency.png)

Utdatasegmentet för varje dag beror på 24 segment per timme från en indatauppsättning. Data Factory beräknar dessa beroenden automatiskt genom att räkna ut de indatasegment som faller under samma tidsperiod som utdatasegmentet som ska produceras. Om något av de 24 indatasegmenten inte är tillgängligt väntar Data Factory på att indatasegmentet ska vara klart innan den dagliga aktivitetskörningen startas.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exempel 2: Ange beroende med uttryck och datafabriksfunktioner
Låt oss överväga ett annat scenario. Anta att du har en registreringsaktivitet som bearbetar två indatauppsättningar. En av dem har nya data dagligen, men en av dem får nya data varje vecka. Anta att du vill göra en koppling över de två ingångarna och producera en utdata varje dag.

Den enkla metoden där Data Factory automatiskt räknar ut rätt indatasegment som ska bearbetas genom att justera till utdatasegmentets tidsperiod fungerar inte.

Du måste ange att datafabriken för varje aktivitet som körs ska använda förra veckans datasegment för den veckovisa indatauppsättningen. Du använder Azure Data Factory-funktioner som visas i följande kodavsnitt för att implementera det här beteendet.

**Indata1: Azure-blob**

Den första indata är Azure blob som uppdateras dagligen.

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

Input2 är Azure-bloben som uppdateras varje vecka.

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

En utdatafil skapas varje dag i mappen för dagen. Tillgängligheten för utdata är inställd på **dag** (frekvens: Dag, intervall: 1).

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

Hive-aktiviteten tar de två ingångarna och producerar ett utdatasegment varje dag. Du kan ange att varje dags utdatasegment ska bero på föregående veckas indatasegment för veckovis inmatning enligt följande.

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

Se [Data Factorys funktioner och systemvariabler](data-factory-functions-variables.md) för en lista över funktioner och systemvariabler som Data Factory stöder.

## <a name="appendix"></a>Bilaga

### <a name="example-copy-sequentially"></a>Exempel: kopiera sekventiellt
Det är möjligt att köra flera kopieringsåtgärder en efter en i sekventiell/ordnad. Du kan till exempel ha två kopieringsaktiviteter i en pipeline (CopyActivity1 och CopyActivity2) med följande datauppsättningar för indatautdata:   

CopyActivity1

Indata: Datauppsättning. Utdata: Dataset2.

Kopieringsaktivitet2

Indata: Datauppsättning2.  Utdata: Dataset3.

CopyActivity2 skulle bara köras om CopyActivity1 har körts och Dataset2 är tillgängligt.

Här är exempelpipelinen JSON:

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

Observera att i exemplet anges utdatauppsättningen för den första kopieringsaktiviteten (Dataset2) som indata för den andra aktiviteten. Därför körs den andra aktiviteten endast när utdatauppsättningen från den första aktiviteten är klar.  

I exemplet kan CopyActivity2 ha en annan indata, till exempel Dataset3, men du anger Dataset2 som indata till CopyActivity2, så aktiviteten körs inte förrän CopyActivity1 är klar. Ett exempel:

CopyActivity1

Indata: Datauppsättning1. Utdata: Dataset2.

Kopieringsaktivitet2

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

Observera att i exemplet anges två indatauppsättningar för den andra kopieringsaktiviteten. När flera indata anges används endast den första indatauppsättningen för att kopiera data, men andra datauppsättningar används som beroenden. CopyActivity2 startar först när följande villkor är uppfyllda:

* CopyActivity1 har slutförts och Dataset2 är tillgängligt. Den här datauppsättningen används inte vid kopiering av data till Dataset4. Det fungerar bara som ett schemaläggningsberoende för CopyActivity2.   
* Dataset3 är tillgängligt. Den här datauppsättningen representerar de data som kopieras till målet. 
