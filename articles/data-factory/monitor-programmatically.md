---
title: Övervaka en Azure data factory programmässigt | Microsoft Docs
description: Lär dig hur du övervakar en pipeline i en datafabrik med hjälp av olika software development Kit (SDK).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 035e12da67d28e8e3fb46ac295717dd6b579922c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486621"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Övervaka en Azure data factory programmässigt
Den här artikeln beskriver hur du övervakar en pipeline i en datafabrik med hjälp av olika software development Kit (SDK). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Dataområdet

Data Factory lagrar bara data för pipelinekörning 45 dagar. När du fråga programmässigt efter data om Data Factory-pipeline-körningar – till exempel med PowerShell-kommando `Get-AzDataFactoryV2PipelineRun` -det finns inga högsta datum för den valfria `LastUpdatedAfter` och `LastUpdatedBefore` parametrar. Men om du fråga efter data för det senaste året, till exempel frågan inte returnerar ett fel, men returnerar bara pipeline-körning data från de senaste 45 dagarna.

Om du vill bevara pipelinekörning data i mer än 45 dagar, ställa in dina egna Diagnostisk loggning med [Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
En fullständig genomgång för att skapa och övervaka en pipeline med hjälp av .NET SDK finns i [skapa en datafabrik och pipeline med hjälp av .NET](quickstart-create-data-factory-dot-net.md).

1. Lägg till följande kod för att kontinuerligt Kontrollera status för pipelinekörningen tills den har slutat att kopiera data.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Lägg till följande kod som hämtar Kopieringsaktivitet körningsinformation, till exempel storleken på lästa/skrivna data.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Fullständig dokumentation för .NET SDK finns i [Data Factory .NET SDK-referensen](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
En fullständig genomgång för att skapa och övervaka en pipeline med Python SDK finns i [skapa en datafabrik och pipeline med hjälp av Python](quickstart-create-data-factory-python.md).

Om du vill övervaka pipelinekörningen lägger du till följande kod:

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Fullständig dokumentation om Python SDK finns [Data Factory Python SDK-referens](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>REST-API
En fullständig genomgång för att skapa och övervaka en pipeline med hjälp av REST API finns i [skapa en datafabrik och pipeline med REST API](quickstart-create-data-factory-rest-api.md).
 
1. Kör följande skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Kör följande skript för att hämta körningsinformation för kopieringsaktiviteten, till exempel storleken på data som lästs/skrivits.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Fullständig dokumentation för REST API finns i [Data Factory REST API-referens](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
En fullständig genomgång för att skapa och övervaka en pipeline med hjälp av PowerShell finns i [skapa en datafabrik och pipeline med hjälp av PowerShell](quickstart-create-data-factory-powershell.md).

1. Kör följande skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Kör följande skript för att hämta körningsinformation för kopieringsaktiviteten, till exempel storleken på data som lästs/skrivits.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Fullständig dokumentation för PowerShell-cmdlets finns i [cmdlet-referens för Data Factory PowerShell](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Nästa steg
Se [övervaka pipelines med Azure Monitor](monitor-using-azure-monitor.md) du lär dig om att använda Azure Monitor för att övervaka Data Factory-pipelines. 

