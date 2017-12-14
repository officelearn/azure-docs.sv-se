---
title: "Hur du skapar utlösare i Azure Data Factory | Microsoft Docs"
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
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Hur du skapar en utlösare som kör en pipeline enligt ett schema
Den här artikeln innehåller steg för att skapa, starta och övervaka en utlösare. Konceptuell information om utlösare finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Läs [get started with Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (kom igång med Data Factory version 1) om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig.


## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Det här avsnittet visar hur du använder Azure PowerShell för att skapa, starta och övervaka en utlösare. Om du vill se det här exemplet fungerar först gå igenom den [Snabbstart: skapa en datafabrik med hjälp av Azure PowerShell](quickstart-create-data-factory-powershell.md). Sedan lägger du till följande kod main-metoden som skapar och startar en schema-utlösare som körs var 15: e minut. Utlösaren är associerad med en rörledning (**Adfv2QuickStartPipeline**) som du skapar som en del i Snabbstart.

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