---
title: Skapa schemautlösare i Azure Data Factory
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline enligt ett schema.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2018
ms.openlocfilehash: a0a01dad5ae86cf20d57ade845326838f8fd686a
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641604"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Skapa en utlösare som kör en pipeline enligt ett schema
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln innehåller information om schemautlösaren och stegen för att skapa, starta och övervaka en schemautlösare. För andra typer av utlösare finns i [Pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md).

När du skapar en schemautlösare anger du ett schema (startdatum, återkommande, slutdatum osv.) och associerar med en pipeline. Pipeliner och utlösare har en många-till-många-relation. Flera utlösare kan starta en pipeline. En enskild utlösare kan starta flera pipeliner.

I följande avsnitt finns steg för att skapa en schemautlösare på olika sätt. 

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt
Du kan skapa en **schemautlösare** för att schemalägga en pipeline så att den körs med jämna mellanrum (varje timme, dagligen osv.). 

> [!NOTE]
> En fullständig genomgång av hur du skapar en pipeline och en schemautlösare, som associerar utlösaren med pipelinen, och kör och övervakar pipelinen finns i [Snabbstart: skapa en datafabrik med datafabrikens användargränssnitt](quickstart-create-data-factory-portal.md).

1. Växla till fliken **Redigera,** som visas med en pennsymbol. 

    ![Växla till fliken Redigera](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. Välj **Utlösare** på menyn och välj sedan **Ny/Redigera**. 

    ![Menyn Ny utlösare](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. På sidan **Lägg till utlösare** väljer du **Välj utlösare...** och väljer sedan **+Nytt**. 

    ![Lägg till utlösare – ny utlösare](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. Gör följande på sidan **Ny utlösare:** 

    1. Bekräfta att **schema** är valt för **typ**. 
    1. Ange startdatum för utlösaren för **STARTdatum (UTC)**. Den är inställd på den aktuella datetime som standard. 
    1. Ange **Återkommande** för utlösaren. Välj ett av värdena i listrutan (Varje minut, Timme, Varje dag, Varje vecka, Varje vecka och Varje månad). Ange multiplikatorn i textrutan. Om du till exempel vill att utlösaren ska köras en gång var 15:e minut väljer du **Varje minut**och anger **15** i textrutan. 
    1. Om du inte vill ange en slutdatumtid för utlösaren för fältet **Slut** väljer du **Inget slut**. Om du vill ange en slutdatumtid väljer du **På datum**och anger slutdatumtid och väljer sedan **OK**. Det finns ingen associerad kostnad till varje pipelinekörning. Om du testar kanske du vill se till att pipelinen bara utlöses ett par gånger. Men se till att det finns tillräckligt med tid att köra pipelinen mellan publiceringstiden och sluttiden. Utlösaren träder endast i kraft när du har publicerat lösningen till Data Factory, och inte när du sparar utlösaren i användargränssnittet.

        ![Inställningar för utlösare](./media/how-to-create-schedule-trigger/trigger-settings.png)

1. I fönstret **Ny utlösare** väljer du **Ja** i alternativet **Aktiverad** och väljer sedan **OK**. Du kan använda den här kryssrutan för att inaktivera utlösaren senare. 

    ![Inställningar för utlösare – knappen Nästa](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. Granska varningsmeddelandet i fönstret **Ny utlösare** och välj sedan **OK**.

    ![Inställningar för utlösare – knappen Slutför](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. Välj **Publicera alla** om du vill publicera ändringarna i Data Factory. Tills du publicerar ändringarna i Data Factory startar inte utlösaren att utlösa pipelinen. 

    ![Knappen Publicera](./media/how-to-create-schedule-trigger/publish-2.png)

1. Växla till fliken **Pipeline-körningar** till vänster och välj sedan **Uppdatera** för att uppdatera listan. Du kommer att se pipeline-körningar som utlöses av den schemalagda utlösaren. Observera värdena i kolumnen **Aktiverad av**. Om du använder alternativet **Utlösare nu** visas den manuella utlösaren i listan. 

    ![Övervaka utlösta körningar](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. Växla till vyn **Trigger Runs** (Utlösarkörningar). 

    ![Övervaka utlösarkörningar](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I det här avsnittet visas hur du använder Azure PowerShell för att skapa, starta och övervaka en schemautlösare. Om du vill se det här exemplet fungerar går du först igenom [snabbstarten: Skapa en datafabrik med hjälp av Azure PowerShell](quickstart-create-data-factory-powershell.md). Lägg sedan till följande kod i huvudmetoden, som skapar och startar en schemautlösare som körs var 15:e minut. Utlösaren är associerad med en pipeline med namnet **Adfv2QuickStartPipeline** som du skapar som en del av snabbstarten.

1. Skapa en JSON-fil med namnet **MyTrigger.json** i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

    > [!IMPORTANT]
    > Innan du sparar JSON-filen anger du värdet **för startTime-elementet** på den aktuella UTC-tiden. Ange värdet för **endTime-elementet** till en timme efter den aktuella UTC-tiden.

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

    I JSON-utdraget:
    - **Typelementet** för utlösaren är inställt på "ScheduleTrigger".
    - **Frekvenselementet** är inställt på "Minut" och **intervallelementet** är inställt på 15. Därför kör utlösaren pipelinen var 15:e minut mellan start- och sluttiderna.
    - **EndTime-elementet** är en timme efter värdet för **elementet startTime.** Därför kör utlösaren pipelinen 15 minuter, 30 minuter och 45 minuter efter starttiden. Glöm inte att uppdatera starttiden till den aktuella UTC-tiden och sluttiden till en timme efter starttiden. 
    - Utlösaren är associerad med **Adfv2QuickStartPipeline-pipelinen.** Om du vill associera flera pipelines med en utlösare lägger du till fler **pipelineReference-avsnitt.**
    - Pipelinen i Snabbstarten tar två **parametervärden:** **inputPath** och **outputPath**. Därför skickar du värden för dessa parametrar från utlösaren.

1. Skapa en utlösare med hjälp av cmdleten **Set-AzDataFactoryV2Trigger:**

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. Bekräfta att utlösarens status **stoppas** med hjälp av cmdleten **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Starta utlösaren med hjälp av cmdleten **Start-AzDataFactoryV2Trigger:**

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Bekräfta att utlösarens status är **Startad** med hjälp av cmdleten **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  Hämta utlösaren körs i Azure PowerShell med hjälp av cmdleten **Get-AzDataFactoryV2TriggerRun.** Om du vill hämta information om utlösarkörningarna kör du följande kommando med jämna mellanrum. Uppdatera **triggerrunstartedafter** och **TriggerRunStartedBefore** värdena för att matcha värdena i utlösardefinitionen:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Information om hur du övervakar utlösarkörningar och pipeline-körningar i Azure-portalen finns i [Övervaka pipelinekörningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>.NET SDK
I det här avsnittet visas hur du använder .NET SDK för att skapa, starta och övervaka en utlösare. Om du vill se det här exemplet fungerar går du först igenom [snabbstarten: Skapa en datafabrik med hjälp av .NET SDK](quickstart-create-data-factory-dot-net.md). Lägg sedan till följande kod i huvudmetoden, som skapar och startar en schemautlösare som körs var 15:e minut. Utlösaren är associerad med en pipeline med namnet **Adfv2QuickStartPipeline** som du skapar som en del av snabbstarten.

Om du vill skapa och starta en schemautlösare som körs var 15:e minut lägger du till följande kod i huvudmetoden:

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

Om du vill övervaka en utlösarkörning `Console.WriteLine` lägger du till följande kod före den sista satsen i exemplet:

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

Information om hur du övervakar utlösarkörningar och pipeline-körningar i Azure-portalen finns i [Övervaka pipelinekörningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Python SDK
I det här avsnittet visas hur du använder Python SDK för att skapa, starta och övervaka en utlösare. Om du vill se det här exemplet fungerar går du först igenom [snabbstarten: Skapa en datafabrik med hjälp av Python SDK](quickstart-create-data-factory-python.md). Lägg sedan till följande kodblock efter kodblocket "övervaka pipeline-körningen" i Python-skriptet. Den här koden skapar en schemautlösare som körs var 15:e minut mellan de angivna start- och sluttiderna. Uppdatera **start_time** variabeln till aktuell UTC-tid och **variabeln end_time** till en timme efter den aktuella UTC-tiden.

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

Information om hur du övervakar utlösarkörningar och pipeline-körningar i Azure-portalen finns i [Övervaka pipelinekörningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Du kan använda en Azure Resource Manager-mall för att skapa en utlösare. Stegvisa instruktioner finns i [Skapa en Azure-datafabrik med hjälp av en Resource Manager-mall](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Skicka starttiden för utlösaren till en pipeline
Azure Data Factory version 1 stöder läsning eller skrivning av partitionerade data med hjälp av systemvariablerna: **SliceStart**, **SliceEnd**, **WindowStart**och **WindowEnd**. I den aktuella versionen av Azure Data Factory kan du uppnå detta genom att använda en pipeline-parameter. Starttiden och schemalagd tid för utlösaren anges som värde för pipelineparametern. I följande exempel skickas den schemalagda tiden för utlösaren som ett värde till parametern pipeline **scheduledRunTime:**

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON-schema
Följande JSON-definition visar hur du skapar en schemautlösare med schemaläggning och upprepning:

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
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
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
>  Egenskapen **parameters** är en obligatorisk egenskap i elementet **pipelines**. Om din pipeline inte tar emot några parametrar måste du ta med en JSON-definition för egenskapen **parameters**.


### <a name="schema-overview"></a>Schemaöversikt
I följande tabell ges en översikt över de viktigaste schemaelementen relaterade till upprepning och schemaläggning i en utlösare:

| JSON-egenskap | Beskrivning |
|:--- |:--- |
| **startTime** | Ett datum/tid-värde. För enkla scheman gäller värdet för egenskapen **startTime** den första förekomsten. För komplexa scheman startar utlösaren tidigast vid det angivna värdet för **startTime**. |
| **endTime** | Slutdatum och tidpunkt för utlösaren. Utlösaren körs inte efter angivet slutdatum och sluttid. Värdet för egenskapen kan inte ha passerat. Den här egenskapen är valfri. |
| **timeZone** | Tidszonen. För närvarande stöds bara tidszonen UTC. |
| **Återkommande** | Ett upprepningsobjekt som anger upprepningsregler för utlösaren. Upprepningsobjektet har stöd för elementen **frequency** (frekvens), **interval** (intervall), **endTime** (sluttid), **count** (antal) och **schedule** (schema). När du definierar ett upprepningsobjekt är elementet **frequency** obligatoriskt. De andra elementen är valfria. |
| **Frekvens** | Frekvensen som utlösaren ska upprepas med. Du kan använda värden som ”minute”, ”hour”, ”day”, ”week” och ”month”. |
| **Intervall** | Ett positivt heltal som anger intervallet för värdet för **frequency** och som avgör hur ofta utlösaren körs. Om **interval** till exempel är 3 och **frequency** är ”week” (vecka) upprepas utlösaren var tredje vecka. |
| **Schema** | Upprepningsschemat för utlösaren. En utlösare med ett angivet värde för **frequency** ändrar sin upprepning baserat på ett upprepningsschema. Egenskapen **schedule** innehåller ändringar för upprepningen som baseras på minuter, timmar, veckodagar, dagar i månaden och veckonummer.


### <a name="schema-defaults-limits-and-examples"></a>Standardvärden för scheman, begränsningar och exempel

| JSON-egenskap | Typ | Krävs | Standardvärde | Giltiga värden | Exempel |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Sträng | Ja | Ingen | ISO 8601-datum/tid | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **Återkommande** | Objekt | Ja | Ingen | Upprepningsobjekt | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **Intervall** | Tal | Inga | 1 | 1 till 1 000 | `"interval":10` |
| **endTime** | Sträng | Ja | Ingen | Ett datum/tid-värde som representerar en tidpunkt i framtiden. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **Schema** | Objekt | Inga | Ingen | Schemaobjekt | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Egenskapen startTime
I följande tabell visas hur egenskapen **startTime** styr körningen av en utlösare:

| startTime-värde | Upprepning utan schema | Upprepning med schema |
|:--- |:--- |:--- |
| Starttid i förfluten tid | Beräknar första framtida körningstid efter starttiden och körs vid den tidpunkten.<br/><br/>Utför efterföljande körningar baserat på beräkningar från senaste körningstid.<br/><br/>Se exemplet som följer den här tabellen. | Utlösaren startar _tidigast_ den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<br/><br/>Kör efterföljande körningar baserat på upprepningsschemat. |
| Starttid i framtiden eller aktuell tid | Körs en gång på angiven starttid.<br/><br/>Utför efterföljande körningar baserat på beräkningar från senaste körningstid. | Utlösaren startar _tidigast_ den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<br/><br/>Kör efterföljande körningar baserat på upprepningsschemat. |

Låt oss se vad som händer när starttiden har passerat, med upprepning men utan schema. Anta att den aktuella tiden är `2017-04-08 13:00`, starttiden är `2017-04-07 14:00` och upprepningen är varannan dag. **(Upprepningsvärdet** definieras genom att **frequency** frekvensegenskapen ställs in på "dag" och intervallegenskapen till 2.) **interval** Observera att **startTime-värdet** är tidigare och inträffar före den aktuella tiden.

Med dessa villkor sker den första körningen `2017-04-09 at 14:00`. Scheduler-motor beräknar körningsförekomster från starttiden. Alla tidigare instanser ignoreras. Motorn använder nästa förekomst som förekommer i framtiden. I det här fallet är starttiden `2017-04-07 at 2:00pm`, så nästa förekomst är två dagar från den tiden, vilket är `2017-04-09 at 2:00pm`.

Den första körningstiden är samma oavsett om värdet för **startTime** är `2017-04-05 14:00` eller `2017-04-01 14:00`. Efter den första körningen beräknas efterföljande körningar med hjälp av schemat. Efterföljande körningar är därför `2017-04-11 at 2:00pm`, sedan `2017-04-13 at 2:00pm`, sedan `2017-04-15 at 2:00pm` och så vidare.

När inga timmar eller minuter anges i schemat för en utlösare används som standard samma timmar och minuter som i den första körningen.

### <a name="schedule-property"></a>Egenskapen schedule
Å ena sidan kan ett schema begränsa antalet utlösarkörningar. Om en utlösare med månatlig frekvens till exempel har ett schema som bara körs dag 31 så körs utlösaren bara de månader som har en 31:a dag.

Ett schema kan å andra sidan även utöka antalet utlösarkörningar. En utlösare med månatlig frekvens som har schemalagts för att köras dag 1 och 2 körs två snarare än en gång i månaden.

Om du anger flera **schedule**-element utvärderas de från största till minsta schemainställning. Utvärderingen börjar med veckonummer, sedan dag i månaden, veckodag, timme och slutligen minut.

I följande tabell beskrivs **schedule**-elementen i detalj:


| JSON-element | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |
| **minutes** | Minuter för den timme då utlösaren körs. | <ul><li>Integer</li><li>Heltalsmatris</li></ul>
| **Timmar** | Timmar på dagen då utlösaren körs. | <ul><li>Integer</li><li>Heltalsmatris</li></ul> |
| **weekDays** | Veckodagar som utlösaren körs på. Värdet kan bara anges med en veckofrekvens. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday och Sunday</li><li>Matris med dagvärden (maximal matrisstorlek är 7)</li><li>Dagvärdena är inte skiftlägeskänsliga</li></ul> |
| **monthlyOccurrences** | Dagar i månaden som utlösaren körs på. Värdet kan bara anges med en månadsfrekvens. | <ul><li>Matris med **månatligaOccurrence-objekt:** `{ "day": day,  "occurrence": occurrence }`.</li><li>Attributet **day** är veckodagen som utlösaren körs på. Om egenskapen **monthlyOccurrences** till exempel har **day**-värdet `{Sunday}` innebär det varje söndag i månaden. Attributet **day** är obligatoriskt.</li><li>Attributet **occurrence** är förekomsten av **day**-värdet i månaden. Om egenskapen **monthlyOccurrences** till exempel har **day**- och **occurrence**-värdena `{Sunday, -1}` innebär det den sista söndagen i månaden. Attributet **occurrence** är valfritt.</li></ul> |
| **monthDays** | Dagar i månaden som utlösaren körs på. Värdet kan bara anges med en månadsfrekvens. | <ul><li>Ett värde < = -1 och > =-31</li><li>Ett värde > = 1 och < = 31</li><li>Matris med värden</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Exempel på scheman för upprepning av utlösare
I det här avsnittet ges exempel på upprepningsscheman med fokus på objektet **schedule** och dess element.

I exemplen antas att värdet för **interval** är 1 och att värdet för **frequency** är giltigt enligt schemadefinitionen. Du kan till exempel inte ha **frekvensvärdet** "dag" och även ha en "monthDays"-ändring i **schemaobjektet.** De här begränsningarna tas upp i tabellen i föregående avsnitt.

| Exempel | Beskrivning |
|:--- |:--- |
| `{"hours":[5]}` | Kör kl. 05.00 varje dag. |
| `{"minutes":[15], "hours":[5]}` | Kör kl. 05.15 varje dag. |
| `{"minutes":[15], "hours":[5,17]}` | Kör kl. 05.15 och 17.15 varje dag |
| `{"minutes":[15,45], "hours":[5,17]}` | Kör kl. 05.15, 5.45, 17.15 och 17.45 varje dag. |
| `{"minutes":[0,15,30,45]}` | Kör var 15:e minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Kör varje timme. Den här utlösaren körs varje timme. Minuterna styrs av **startTime**-värdet när du anger ett sådant. Om du inte anger något värde styrs minuterna av tiden för skapandet. Om starttiden eller skapandetiden (beroende på vilken som gäller) till exempel är 00.25 körs utlösaren 00.25, 01.25, 02.25, …, 23:25.<br/><br/>Det här schemat motsvarar att ha en utlösare med **frekvensvärdet** "timme", ett **intervallvärde** på 1 och inget **schema**.  Det här schemat kan användas med andra värden för **frequency** och **interval** om du vill skapa andra utlösare. När **frekvensvärdet** till exempel är "månad" körs schemat bara en gång i månaden, i stället för varje dag, när **frekvensvärdet** är "dag". |
| `{"minutes":[0]}` | Körs varje hel timme. Den här utlösaren körs varje timma med början vid 00.00, 01.00, 02.00 och så vidare.<br/><br/>Det här schemat motsvarar en utlösare med **frequency**-värdet ”hour” och **startTime**-värdet noll minuter, eller inget **schema** men **frequency**-värdet ”day”. Om **frekvensvärdet** är "vecka" eller "månad" körs schemat endast en dag i veckan eller en dag i månaden. |
| `{"minutes":[15]}` | Körs 15 minuter efter varje hel timme. Den här utlösaren körs 15 minuter efter varje timme med början vid 00.15, 01.15, 02.15 och så vidare. |
| `{"hours":[17], "weekDays":["saturday"]}` | Körs 17.00 varje lördag. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17.00 varje måndag, onsdag och fredag. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17.15 och 17.45 varje måndag, onsdag och fredag. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15:e minut på vardagar. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15:e minut på vardagar mellan 09.00 och 16.45. |
| `{"weekDays":["tuesday", "thursday"]}` | Körs varje tisdag och torsdag den angivna starttiden. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Körs 06.00 den 28:e dagen varje månad (förutsatt att **frequency**-värdet är ”month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Körs 06.00 den sista dagen i månaden. Om du vill köra en utlösare den sista dagen i månaden ska du använda -1 istället för dag 28, 29, 30 eller 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Körs 06.00 den första och sista dagen varje månad. |
| `{monthDays":[1,14]}` | Körs den första och den fjortonde dagen i varje månad den angivna starttiden. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs den första fredagen i varje månad 05.00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs den första fredagen i varje månad vid den angivna starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Körs den tredje fredagen från slutet av månad, varje månad, vid den angivna starttiden. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs den första och sista fredagen i varje månad 05.15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs den första och sista fredagen i varje månad vid den angivna starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Körs den femte fredagen i varje månad vid den angivna starttiden. När det inte finns någon femte fredag i en månad körs inte pipelinen, eftersom den är schemalagd att bara köras på den femte fredagen. Om du vill köra utlösaren på den sista fredagen i månaden kan du använda -1 istället för 5 som värde för **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Körs var 15:e minut den sista fredagen i månaden. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Körs 05.15, 05.45, 17.15 och 17.45 den tredje onsdagen varje månad. |


## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns i [Pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md#trigger-execution).
