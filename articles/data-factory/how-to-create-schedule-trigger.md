---
title: "Hur du skapar schemat utlösare i Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline enligt ett schema."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Hur du skapar en utlösare som kör en pipeline enligt ett schema
Den här artikeln innehåller information om schemat utlösare och åtgärder för att skapa, starta och övervaka en utlösare. För andra typer av utlösare finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Läs [get started with Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (kom igång med Data Factory version 1) om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig.

### <a name="schedule-trigger-json-definition"></a>Schemat utlösaren JSON-definition
Du kan ange schemaläggning och upprepning med JSON som visas i exemplet i det här avsnittet när du skapar en utlösare för schemat.

Om du vill att schemat utlösaren startar en pipeline som kör ett hs pipeline för viss pipeline i definitionen för utlösare. Pipeliner och utlösare har en många-till-många-relation. Flera utlösare kan starta en pipeline. En enskild utlösare kan starta flera pipeliner.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  Egenskapen **parametrar** är en obligatorisk egenskap i **pipeliner**. Inkludera en tom json för parametrar eftersom egenskapen måste finnas, även om din pipeline inte tar några parametrar.


### <a name="overview-schedule-trigger-schema"></a>Översikt: Schema utlösaren schema
Följande tabell innehåller en översikt över huvudelementen relaterade till upprepning och schemaläggning i en utlösare:

JSON-egenskap |     Beskrivning
------------- | -------------
startTime | startTime är datum och tid. StartTime är den första förekomsten för enkla scheman. För komplexa scheman startar utlösaren tidigast startTime.
endTime | Anger slutvärdet för datum/tid för utlösaren. Utlösaren körs inte efter den tidpunkten. Det går inte att ha en endTime (sluttid) i dåtid. Det här är en valfri egenskap.
Tidszon | För närvarande stöds endast UTC.
recurrence | Objektet recurrence anger upprepningsregler för utlösaren. Upprepningsobjektet stöder följande element: frequency (frekvens), interval (intervall), endTime (sluttid), count (antal) och schedule (schema). Om recurrence (upprepning) definieras krävs frequency (frekvens). De andra recurrence-elementen är valfria.
frequency | Representerar frekvensen med vilken utlösaren upprepas. Värden som stöds är: `minute`, `hour`, `day`, `week` eller `month`.
interval | Elementet interval är ett positivt heltal. Den anger intervallet för den frekvens som avgör hur ofta utlösaren körs. Om intervallet till exempel är 3 och frekvensen är week (vecka) upprepas utlösaren var tredje vecka.
schedule | En utlösare med en angiven frekvens ändrar sin upprepning baserat på ett recurrence schedule (upprepningsschema). Ett schema innehåller ändringar som baseras på minuter, timmar, veckodagar, dagar i månaden och veckonummer.


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>Översikt: Schema utlösaren schemat standarder, begränsningar och exempel

JSON-namn | Värdetyp | Krävs? | Standardvärde | Giltiga värden | Exempel
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Sträng | Ja | Ingen | ISO 8601-datum/tid | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Objekt | Ja | Ingen | Upprepningsobjekt | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Tal | Nej | 1 | 1 till 1000. | ```"interval":10```
endTime | Sträng | Ja | Ingen | Datum/tid-värde som representerar en tidpunkt i framtiden | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Objekt | Nej | Ingen | Schemaobjekt | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>Djupdykning: startTime (starttid)
Följande tabell visar hur startTime styr hur en utlösare körs:

startTime-värde | Upprepning utan schema | Upprepning med schema
--------------- | --------------------------- | ------------------------
Starttid i förfluten tid | Beräknar första framtida körningstid efter starttid och kör den tiden.<p>Utför efterföljande körningar baserat på beräkning från senaste körningstid.</p><p>Se exemplet som följer den här tabellen.</p> | Utlösaren startar _tidigast_ på den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden. <p>Kör efterföljande körningar baserat på upprepningsschemat</p>
Starttid i framtiden eller aktuell tid | Körs en gång på angiven starttid. <p>Utför efterföljande körningar baserat på beräkning från senaste körningstid.</p> | Utlösaren startar _tidigast_ på den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<p>Kör efterföljande körningar baserat på upprepningsschemat.</p>

Nu tittar vi på ett exempel på när startTime är i förfluten tid, med upprepning men inget schema. Anta att den aktuella tiden är `2017-04-08 13:00`, startTime är `2017-04-07 14:00` och upprepningen är varannan dag (definieras med frequency (frekvens): day (dag) och interval (intervall): 2) Observera att startTime är i förfluten tid och inträffar före aktuell tid.

Med dessa villkor sker den första körningen `2017-04-09 at 14:00`. Scheduler-motor beräknar körningsförekomster från starttiden. Alla tidigare instanser ignoreras. Motorn använder nästa förekomst som förekommer i framtiden. I det här fallet är startTime `2017-04-07 at 2:00pm`, så nästa instans är två dagar från den tiden, vilket är `2017-04-09 at 2:00pm`.

Den första körningstiden är samma även om startTime `2017-04-05 14:00` eller `2017-04-01 14:00`. Efter den första körningen beräknas efterföljande körningar med hjälp av schemat. De är därför `2017-04-11 at 2:00pm`, sedan `2017-04-13 at 2:00pm`, sedan `2017-04-15 at 2:00pm` osv.

Till sist: när en utlösare har ett schema, om timmar och/eller minuter inte anges i schemat, används standardvärdet för timmar och/eller minuter för den första körningen.

### <a name="deep-dive-schedule"></a>Djupdykning: schema
Å ena sidan kan ett schema begränsa antalet utlösarkörningar. Om en utlösare med frekvensen month (månad) har ett schema som bara körs på dag 31 körs utlösaren bara på de månader som har en 31:a dag.

Ett schema kan å andra sidan även utöka antalet utlösarkörningar. Om en utlösare med frekvensen month (månad) har ett schema som körs på dag 1 och 2 i månaden körs utlösaren på den första och andra dagen i månaden istället för en gång i månaden.

Om flera schemaelement anges är utvärderingsordningen från största till minsta – veckonummer, dag i månaden, veckodag, timme och minut.

I följande tabell beskrivs schemaelement i detalj:


JSON-namn | Beskrivning | Giltiga värden
--------- | ----------- | ------------
minutes | Minuter för den timme då utlösaren körs. | <ul><li>Integer</li><li>Heltalsmatris</li></ul>
hours | Timmar på dagen då utlösaren körs. | <ul><li>Integer</li><li>Heltalsmatris</li></ul>
weekDays | Veckodagar då utlösaren körs. Kan bara anges med veckofrekvens. | <ul><li>Måndag, tisdag, onsdag, torsdag, fredag, lördag och söndag</li><li>Matris med något av värdena (max. matrisstorlek 7)</li></p>Inte skiftlägeskänslig</p>
monthlyOccurrences | Anger vilka dagar i månaden som utlösaren körs. Kan bara anges med månadsfrekvens. | Matris med monthlyOccurence-objekt: `{ "day": day,  "occurrence": occurence }`. <p> Dagen är den veckodag då utlösaren körs, till exempel är `{Sunday}` varje söndag i månaden. Krävs.<p>Förekomst är förekomsten av dag under månaden, till exempel är `{Sunday, -1}` den sista söndagen i månaden. Valfri.
monthDays | Dagen i månaden då utlösaren körs. Kan bara anges med månadsfrekvens. | <ul><li>Ett värde < = -1 och > =-31</li><li>Ett värde > = 1 och < = 31</li><li>En matris med värden</li>


## <a name="examples-recurrence-schedules"></a>Exempel: upprepningsscheman
I det här avsnittet ges exempel på upprepningsscheman – med fokus på schemaobjektet och dess underelement.

I dessa exempelscheman antas att 1 anges som intervallvärde. Anta dessutom rätt frekvens enligt vad som finns i schemat – till exempel kan du inte använda frekvensen day (dag) och ha en monthDays-modifiering (dagar i månaden) i schemat. Dessa begränsningar nämns i tabellen i föregående avsnitt.

Exempel | Beskrivning
------- | -----------
`{"hours":[5]}` | Körs 05:00 varje dag
`{"minutes":[15], "hours":[5]}` | Körs 05:15 varje dag
`{"minutes":[15], "hours":[5,17]}` | Körs 05:15 och 17:15 varje dag
`{"minutes":[15,45], "hours":[5,17]}` | Körs 05:15, 05:45, 17:15 och 17:45 varje dag
`{"minutes":[0,15,30,45]}` | Körs var 15:e minut
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Körs varje timme. Den här utlösaren körs varje timme. Minuten störs av startTime, om den har angetts, eller skapandetiden, om den inte har angetts. Exempel: om starttiden eller skapandetiden (beroende på vilken som gäller) är 12:25 körs utlösaren 00:25, 01:25, 02:25, …, 23:25. Schemat motsvarar att ha en utlösare med frekvensen hour (timme), intervallet 1 och inget schema. Skillnaden är att det här schemat kan användas med olika frekvens och intervall för att också skapa andra utlösare. Om frekvensen är month (månad) skulle schemat bara köras en gång i månaden istället för varje dag, om frekvensen är day (dag).
`{"minutes":[0]}` | Körs varje timme. Den här utlösaren körs även varje timme, men prick på timmen (t.ex. 12:00, 13:00, 14:00 osv.). Den här inställningen motsvarar en utlösare med frekvensen hour (timme) startTime med noll minuter och inget scheman om frekvensen är day (dag) men om frekvensen är week (vecka) eller month (månad) körs schemat bara en gång i veckan respektive en gång i månaden.
`{"minutes":[15]}` | Körs 15 minuter över varje hel timme. Körs varje timme, med start 00:15, 01:15, 02:15 osv och slutar 22:15 och 23:15.
`{"hours":[17], "weekDays":["saturday"]}` | Körs 17:00 på lördagar varje vecka
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17: 00 på måndag, onsdag och fredag varje vecka
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17:15 och 17:45 på måndag, onsdag och fredag varje vecka
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15: e minut på vardagar
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15: e minut på vardagar mellan 09:00 och 16:45
`{"weekDays":["tuesday", "thursday"]}` | Körs varje tisdag och torsdag på den angivna starttiden.
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | Körs 06: 00 på den 28:e dagen varje månad (förutsatt frekvensen månad)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Körs 06:00 på den sista dagen i månaden. Om du vill köra en utlösare på sista dagen i månaden använder du -1 istället för dag 28, 29, 30 eller 31.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Körs 06:00 på första och sista dagen varje månad
`{monthDays":[1,14]}` | Körs på den första och fjortonde dagen i varje månad på den angivna starttiden.
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs på den första fredagen i varje månad 05:00
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs på den första fredagen i varje månad på den angivna starttiden.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Körs på den tredje fredagen från slutet i månaden, varje månad, på starttiden
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs på första och sista fredagen i varje månad 05:15
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs på första och sista fredagen i varje månad på den angivna starttiden
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Körs på den femte fredagen i varje månad på starttiden. Om det inte finns någon femte fredag i en månad körs inte pipelinen, eftersom den är schemalagd att bara köras på den femte fredagen.  Om du vill köra utlösaren på den sista fredagen i månaden kan du använda -1 istället för 5 som förekomst.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Körs var 15:e minut på sista fredagen i månaden.
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Körs 05:15, 05:45, 17:15 och 17:45 på den tredje onsdagen i varje månad.


## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Det här avsnittet visar hur du använder Azure PowerShell för att skapa, starta och övervaka en scheduler-utlösare. Om du vill se det här exemplet fungerar först gå igenom den [Snabbstart: skapa en datafabrik med hjälp av Azure PowerShell](quickstart-create-data-factory-powershell.md). Sedan lägger du till följande kod main-metoden som skapar och startar en schema-utlösare som körs var 15: e minut. Utlösaren är associerad med en rörledning (**Adfv2QuickStartPipeline**) som du skapar som en del i Snabbstart.

1. Skapa en JSON-fil med namnet MyTrigger.json i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

    > [!IMPORTANT]
    > Ange **startTime** till den aktuella UTC-tid och **endTime** tid innan du sparar JSON-filen till en timme efter den aktuella UTC.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    I JSON-utdrag:
    - Den **typen** för utlösaren är inställd på **ScheduleTrigger**.
    - Den **frekvens** är inställd på **minut** och **intervall** är inställd på **15**. Utlösaren körs därför pipeline var 15: e minut mellan start- och sluttider.
    - Den **endTime** är en timme efter den **startTime**, så utlösaren körs pipeline 15 minuter, 30 minuter och 45 minuter efter startTime. Glöm inte att uppdatera startTime till den aktuella UTC-tid och endTime till en timme efter startTime.  
    - Utlösaren är associerad med den **Adfv2QuickStartPipeline** pipeline. Om du vill associera flera pipelines med en utlösare, lägga till fler **pipelineReference** avsnitt.
    - Pipeline i Snabbstart tar två **parametrar**. Därför kan överföra du värden för dessa parametrar från utlösaren.
2. Skapa en utlösare med hjälp av den **Set AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. Bekräfta att status för utlösaren är **stoppad** med hjälp av den **Get-AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. Starta utlösaren med hjälp av den **Start AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. Bekräfta att status för utlösaren är **igång** med hjälp av den **Get-AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  Hämta utlösaren körs med PowerShell med hjälp av den **Get-AzureRmDataFactoryV2TriggerRun** cmdlet. För att få information om utlösaren körs kan du köra följande kommando med jämna mellanrum: uppdatera **TriggerRunStartedAfter** och **TriggerRunStartedBefore** värden som stämmer med värdena i utlösardefinition .

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    Om du vill övervaka utlösaren körs/pipeline körs i Azure portal finns [övervakaren pipeline körs](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-net-sdk"></a>Använd .NET SDK
Det här avsnittet visar hur du använder .NET SDK för att skapa, starta och övervaka en utlösare. Om du vill se den här koden fungerar först gå igenom den [Snabbstart för att skapa en datafabrik med .NET SDK](quickstart-create-data-factory-dot-net.md). Sedan lägger du till följande kod main-metoden som skapar och startar en schema-utlösare som körs var 15: e minut. Utlösaren är associerad med en rörledning (**Adfv2QuickStartPipeline**) som du skapar som en del i Snabbstart.

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method.
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

Lägg till följande kod innan senaste för att övervaka en utlösare som kör `Console.WriteLine` instruktionen:

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Om du vill övervaka utlösaren körs/pipeline körs i Azure portal finns [övervakaren pipeline körs](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>Använda Python SDK
Det här avsnittet visar hur du använder Python SDK för att skapa, starta och övervaka en utlösare. Om du vill se den här koden fungerar först gå igenom den [Snabbstart för att skapa en datafabrik med Python SDK](quickstart-create-data-factory-python.md). Lägg sedan till följande kodblock efter kodblocket ”övervaka pipeline kör” i python-skriptet. Den här koden skapar en schema-utlösare som körs varje kvart mellan angivna start- och sluttider. Uppdatera starttid till den aktuella UTC-tid och end_time till en timme efter den aktuella UTC-tid.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Om du vill övervaka utlösaren körs/pipeline körs i Azure portal finns [övervakaren pipeline körs](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>Använd Resource Manager-mall
Du kan använda en Azure Resource Manager-mall för att skapa en utlösare. Stegvisa instruktioner finns [skapa ett Azure data factory med Resource Manager-mall](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Skicka utlösaren starttiden för en pipeline
Azure Data Factory stöds läsning eller skrivning partitionerade data med hjälp av SliceStart/SliceEnd/WindowStart/WindowEnd systemvariabler i version 1. Du kan åstadkomma detta genom att använda en pipeline-parameter och utlösarens schemalagd tid/starttid som ett värde för parametern i version 2. I följande exempel skickas utlösarens schemalagd tid som ett värde till parametern-scheduledRunTime pipeline.

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
Mer information finns i [så att läsa eller skriva partitionerad data](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
