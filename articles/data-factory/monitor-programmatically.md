---
title: Programmässigt övervaka en Azure-datafabrik
description: Lär dig hur du övervakar en pipeline i en datafabrik med hjälp av olika programvaruutvecklingspaket (SDK:er).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: d416a4a2bace2aeced6961d4959b0478feb0e650
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398797"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programmässigt övervaka en Azure-datafabrik

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs hur du övervakar en pipeline i en datafabrik med hjälp av olika SDK:er (Software Development Kits). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Dataområde

Data Factory lagrar endast pipelinekörda data i 45 dagar. När du frågar programmässigt efter data om Data Factory pipeline `Get-AzDataFactoryV2PipelineRun` körs - till exempel med `LastUpdatedAfter` `LastUpdatedBefore` PowerShell-kommandot - finns det inga maximala datum för valfria och parametrar. Men om du frågar efter data för det senaste året, till exempel, returnerar frågan inte ett fel, utan returnerar bara pipeline-körningsdata från de senaste 45 dagarna.

Om du vill spara pipelinekörningsdata i mer än 45 dagar konfigurerar du din egen diagnostikloggning med [Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
En fullständig genomgång av hur du skapar och övervakar en pipeline med .NET SDK finns i [Skapa en datafabrik och pipeline med .NET](quickstart-create-data-factory-dot-net.md).

1. Lägg till följande kod för att kontinuerligt kontrollera status för pipelinekörningen tills data har kopierats.

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

2. Lägg till följande kod till den hämtar information om kopieringsaktivitetskörning, till exempel storleken på de data som läs/skrivits.

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

Fullständig dokumentation om .NET SDK finns i [Data Factory .NET SDK-referens](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
En fullständig genomgång av hur du skapar och övervakar en pipeline med Python SDK finns i [Skapa en datafabrik och pipeline med Python](quickstart-create-data-factory-python.md).

Om du vill övervaka pipelinekörningen lägger du till följande kod:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Fullständig dokumentation om Python SDK finns i [Data Factory Python SDK-referens](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>REST-API
En fullständig genomgång av hur du skapar och övervakar en pipeline med REST API finns i [Skapa en datafabrik och pipeline med REST API](quickstart-create-data-factory-rest-api.md).
 
1. Kör följande skript för att kontinuerligt kontroller pipelinekörningens status tills kopieringen av data är klar.

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

Fullständig dokumentation om REST API finns i [Referens för DATA Factory REST API](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
En fullständig genomgång av hur du skapar och övervakar en pipeline med PowerShell finns i [Skapa en datafabrik och pipeline med PowerShell](quickstart-create-data-factory-powershell.md).

1. Kör följande skript för att kontinuerligt kontroller pipelinekörningens status tills kopieringen av data är klar.

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

Fullständig dokumentation om PowerShell-cmdlets finns i [Data Factory PowerShell cmdlet reference](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Nästa steg
Se [Övervaka pipelines med hjälp av Azure Monitor-artikel](monitor-using-azure-monitor.md) om du vill veta mer om hur du använder Azure Monitor för att övervaka Data Factory-pipelines. 

