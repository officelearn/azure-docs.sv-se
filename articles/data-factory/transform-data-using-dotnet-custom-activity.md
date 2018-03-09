---
title: "Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)"
description: "Lär dig hur du skapar anpassade aktiviteter och använda dem i ett Azure Data Factory-pipelinen."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 6aaeaaacdc9ee67ebbed3ea3090455dde2357c3d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-use-custom-activities.md)
> * [Version 2 – förhandsversion](transform-data-using-dotnet-custom-activity.md)

Det finns två typer av aktiviteter som du kan använda i ett Azure Data Factory-pipelinen.

- [Data movement aktiviteter](copy-activity-overview.md) att flytta data mellan [stöds källa och mottagare datalager](copy-activity-overview.md#supported-data-stores-and-formats).
- [Data transformation aktiviteter](transform-data.md) för att omvandla data med hjälp av compute-tjänster som Azure HDInsight, Azure Batch och Azure Machine Learning. 

För att flytta data till/från en lagra Data Factory inte har stöd för, eller för att transformera/bearbeta data på ett sätt som inte stöds av Data Factory, kan du skapa en **anpassad aktivitet** med dina egna data flyttas eller omvandling logik och användning aktiviteten i en pipeline. Den anpassade aktiviteten körs din anpassade kod logik på ett **Azure Batch** pool för virtuella datorer.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [(anpassat) DotNet aktivitet i Data Factory version 1](v1/data-factory-use-custom-activities.md).
 

Se följande artiklar om du har använt Azure Batch-tjänsten:

* [Grunderna i Azure Batch](../batch/batch-technical-overview.md) en översikt över Azure Batch-tjänsten.
* [Nya AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) för att skapa ett Azure Batch-konto (eller) [Azure-portalen](../batch/batch-account-create-portal.md) skapa Azure Batch-kontot med hjälp av Azure portal. Se [använda PowerShell för att hantera Azure Batch-kontot](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) artikel detaljerade anvisningar om hur du använder cmdlet.
* [Nya AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) för att skapa en Azure Batch-pool.

## <a name="azure-batch-linked-service"></a>Azure Batch länkad tjänst 
Följande JSON definierar ett exempel på Azure Batch länkade tjänsten. Mer information finns i [Compute miljöer som stöds av Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Läs mer om Azure Batch länkade tjänsten i [Compute länkade tjänster](compute-linked-services.md) artikel. 

## <a name="custom-activity"></a>Anpassad aktivitet

Följande JSON-fragment definierar en pipeline med en enkel anpassad aktivitet. Aktivitetsdefinitionen har en referens till Azure Batch länkade tjänsten. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

I det här exemplet är helloworld.exe ett anpassat program som lagras i mappen customactv2/helloworld i Azure Storage-konto som används i resourceLinkedService. Anpassad aktivitet skickar programmet ska köras på Azure Batch. Du kan ersätta kommandot till alla prioriterade program som kan köras på målsystemet åtgärden Azure Batch-Pool-noder. 

I följande tabell beskrivs namn och beskrivningar av egenskaper som är specifika för den här aktiviteten. 

| Egenskap              | Beskrivning                              | Krävs |
| :-------------------- | :--------------------------------------- | :------- |
| namn                  | Namnet på aktivitet i pipelinen     | Ja      |
| description           | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ                  | För anpassade aktiviteten aktivitetstypen är **anpassad**. | Ja      |
| linkedServiceName     | Länkade tjänsten Azure Batch. Mer information om den här länkade tjänsten, se [Compute länkade tjänster](compute-linked-services.md) artikel.  | Ja      |
| kommandot               | Kommandot för det anpassa programmet som ska köras. Om programmet redan är tillgängliga i Azure Batch-Pool noden kan resourceLinkedService och folderPath hoppas över. Du kan till exempel ange kommandot för att vara `cmd /c dir`, som stöds internt av noden Windows Batch-Pool. | Ja      |
| resourceLinkedService | Azure länkade lagringstjänsten till lagringskontot där programmet finns | Nej       |
| folderPath            | Sökvägen till mappen för det anpassa programmet och dess beroenden | Nej       |
| referenceObjects      | En matris med befintliga länkade tjänster och datauppsättningar. Den refererade länkade tjänster och datauppsättningar skickas till det anpassa programmet i JSON-format så att din anpassade kod kan hänvisa till resurser av Data Factory | Nej       |
| extendedProperties    | Användardefinierade egenskaper som kan skickas till det anpassa programmet i JSON-format så att din anpassade kod kan referera till ytterligare egenskaper | Nej       |

## <a name="executing-commands"></a>Kör kommandon

Direkt kan du köra ett kommando använder anpassad aktivitet. I följande exempel körs kommandot ”echo hello world” på Azure Batch-Pool målnoder och skriver ut utdata till stdout. 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>Skicka objekt och egenskaper

Det här exemplet visas hur du kan använda referenceObjects och extendedProperties för att skicka Data Factory-objekt och användardefinierade egenskaper till anpassade program. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

När aktiviteten har körts lagras referenceObjects och extendedProperties i följande filer som har distribuerats till samma mapp för körning av SampleApp.exe: 

- activity.json

  Lagrar extendedProperties och egenskaperna för den anpassade aktiviteten. 

- linkedServices.json

  Lagrar en matris med länkade tjänster som anges i egenskapen referenceObjects. 

- datasets.json

  Lagrar en matris med datauppsättningar som definierats i egenskapen referenceObjects. 

Följande exempelkod visa hur SampleApp.exe kan komma åt den begärda informationen från JSON-filer: 

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Hämta utdata för körning

  Du kan starta en pipeline-körning med följande PowerShell-kommando: 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  När pipelinen körs, kan du kontrollera utförande-utdatan med följande kommandon: 

  ```.powershell
  while ($True) {
      $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

      if(!$result) {
          Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
      }
      elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
          Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
      }
      else {
          Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
          $result
          break
      }
      ($result | Format-List | Out-String)
      Start-Sleep -Seconds 15
  }

  Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
  $result.Output -join "`r`n"

  Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
  $result.Error -join "`r`n"
  ```

  Den **stdout** och **stderr** av det anpassade programmet sparas i den **adfjobs** behållare i Azure länkade lagringstjänsten du definierat när du skapar Azure Batch länkade Tjänsten med ett GUID för uppgiften. Du kan få detaljerad sökväg från aktiviteten kör utdata som visas i följande fragment: 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
Om du vill använda innehållet i stdout.txt i underordnade aktiviteter kan du få sökvägen till filen stdout.txt i uttrycket ”@activity(MyCustomActivity).output.outputs [0]”. 

  > [!IMPORTANT]
  > - Activity.json, linkedServices.json och datasets.json lagras i mappen körning av aktiviteten Batch. I det här exemplet activity.json, linkedServices.json och datasets.json lagras i ”https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/” sökväg. Om det behövs, måste du rensa dem separat. 
  > - För länkade tjänster använder Self-Hosted integrering Runtime, känslig information som nycklar eller lösenord krypteras av Self-Hosted integrering körningen så autentiseringsuppgifter definierats förblir i kunden privata nätverksmiljö. Vissa känsliga fält kan vara saknas när refereras av din anpassade programkod på detta sätt. Använd SecureString i extendedProperties istället för att använda länkade tjänstreferensen om det behövs. 

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a>Jämför v2 anpassad aktivitet och version 1 (Custom) DotNet-aktivitet

  I Azure Data Factory version 1 du implementerar en (anpassat) DotNet aktivitet genom att skapa en .net-klassbiblioteket projekt med en klass som implementerar det `Execute` metod för den `IDotNetActivity` gränssnitt. Länkade tjänster, datauppsättningar och utökade egenskaper i JSON-nyttolast för en aktivitet för DotNet (anpassat) skickas till metoden körning som strikt typkontroll objekt. Mer information om hur version 1 finns [(anpassat) DotNet i version 1](v1/data-factory-use-custom-activities.md). På grund av den här implementeringen version 1 DotNet aktivitet koden har som mål .net Framework 4.5.2. Version 1 DotNet aktivitet har även ska köras på Windows-baserade Azure Batch-Pool noder. 

  Du behöver inte implementera ett gränssnitt för .net i Azure Data Factory V2 anpassad aktivitet. Du kan nu direkt köra kommandon, skript och egna anpassade kod kompileras som en körbar fil. Om du vill konfigurera den här implementeringen, anger du den `Command` egenskapen tillsammans med den `folderPath` egenskapen. Anpassad aktivitet överför den körbara filen och dess beroenden till `folderpath` och utför kommandot för dig. 

  Länkade tjänster, datauppsättningar (definieras i referenceObjects) och utökade egenskaper som definierats i JSON-nyttolast av en Data Factory-v2 anpassad aktivitet kan nås av den körbara filen som JSON-filer. Du kan komma åt de obligatoriska egenskaperna med hjälp av en JSON-serialisering som visas i föregående SampleApp.exe kodexempel. 

  Med de ändringar som införs i Data Factory V2 anpassad aktivitet du skriva egen kod-logik på ditt språk och köras på Windows och Linux-operativsystem som stöds av Azure Batch. 

  I följande tabell beskrivs skillnaderna mellan Data Factory V2 anpassad aktivitet och Data Factory version 1 (Custom) DotNet aktiviteten: 


|Skillnader      |version 2 anpassad aktivitet      | version 1 (Custom) DotNet-aktivitet      |
| ---- | ---- | ---- |
|Hur egen kod som har definierats      |Genom att tillhandahålla en körbar fil      |Genom att implementera en .net-DLL      |
|Körningsmiljön för egen kod      |Windows- eller Linux      |Windows (.Net Framework 4.5.2)      |
|Kör skript      |Stöder kör skript direkt (till exempel ”cmd /c echo hello world” på Windows virtuell dator)      |Kräver implementering i .net DLL      |
|DataSet som krävs      |Valfri      |Krävs för att kedja aktiviteter och skickar information      |
|Skicka information från aktiviteten till egen kod      |Via ReferenceObjects (LinkedServices och datauppsättningar) och ExtendedProperties (egna egenskaper)      |ExtendedProperties (egna egenskaper), indata och utdata-datauppsättningar      |
|Hämta information i egen kod      |Parsar activity.json och linkedServices.json datasets.json som lagras i samma mapp på den körbara filen      |Via .net SDK (.Net ram 4.5.2)      |
|Loggning      |Skriver direkt till STDOUT      |Implementera loggaren i .net DLL      |


  Om du har befintliga .net-kod som skrivs för en version 1 (Custom) DotNet-aktivitet som du behöver ändra koden att fungera med en version 2 anpassad aktivitet. Uppdatera din kod genom att följa riktlinjerna på hög nivå:  

   - Ändra projektet från en .net-klassbiblioteket till en Konsolapp. 
   - Starta programmet med den `Main` metoden. Den `Execute` metod för den `IDotNetActivity` gränssnittet inte längre behövs. 
   - Läsa och tolka länkade tjänster, datauppsättningar och aktivitet med en JSON-serialisering och inte som strikt typkontroll objekt. Överför värden för obligatoriska egenskaper till dina viktigaste anpassad kod logik. Referera till föregående SampleApp.exe koden som ett exempel. 
   - Loggaren objekt stöds inte längre. Utdata från den körbara filen kan skrivas till konsolen och sparas på stdout.txt. 
   - Det krävs inte längre Microsoft.Azure.Management.DataFactories NuGet-paketet. 
   - Kompilera koden, ladda upp den körbara filen och dess beroenden till Azure Storage och definiera sökvägen i den `folderPath` egenskapen. 

Ett komplett exempel på hur det slutpunkt till slutpunkt-DLL och pipeline exemplet beskrivs i Data Factory-version 1 artikel [använda anpassade aktiviteter i ett Azure Data Factory-pipelinen](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) kan skrivas om som en anpassad aktivitet för Data Factory-v2, se [ Data Factory version 2 anpassad aktivitet exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample). 

## <a name="auto-scaling-of-azure-batch"></a>Automatisk skalning av Azure Batch
Du kan också skapa en Azure Batch-pool med **Autoskala** funktion. Du kan till exempel skapa en azure batch-pool med 0 dedikerade virtuella datorer och en Autoskala formel baserat på antalet väntande åtgärder. 

Exempel formeln här uppnår på följande: När poolen skapas, det börjar med 1 VM. $PendingTasks mått definierar antalet aktiviteter i körs + aktiv (i kö) tillstånd.  Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger därefter TargetDedicated. Det garanterar att TargetDedicated aldrig är mer omfattande än 25 virtuella datorer. Så när nya aktiviteter skickas pool växer automatiskt och som slutförda uppgifter, virtuella datorer blir ledigt i taget och autoskalning krymper dessa virtuella datorer. startingNumberOfVMs och maxNumberofVMs kan justeras efter dina behov.

Autoskala formel:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Se [automatiskt skala compute-noder i en Azure Batch-pool](../batch/batch-automatic-scaling.md) mer information.

Om poolen använder standard [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), Batch-tjänsten kan ta 15 30 minuter att förbereda den virtuella datorn innan du kör den anpassade aktiviteten.  Om poolen använder en annan autoScaleEvaluationInterval, kan Batch-tjänsten ta autoScaleEvaluationInterval + 10 minuter.


## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du Transformera data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Pig-aktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [Machine Learning Batch Execution aktivitet](transform-data-using-machine-learning.md)
* [Aktiviteten lagrad procedur](transform-data-using-stored-procedure.md)
