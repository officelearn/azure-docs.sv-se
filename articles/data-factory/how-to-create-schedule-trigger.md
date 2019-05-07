---
title: Skapa en schemautlösare i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline enligt ett schema.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: shlo
ms.openlocfilehash: 09f80f69857ae17a0136229fe9bf13d4f63e7096
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151077"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Skapa en utlösare som kör en pipeline enligt ett schema
Den här artikeln innehåller information om schemautlösare och stegen för att skapa, starta och övervaka en schemautlösare. För andra typer av utlösare finns i [Pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

När du skapar en schemautlösare kan du ange ett schema (startdatum, upprepning, slutdatum etc.) för utlösare och associera det med en pipeline. Pipeliner och utlösare har en många-till-många-relation. Flera utlösare kan starta en pipeline. En enskild utlösare kan starta flera pipeliner.

Följande avsnitt innehåller steg för att skapa en schemautlösare på olika sätt. 

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt
Du kan skapa en **schemautlösare** att schemalägga en pipeline kan köras regelbundet (varje timme, varje dag, osv.). 

> [!NOTE]
> En fullständig genomgång för att skapa en pipeline och en schemautlösare associera utlösaren med pipeline, och köra och övervaka pipelinen finns i [Snabbstart: skapa en datafabrik med hjälp av Användargränssnittet för Data Factory](quickstart-create-data-factory-portal.md).

1. Växla till fliken **Redigera**. 

    ![Växla till fliken Redigera](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Klicka på **Utlösare** på menyn och klicka på **New/Edit** (Nytt/Redigera). 

    ![Menyn Ny utlösare](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. På sidan **Add Triggers** (Lägg till utlösare) klickar du på **Choose trigger...** (Välj utlösare...) och klickar på **New** (Nytt). 

    ![Lägg till utlösare – ny utlösare](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. I den **ny utlösare** gör du följande steg: 

    1. Bekräfta att **schema** har valts för **typ**. 
    2. Ange start-datum/tid för utlösaren för **starta datum (UTC)**. Den är inställd på den aktuella datumet/tiden som standard. 
    3. Ange **upprepning** för utlösaren. Välj ett av värdena från den nedrullningsbara listan (varje minut, per timme, varje dag, varje vecka och per månad). Ange multiplikatorn i textrutan. Till exempel om du vill att utlösaren ska köras en gång för varje 15 minuter kan du välja **varje minut**, och ange **15** i textrutan. 
    4. För den **slutet** om du inte vill ange en sluttid för utlösaren, väljer **No End**. Välj för att ange ett omfattande tidsvärdet **på datumet**, och anger slutdatumet och klickar på **tillämpa**. Det finns ingen associerad kostnad till varje pipelinekörning. Om du vill testa kan du se till att pipelinen utlöses endast några gånger. Men se till att det finns tillräckligt med tid att köra pipelinen mellan publiceringstiden och sluttiden. Utlösaren träder endast i kraft när du har publicerat lösningen till Data Factory, och inte när du sparar utlösaren i användargränssnittet.

        ![Inställningar för utlösare](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. I den **ny utlösare** och kontrollera den **aktiverad** , och klickar på **nästa**. Du kan använda den här kryssrutan för att inaktivera utlösaren senare. 

    ![Inställningar för utlösare – knappen Nästa](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. På sidan **Ny utlösare** läser du varningsmeddelandet och klickar på **Slutför**.

    ![Inställningar för utlösare – knappen Slutför](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Klicka på **Publicera** för att publicera Data Factory-ändringar. Tills du publicera ändringar i Data Factory startar utlösaren inte utlösa pipeline-körningar. 

    ![Knappen Publicera](./media/how-to-create-schedule-trigger/publish-2.png)
8. Växla till fliken **Övervaka** till vänster. Om du vill uppdatera listan klickar du på **Uppdatera**. Du ser pipelinen körs utlöstes av schemalagda utlösaren. Observera värdena i kolumnen **Aktiverad av**. Om du använder **Utlös nu** alternativet kan du se den manuella utlösarkörningen i listan. 

    ![Övervaka utlösta körningar](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Klicka på nedåtpilen bredvid **Pipeline Runs** (Pipelinekörningar) för att växla till vyn **Trigger Runs** (Utlösarkörningar). 

    ![Övervaka utlösarkörningar](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Det här avsnittet visar hur du använder Azure PowerShell för att skapa, starta och övervaka en schemautlösare. Om du vill se det här exemplet fungerar först gå igenom den [Snabbstart: Skapa en datafabrik med hjälp av Azure PowerShell](quickstart-create-data-factory-powershell.md). Lägg sedan till följande kod till main-metoden som skapar och startar en schemautlösare som körs var 15: e minut. Utlösaren är associerad med en pipeline med namnet **Adfv2QuickStartPipeline** som du skapar som en del av snabbstarten.

1. Skapa en JSON-fil med namnet **MyTrigger.json** i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

    > [!IMPORTANT]
    > Innan du sparar JSON-fil, ange värdet för den **startTime** elementet så att den aktuella UTC-tiden. Ange värdet för den **endTime** element till en timme efter den aktuella UTC-tiden.

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

    I JSON-kodfragmentet:
    - Den **typ** element för utlösaren är inställd på ”ScheduleTrigger”.
    - Den **frekvens** elementet är inställt på ”Minute” och **intervall** element är inställd på 15. Därför kör utlösaren pipelinen varje kvart mellan start- och sluttider.
    - Den **endTime** element är en timme efter värdet för den **startTime** element. Därför kör utlösaren pipelinen 15 minuter, 30 minuter och 45 minuter efter starttiden. Glöm inte att uppdatera starttiden till aktuellt UTC-tid och sluttid för en timme efter starttiden. 
    - Utlösaren är associerad med den **Adfv2QuickStartPipeline** pipeline. Om du vill associera flera pipelines med en utlösare, lägga till fler **pipelineReference** avsnitt.
    - Pipelinen i snabbstarten tar två **parametrar** värden: **inputPath** och **outputPath**. Därför kan skicka du värden för dessa parametrar från utlösaren.

2. Skapa en utlösare med hjälp av den **Set-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Kontrollera att statusen för utlösaren är **stoppad** med hjälp av den **Get-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Starta utlösaren med hjälp av den **Start AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Kontrollera att statusen för utlösaren är **startad** med hjälp av den **Get-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6.  Hämta som utlösaren körs i Azure PowerShell med hjälp av den **Get-AzDataFactoryV2TriggerRun** cmdlet. Kör följande kommando med jämna mellanrum för att få information om utlösaren körs. Uppdatera den **TriggerRunStartedAfter** och **TriggerRunStartedBefore** värdena för att matcha värdena i utlösarens definition:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Övervaka utlösaren körs och pipelinen körs i Azure-portalen, se [övervaka pipelinekörningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>.NET SDK
Det här avsnittet visar hur du använder .NET SDK för att skapa, starta och övervaka en utlösare. Om du vill se det här exemplet fungerar först gå igenom den [Snabbstart: Skapa en datafabrik med hjälp av .NET SDK](quickstart-create-data-factory-dot-net.md). Lägg sedan till följande kod till main-metoden som skapar och startar en schemautlösare som körs var 15: e minut. Utlösaren är associerad med en pipeline med namnet **Adfv2QuickStartPipeline** som du skapar som en del av snabbstarten.

Lägg till följande kod till main-metoden för att skapa och starta en schemautlösare som körs var 15: e minut:

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

Om du vill övervaka körningen av en utlösare lägger du till följande kod innan senaste `Console.WriteLine` instruktionen i det här exemplet:

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

Övervaka utlösaren körs och pipelinen körs i Azure-portalen, se [övervaka pipelinekörningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Python SDK
Det här avsnittet visar hur du använder Python SDK för att skapa, starta och övervaka en utlösare. Om du vill se det här exemplet fungerar först gå igenom den [Snabbstart: Skapa en datafabrik med hjälp av Python SDK](quickstart-create-data-factory-python.md). Lägg sedan till följande kodblock efter kodblocket ”övervaka pipelinekörningen” i Python-skriptet. Den här koden skapar en schemautlösare som körs varje kvart mellan angivna start- och sluttider. Uppdatera den **starttid** variabeln till den aktuella UTC-tid och **end_time** variabeln till en timme efter den aktuella UTC-tiden.

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

Övervaka utlösaren körs och pipelinen körs i Azure-portalen, se [övervaka pipelinekörningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Du kan använda en Azure Resource Manager-mall för att skapa en utlösare. Stegvisa instruktioner finns i [skapa en Azure-datafabrik med hjälp av Resource Manager-mall](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Skicka utlösarens starttid för en pipeline
Azure Data Factory version 1 har stöd för att läsa eller skriva partitionerade data med hjälp av systemvariablerna: **SliceStart**, **SliceEnd**, **WindowStart**, och **WindowEnd**. I den aktuella versionen av Azure Data Factory kan du göra detta med hjälp av en pipeline-parameter. Start- och schema för utlösaren har angetts som värde för parametern pipeline. I följande exempel visas den schemalagda tiden för utlösaren skickas som ett värde till pipelinen **scheduledRunTime** parameter:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    

Mer information finns i anvisningarna i [hur att läsa eller skriva partitionerade data](how-to-read-write-partitioned-data.md).

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
| **recurrence** | Ett upprepningsobjekt som anger upprepningsregler för utlösaren. Upprepningsobjektet har stöd för elementen **frequency** (frekvens), **interval** (intervall), **endTime** (sluttid), **count** (antal) och **schedule** (schema). När du definierar ett upprepningsobjekt är elementet **frequency** obligatoriskt. De andra elementen är valfria. |
| **frequency** | Frekvensen som utlösaren ska upprepas med. Du kan använda värden som ”minute”, ”hour”, ”day”, ”week” och ”month”. |
| **interval** | Ett positivt heltal som anger intervallet för värdet för **frequency** och som avgör hur ofta utlösaren körs. Om **interval** till exempel är 3 och **frequency** är ”week” (vecka) upprepas utlösaren var tredje vecka. |
| **schedule** | Upprepningsschemat för utlösaren. En utlösare med ett angivet värde för **frequency** ändrar sin upprepning baserat på ett upprepningsschema. Egenskapen **schedule** innehåller ändringar för upprepningen som baseras på minuter, timmar, veckodagar, dagar i månaden och veckonummer.


### <a name="schema-defaults-limits-and-examples"></a>Standardvärden för scheman, begränsningar och exempel

| JSON-egenskap | Typ | Obligatoriskt | Standardvärde | Giltiga värden | Exempel |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | String | Ja | Ingen | ISO 8601-datum/tid | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Object | Ja | Ingen | Upprepningsobjekt | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Tal | Nej | 1 | 1 till 1 000 | `"interval":10` |
| **endTime** | String | Ja | Ingen | Ett datum/tid-värde som representerar en tidpunkt i framtiden. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Object | Nej | Ingen | Schemaobjekt | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Egenskapen startTime
I följande tabell visas hur egenskapen **startTime** styr körningen av en utlösare:

| startTime-värde | Upprepning utan schema | Upprepning med schema |
|:--- |:--- |:--- |
| Starttid i förfluten tid | Beräknar första framtida körningstid efter starttiden och körs vid den tidpunkten.<br/><br/>Utför efterföljande körningar baserat på beräkningar från senaste körningstid.<br/><br/>Se exemplet som följer den här tabellen. | Utlösaren startar _tidigast_ den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<br/><br/>Kör efterföljande körningar baserat på upprepningsschemat. |
| Starttid i framtiden eller aktuell tid | Körs en gång på angiven starttid.<br/><br/>Utför efterföljande körningar baserat på beräkningar från senaste körningstid. | Utlösaren startar _tidigast_ den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<br/><br/>Kör efterföljande körningar baserat på upprepningsschemat. |

Låt oss se vad som händer när starttiden har passerat, med upprepning men utan schema. Anta att den aktuella tiden är `2017-04-08 13:00`, starttiden är `2017-04-07 14:00` och upprepningen är varannan dag. (**Upprepningen** definieras genom att du sätter egenskapen **frequency** till ”day” och egenskapen **interval** till 2.) Observera att värdet för **startTime** har passerat och inträffat före aktuell tid.

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
| **hours** | Timmar på dagen då utlösaren körs. | <ul><li>Integer</li><li>Heltalsmatris</li></ul> |
| **weekDays** | Veckodagar som utlösaren körs på. Värdet kan bara anges med en veckofrekvens. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday och Sunday</li><li>Matris med dagvärden (maximal matrisstorlek är 7)</li><li>Dagvärdena är inte skiftlägeskänsliga</li></ul> |
| **monthlyOccurrences** | Dagar i månaden som utlösaren körs på. Värdet kan bara anges med en månadsfrekvens. | <ul><li>Matris med **monthlyOccurrence** objekt: `{ "day": day,  "occurrence": occurrence }`.</li><li>Attributet **day** är veckodagen som utlösaren körs på. Om egenskapen **monthlyOccurrences** till exempel har **day**-värdet `{Sunday}` innebär det varje söndag i månaden. Attributet **day** är obligatoriskt.</li><li>Attributet **occurrence** är förekomsten av **day**-värdet i månaden. Om egenskapen **monthlyOccurrences** till exempel har **day**- och **occurrence**-värdena `{Sunday, -1}` innebär det den sista söndagen i månaden. Attributet **occurrence** är valfritt.</li></ul> |
| **monthDays** | Dagar i månaden som utlösaren körs på. Värdet kan bara anges med en månadsfrekvens. | <ul><li>Ett värde < = -1 och > =-31</li><li>Ett värde > = 1 och < = 31</li><li>Matris med värden</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Exempel på scheman för upprepning av utlösare
I det här avsnittet ges exempel på upprepningsscheman med fokus på objektet **schedule** och dess element.

I exemplen antas att värdet för **interval** är 1 och att värdet för **frequency** är giltigt enligt schemadefinitionen. Du kan till exempel inte använda **frequency**-värdet ”day” och samtidigt ha en ”monthDays”-modifiering i **schedule**-objektet. De här begränsningarna tas upp i tabellen i föregående avsnitt.

| Exempel | Beskrivning |
|:--- |:--- |
| `{"hours":[5]}` | Kör kl. 05.00 varje dag. |
| `{"minutes":[15], "hours":[5]}` | Kör kl. 05.15 varje dag. |
| `{"minutes":[15], "hours":[5,17]}` | Kör kl. 05.15 och 17.15 varje dag |
| `{"minutes":[15,45], "hours":[5,17]}` | Kör kl. 05.15, 5.45, 17.15 och 17.45 varje dag. |
| `{"minutes":[0,15,30,45]}` | Kör var 15:e minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Kör varje timme. Den här utlösaren körs varje timme. Minuterna styrs av **startTime**-värdet när du anger ett sådant. Om du inte anger något värde styrs minuterna av tiden för skapandet. Om starttiden eller skapandetiden (beroende på vilken som gäller) till exempel är 00.25 körs utlösaren 00.25, 01.25, 02.25, …, 23:25.<br/><br/>Schemat motsvarar att ha en utlösare med **frequency**-värdet hour (timme), **interval**-värdet 1 och inget **schema**.  Det här schemat kan användas med andra värden för **frequency** och **interval** om du vill skapa andra utlösare. När värdet för **frequency** till exempel är ”month” körs schemat bara en gång i månaden, snarare än varje dag när värdet för **frequency** är ”day”. |
| `{"minutes":[0]}` | Körs varje hel timme. Den här utlösaren körs varje timma med början vid 00.00, 01.00, 02.00 och så vidare.<br/><br/>Det här schemat motsvarar en utlösare med **frequency**-värdet ”hour” och **startTime**-värdet noll minuter, eller inget **schema** men **frequency**-värdet ”day”. Om **frequency**-värdet är ”week” eller ”month” körs schemat en dag i veckan eller en dag i månaden. |
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
Detaljerad information om utlösare finns i [Pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
