---
title: "Skapa schemat utlösare i Azure Data Factory | Microsoft Docs"
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
ms.date: 01/23/2018
ms.author: shlo
ms.openlocfilehash: 51e2dddbe66ca372d89fc8efeb24bdab9fe6a442
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Skapa en utlösare som kör en pipeline enligt ett schema
Den här artikeln innehåller information om utlösaren schema och stegen för att skapa, starta och övervaka en schema-utlösare. För andra typer av utlösare finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md).

När du skapar en utlösare för schemat måste du ange ett schema (startdatum, återkommande, slutdatum o.s.v.) för utlösare och associeras med en rörledning. Pipeliner och utlösare har en många-till-många-relation. Flera utlösare kan starta en pipeline. En enskild utlösare kan starta flera pipeliner.

> [!NOTE]
> Den här artikeln gäller för Azure Data Factory version 2, vilket är för närvarande under förhandsgranskning. Om du använder Azure Data Factory version 1, som är allmänt tillgänglig (GA), se [Kom igång med Azure Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Följande avsnitt innehåller steg för att skapa en schema-utlösare på olika sätt. 

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt
Du kan skapa en **schema utlösaren** att schemalägga en rörledning för att köra regelbundet (varje timme, dagligen, etc.). 

> [!NOTE]
> En fullständig genomgång av hur du skapar en pipeline och en schema-utlösare associera utlösaren pipeline, och köra och övervaka pipelinen, se [Snabbstart: skapa en datafabrik som använder Data Factory UI](quickstart-create-data-factory-portal.md).

1. Växla till fliken **Redigera**. 

    ![Växla till fliken Redigera](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Klicka på **Utlösare** på menyn och klicka på **New/Edit** (Nytt/Redigera). 

    ![Menyn Ny utlösare](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. På sidan **Add Triggers** (Lägg till utlösare) klickar du på **Choose trigger...** (Välj utlösare...) och klickar på **New** (Nytt). 

    ![Lägg till utlösare – ny utlösare](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. I den **ny utlösare** gör du följande: 

    1. Bekräfta att **schema** har valts för **typen**. 
    2. Ange start-datetime av utlösare för **Start (UTC)**. Den är inställd på den aktuella datetime som standard. 
    3. Ange **återkommande** för utlösaren. Välj ett av värdena från den nedrullningsbara listan (varje minut, varje timme, varje dag, varje vecka, och varje månad). Ange multiplikatorn i textrutan. Till exempel om du vill att utlösaren ska köras en gång för varje 15 minuter kan du välja **varannan minut**, och ange **15** i textrutan. 
    4. För den **End** om du inte vill ange en end datetime för utlösaren, Välj **No End**. Välj för att ange ett slut tidsvärdet **på datum**, och end datetime och klickar på **tillämpa**. Det finns ingen associerad kostnad till varje pipelinekörning. Om du testar du kanske vill se till att pipeline utlöses endast några gånger. Men se till att det finns tillräckligt med tid att köra pipelinen mellan publiceringstiden och sluttiden. Utlösaren träder endast i kraft när du har publicerat lösningen till Data Factory, och inte när du sparar utlösaren i användargränssnittet.

        ![Inställningar för utlösare](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. I den **ny utlösare** och kontrollera den **aktiverad** alternativ och klickar på **nästa**. Du kan använda den här kryssrutan för att inaktivera utlösaren senare. 

    ![Inställningar för utlösare – knappen Nästa](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. På sidan **Ny utlösare** läser du varningsmeddelandet och klickar på **Slutför**.

    ![Inställningar för utlösare – knappen Slutför](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Klicka på **Publicera** för att publicera Data Factory-ändringar. Tills du publicera ändringar i Data Factory startar inte utlösaren utlösa pipeline-körs. 

    ![Knappen Publicera](./media/how-to-create-schedule-trigger/publish-2.png)
8. Växla till fliken **Övervaka** till vänster. Om du vill uppdatera listan klickar du på **Uppdatera**. Du kan se pipelinen körs utlösta av schemalagda utlösaren. Observera värdena i kolumnen **Aktiverad av**. Om du använder **utlösaren nu** kan du se manuell utlösaren köra i listan. 

    ![Övervaka utlösta körningar](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Klicka på nedåtpilen bredvid **Pipeline Runs** (Pipelinekörningar) för att växla till vyn **Trigger Runs** (Utlösarkörningar). 

    ![Övervaka utlösarkörningar](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell
Det här avsnittet visar hur du använder Azure PowerShell för att skapa, starta och övervaka en schema-utlösare. Om du vill se det här exemplet fungerar först gå igenom den [Snabbstart: skapa en datafabrik med hjälp av Azure PowerShell](quickstart-create-data-factory-powershell.md). Sedan lägger du till följande kod main-metoden som skapar och startar en schema-utlösare som körs var 15: e minut. Utlösaren är associerad med en rörledning med namnet **Adfv2QuickStartPipeline** som du skapar som en del i Snabbstart.

1. Skapa en JSON-fil med namnet **MyTrigger.json** i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

    > [!IMPORTANT]
    > Innan du sparar JSON-fil, ange värdet för den **startTime** elementet så att den aktuella UTC-tid. Ange värdet för den **endTime** element till en timme efter den aktuella UTC-tid.

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
    - Den **typen** element för utlösaren är inställd på ”ScheduleTrigger”.
    - Den **frekvens** element är inställd på ”minuter” och **intervall** element är inställt på 15. Utlösaren körs därför pipeline var 15: e minut mellan start- och sluttider.
    - Den **endTime** elementet är en timme efter värdet för den **startTime** element. Utlösaren körs därför pipeline 15 minuter, 30 minuter och 45 minuter efter starttiden. Glöm inte att uppdatera starttiden till aktuellt UTC-tid och sluttid för en timme efter starttiden. 
    - Utlösaren är associerad med den **Adfv2QuickStartPipeline** pipeline. Om du vill associera flera pipelines med en utlösare, lägga till fler **pipelineReference** avsnitt.
    - Pipeline i Snabbstart tar två **parametrar** värden: **inputPath** och **outputPath**. Därför kan skicka du värdena för dessa parametrar från utlösaren.

2. Skapa en utlösare med hjälp av den **Set AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Bekräfta att status för utlösaren är **stoppad** med hjälp av den **Get-AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Starta utlösaren med hjälp av den **Start AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Bekräfta att status för utlösaren är **igång** med hjälp av den **Get-AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6.  Get-utlösaren körs i Azure PowerShell med hjälp av den **Get-AzureRmDataFactoryV2TriggerRun** cmdlet. Kör följande kommando med jämna mellanrum för att få information om utlösaren körs. Uppdatering av **TriggerRunStartedAfter** och **TriggerRunStartedBefore** värden som stämmer med värdena i utlösardefinition:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Övervaka utlösaren kör och pipeline i Azure-portalen, se [övervakaren pipeline körs](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>.NET SDK
Det här avsnittet visar hur du använder .NET SDK för att skapa, starta och övervaka en utlösare. Om du vill se det här exemplet fungerar först gå igenom den [Snabbstart: skapa en datafabrik med hjälp av .NET SDK](quickstart-create-data-factory-dot-net.md). Sedan lägger du till följande kod main-metoden som skapar och startar en schema-utlösare som körs var 15: e minut. Utlösaren är associerad med en rörledning med namnet **Adfv2QuickStartPipeline** som du skapar som en del i Snabbstart.

Om du vill skapa och starta en schema-utlösare som körs var 15: e minut, lägger du till följande kod main-metoden:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Lägg till följande kod innan senaste för att övervaka en utlösare som kör `Console.WriteLine` instruktionen i exemplet:

```csharp
            // Check that the trigger runs every 15 minutes
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

Övervaka utlösaren kör och pipeline i Azure-portalen, se [övervakaren pipeline körs](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Python SDK
Det här avsnittet visar hur du använder Python SDK för att skapa, starta och övervaka en utlösare. Om du vill se det här exemplet fungerar först gå igenom den [Snabbstart: skapa en datafabrik med hjälp av Python SDK](quickstart-create-data-factory-python.md). Lägg sedan till följande kodblock efter kodblocket ”övervaka pipeline kör” i Python-skriptet. Den här koden skapar en schema-utlösare som körs varje kvart mellan angivna start- och sluttider. Uppdatering av **starttid** variabeln till den aktuella UTC-tid och **end_time** variabel till en timme efter den aktuella UTC-tid.

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

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Övervaka utlösaren kör och pipeline i Azure-portalen, se [övervakaren pipeline körs](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Du kan använda en Azure Resource Manager-mall för att skapa en utlösare. Stegvisa instruktioner finns [skapa ett Azure data factory med hjälp av en Resource Manager-mall](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Skicka utlösaren starttiden för en pipeline
Azure Data Factory version 1 stöder läsning eller skrivning partitionerade data med hjälp av systemvariablerna: **SliceStart**, **SliceEnd**, **WindowStart**, och **WindowEnd**. Du kan åstadkomma detta genom att använda en pipeline-parameter i Azure Data Factory version 2. Start- och schema för utlösaren anges som värde för parametern pipeline. I följande exempel visas den schemalagda tiden för utlösaren skickas som ett värde till pipelinen **scheduledRunTime** parameter:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    

Mer information finns i anvisningarna i [så att läsa eller skriva partitionerad data](how-to-read-write-partitioned-data.md).

## <a name="json-schema"></a>JSON-schema
Följande JSON-definitionen visar hur du skapar en utlösare för schema med schemaläggning och upprepning:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
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
>  Den **parametrar** -egenskapen är en obligatorisk egenskap för den **pipelines** element. Om din pipeline tar inga parametrar, måste du inkludera ett tomt JSON-definitionen för den **parametrar** egenskapen.


### <a name="schema-overview"></a>Översikt över schema
Följande tabell innehåller en översikt över större schemaelement som är relaterade till upprepning och schemaläggning av en utlösare:

| JSON-egenskap | Beskrivning |
|:--- |:--- |
| **startTime** | Ett datum / tid-värde. För enkel scheman, värdet för den **startTime** egenskap gäller för den första förekomsten. För komplexa scheman utlösaren startar inga snabbare än det angivna **startTime** värde. |
| **Sluttid** | Slutdatum och sluttid för utlösaren. Utlösaren köra inte efter angivna slutdatum och sluttid. Värdet för egenskapen får inte vara tidigare. Den här egenskapen är valfri. |
| **timeZone** | Tidszon. För närvarande stöds endast UTC-tidszonen. |
| **upprepning** | Ett återkommande objekt som anger reglerna för upprepning för utlösaren. Återkommande objekt stöder den **frekvens**, **interva**l, **endTime**, **antal**, och **schema**element. När ett återkommande objekt definieras i **frekvens** -element krävs. Andra element för upprepning objektet är valfria. |
| **frekvens** | Enheten för att utlösaren ska återkomma frekvens. Värdena som stöds omfattar ”minute”, ”timmar”, ”dag”, ”vecka” och ”månad”. |
| **interval** | Ett positivt heltal som anger intervallet för den **frekvens** -värde som anger hur ofta utlösaren körs. Till exempel om den **intervall** är 3 och **frekvens** ”vecka” utlösaren återkommer var 3 veckor. |
| **schema** | Upprepningsschemat för utlösaren. En utlösare med en angiven **frekvens** värde ändrar dess upprepning baserat på ett återkommande schema. Den **schema** egenskap innehåller ändringar för återkommande som baseras på minuter, timmar, veckodagar, dagar i månaden och veckonumret.


### <a name="schema-defaults-limits-and-examples"></a>Standardvärdena för schemat, begränsningar och exempel

| JSON-egenskap | Typ | Krävs | Standardvärde | Giltiga värden | Exempel |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Sträng | Ja | Ingen | ISO 8601-datum/tid | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **upprepning** | Objekt | Ja | Ingen | Upprepningsobjekt | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Tal | Nej | 1 | 1 till 1 000 | `"interval":10` |
| **Sluttid** | Sträng | Ja | Ingen | Ett datum / tid-värde som representerar en tidpunkt i framtiden. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schema** | Objekt | Nej | Ingen | Schemaobjekt | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Egenskapen startTime
Följande tabell visar hur **startTime** styr egenskapen en utlösare som kör:

| startTime-värde | Upprepning utan schema | Upprepning med schema |
|:--- |:--- |:--- |
| Starttid i förfluten tid | Beräknar den första framtida körningstiden efter starttiden och körs vid den tiden.<br/><br/>Kör efterföljande körningar baserat på Beräkna från den senaste körningstiden.<br/><br/>Se exemplet som följer den här tabellen. | Utlösaren startar _inte sooner än_ den angivna starttiden. Den första förekomsten är baserad på det schema som beräknas utifrån starttiden.<br/><br/>Kör efterföljande körningar baserat på det återkommande schemat. |
| Starttid i framtiden eller aktuell tid | Kör en gång vid den angivna starttiden.<br/><br/>Kör efterföljande körningar baserat på Beräkna från den senaste körningstiden. | Utlösaren startar _tidigast_ än den angivna starttiden. Den första förekomsten är baserad på det schema som beräknas utifrån starttiden.<br/><br/>Kör efterföljande körningar baserat på det återkommande schemat. |

Låt oss se ett exempel på vad som händer när starttiden är tidigare med ett återkommande, men inget schema. Anta att den aktuella tiden är `2017-04-08 13:00`, starttiden är `2017-04-07 14:00`, och upprepningen är två dagar. (Den **återkommande** värdet definieras genom att ange den **frekvens** egenskapen ”dag” och **intervall** egenskap till 2.) Observera att den **startTime** värde är i förflutna och inträffar före aktuell tid.

Med dessa villkor sker den första körningen `2017-04-09 at 14:00`. Scheduler-motor beräknar körningsförekomster från starttiden. Alla tidigare instanser ignoreras. Motorn använder nästa förekomst som förekommer i framtiden. I det här scenariot starttiden är `2017-04-07 at 2:00pm`, så nästa förekomst är två dagar från den tid, vilket är `2017-04-09 at 2:00pm`.

Den första körningstiden är samma även om den **startTime** värdet är `2017-04-05 14:00` eller `2017-04-01 14:00`. Efter den första körningen beräknas efterföljande körningar med hjälp av schemat. Efterföljande körningar är därför vid `2017-04-11 at 2:00pm`, sedan `2017-04-13 at 2:00pm`, sedan `2017-04-15 at 2:00pm`och så vidare.

Slutligen när timmar eller minuter har ställts in i schemat för en utlösare, används timmar eller minuter efter den första körningen som standard.

### <a name="schedule-property"></a>schemat för egenskapen
Dels måste kan användning av ett schema begränsa antalet utlösaren körningar. Om en utlösare med en månatlig frekvens är schemalagd att köras endast på dag 31, till exempel körs utlösaren bara på de månader som har 31 dagar.

Ett schema kan å andra sidan även utöka antalet utlösarkörningar. Till exempel körs en utlösare med en månatlig frekvens som har schemalagts för att köras med dagar i månaden 1 och 2, 1 och 2 dagar i månaden, snarare än en gång i månaden.

Om flera **schema** element anges är utvärderingsordningen från det största till inställningen för minsta schema. Utvärderingen börjar med vecka antal och sedan månad, dag, veckodag, timme, och slutligen minut.

I följande tabell beskrivs de **schema** element i detalj:


| JSON-element | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |
| **minuter** | Minuter för den timme då utlösaren körs. | <ul><li>Integer</li><li>Heltalsmatris</li></ul>
| **timmar** | Timmar på dagen då utlösaren körs. | <ul><li>Integer</li><li>Heltalsmatris</li></ul> |
| **Veckodagar** | Dagar i veckan då utlösaren körs. Värdet kan anges med en vecka frekvens. | <ul><li>Måndag, tisdag, onsdag, torsdag, fredag, lördag, söndag</li><li>Matris med-Dagvärden (maximal matrislängd storlek är 7)</li><li>Detta värde är inte skiftlägeskänsliga</li></ul> |
| **monthlyOccurrences** | Dagar i månaden som utlösaren körs. Värdet kan anges med en månatlig frekvens. | <ul><li>Matris med **monthlyOccurence** objekt: `{ "day": day,  "occurrence": occurence }`.</li><li>Den **dag** attributet är veckodag då utlösaren körs. Till exempel en **monthlyOccurrences** egenskap med ett **dag** värdet för `{Sunday}` innebär varje söndag i månaden. Den **dag** -attribut krävs.</li><li>Den **förekomsten** attributet är förekomsten av det angivna **dag** under månaden. Till exempel en **monthlyOccurrences** egenskap med **dag** och **förekomsten** värdena för `{Sunday, -1}` innebär sista söndagen i månaden. Den **förekomsten** attributet är valfritt.</li></ul> |
| **monthDays** | Dagen i månaden som utlösaren körs. Värdet kan anges med en månatlig frekvens. | <ul><li>Ett värde < = -1 och > =-31</li><li>Ett värde > = 1 och < = 31</li><li>Matris med-värden</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Exempel på utlösare återkommande scheman
Det här avsnittet innehåller exempel på återkommande scheman och fokuserar på de **schema** objektet och dess element.

I exempel anta att den **intervall** värdet är 1 och att den **frekvens** värdet är korrekt enligt schemadefinitionen. Du kan till exempel har en **frekvens** värdet för ”dag” och har också en ”monthDays” ändring den **schema** objekt. Begränsningar som dessa anges i tabellen i föregående avsnitt.

| Exempel | Beskrivning |
|:--- |:--- |
| `{"hours":[5]}` | Kör på 5:00:00 varje dag. |
| `{"minutes":[15], "hours":[5]}` | Kör kl 5:15 varje dag. |
| `{"minutes":[15], "hours":[5,17]}` | Kör på 5:15 AM och 17:15:00 varje dag. |
| `{"minutes":[15,45], "hours":[5,17]}` | Kör kl 5:15, 5:45 AM, 17:15:00 och 17:45:00 varje dag. |
| `{"minutes":[0,15,30,45]}` | Kör var 15: e minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Kör varje timme. Den här utlösaren körs varje timme. Protokollet styrs av den **startTime** när ett värde har angetts. Om ett värde som angetts styrs i minuter av tiden för skapandet av. Till exempel om starttiden eller skapelsetid (beroende på vilket) är 12:25 PM, utlösaren så körs 00:25, 01:25 02:25,..., och 23:25.<br/><br/>Det här schemat motsvarar med en utlösare med en **frekvens** värdet för ”timme” en **intervall** värdet 1 och Nej **schema**.  Det här schemat kan användas med olika **frekvens** och **intervall** värden för att skapa andra utlösare. Till exempel, när den **frekvens** värdet är ”månad”, schemat körs bara en gång i månaden i stället för varje dag, när den **frekvens** värdet är ”dag”. |
| `{"minutes":[0]}` | Kör varje timme timma. Den här utlösaren körs varje timma som börjar vid 12:00:00, 1:00:00, 2:00:00, och så vidare.<br/><br/>Det här schemat motsvarar en utlösare med en **frekvens** värdet för ”timme” och en **startTime** värdet noll minuter, eller på Nej **schema** men **frekvens**  värdet för ”dag”. Om den **frekvens** värdet är ”vecka” eller ”månad” schemat körs en dag som en vecka eller en dag i månaden, endast. |
| `{"minutes":[15]}` | Kör 15 minuter över varje timme. Den här utlösaren körs varje 15 minuter efter den timme från kl 00:15, 1:15 AM, 2:15 AM, och så vidare och sista kl 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Kör på 17:00:00 på lördagar varje vecka. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Kör på 17:00:00 på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Kör på 17:15:00 och 17:45:00 på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Kör var 15: e minut på veckodagar. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Kör var 15: e minut på vardagar mellan 9:00:00 och 4:45 PM. |
| `{"weekDays":["tuesday", "thursday"]}` | Kör varje tisdag och torsdag vid den angivna starttiden. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Kör på 6:00:00 28 dagen varje månad (förutsatt att en **frekvens** värdet för ”månad”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Kör på 6:00:00 på den sista dagen i månaden. Använd -1 om du vill köra en utlösare på den sista dagen i månaden i stället för dag 28, 29, 30 och 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Kör på 6:00:00 på första och sista dag i månaden. |
| `{monthDays":[1,14]}` | Kör den första och 14 dagen varje månad på den angivna starttiden. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Kör den första fredagen i månaden på 5:00:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Kör den första fredagen i månaden på den angivna starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Kör den tredje fredagen från slutet av månad, varje månad, vid den angivna starttiden. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Kör första och sista fredagen i varje månad kl 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Kör första och sista fredagen i månaden på den angivna starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Kör den femte fredagen i månaden på den angivna starttiden. När det finns inga femte fredag i en månad, körs pipelinen inte, eftersom den har schemalagts för att köras endast på femte fredagar. Att köra utlösaren på sist fredagen i månaden, inträffar bör du använda -1 i stället för 5 för den **förekomsten** värde. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Kör var 15: e minut på den sista fredagen i månaden. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Kör kl 5:15, 5:45 AM, 17:15:00 och 17:45:00 på den tredje onsdagen varje månad. |


## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
