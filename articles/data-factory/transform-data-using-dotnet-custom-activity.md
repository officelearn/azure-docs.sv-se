---
title: Använda anpassade aktiviteter i en pipeline
description: Lär dig hur du skapar anpassade aktiviteter och använder dem i en Azure Data Factory-pipeline.
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 4913152125b0fafd74db575f835d53fa992b075e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260584"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-use-custom-activities.md)
> * [Aktuell version](transform-data-using-dotnet-custom-activity.md)

Det finns två typer av aktiviteter som du kan använda i en Azure Data Factory-pipeline.

- [Dataflyttningsaktiviteter](copy-activity-overview.md) för att flytta data mellan [käll- och sinkdatalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
- [Dataomvandlingsaktiviteter](transform-data.md) för att omvandla data med hjälp av beräkningstjänster som Azure HDInsight, Azure Batch och Azure Machine Learning.

Om du vill flytta data till/från ett datalager som Data Factory inte stöder, eller för att omvandla/bearbeta data på ett sätt som inte stöds av Data Factory, kan du skapa en **anpassad aktivitet** med din egen dataförflyttnings- eller omvandlingslogik och använda aktiviteten i en pipeline. Den anpassade aktiviteten kör din anpassade kodlogik på en **Azure Batch-pool** med virtuella datorer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se följande artiklar om du inte har tidigare i Azure Batch-tjänsten:

* [Grunderna i Azure Batch](../batch/batch-technical-overview.md) för en översikt över Azure Batch-tjänsten.
* [Cmdlet New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) för att skapa ett Azure Batch-konto (eller) [Azure-portal](../batch/batch-account-create-portal.md) för att skapa Azure Batch-kontot med Azure-portalen. Se Använda PowerShell för att hantera Azure [Batch-konto](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) artikel för detaljerade instruktioner om hur du använder cmdlet.
* [Cmdlet new-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) för att skapa en Azure Batch-pool.

## <a name="azure-batch-linked-service"></a>Azure Batch-länkad tjänst

Följande JSON definierar ett exempel på Azure Batch-länkad tjänst. Mer information finns i [Beräkningsmiljöer som stöds av Azure Data Factory](compute-linked-services.md)

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
    }
}
```

 Mer information om Azure Batch-länkad tjänst finns i artikeln [Beräkna länkade tjänster.](compute-linked-services.md)

## <a name="custom-activity"></a>Anpassad aktivitet

Följande JSON-kodavsnitt definierar en pipeline med en enkel anpassad aktivitet. Aktivitetsdefinitionen har en referens till azure batch-länkade tjänsten.

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

I det här exemplet är helloworld.exe ett anpassat program som lagras i customactv2/helloworld-mappen i Azure Storage-kontot som används i resourceLinkedService. Den anpassade aktiviteten skickar det här anpassade programmet som ska köras på Azure Batch. Du kan ersätta kommandot till alla önskade program som kan köras på måloperationssystemet för Azure Batch Pool-noderna.

I följande tabell beskrivs namn och beskrivningar av egenskaper som är specifika för den här aktiviteten.

| Egenskap              | Beskrivning                              | Krävs |
| :-------------------- | :--------------------------------------- | :------- |
| namn                  | Namnet på den verksamhet som är på gång     | Ja      |
| description           | Text som beskriver vad aktiviteten gör.  | Inga       |
| typ                  | För Anpassad aktivitet är **aktivitetstypen Anpassad**. | Ja      |
| linkedServiceName     | Länkad tjänst till Azure Batch. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md)  | Ja      |
| command               | Kommando för det anpassade program som ska köras. Om programmet redan är tillgängligt på Azure Batch Pool Node kan resourceLinkedService och folderPath hoppas över. Du kan till exempel ange `cmd /c dir`vilket kommando som ska användas för att användas i Windows Batch Pool-noden. | Ja      |
| resourceLinkedService | Azure Storage Linked Service till lagringskontot där det anpassade programmet lagras | Inga &#42;       |
| folderPath            | Sökväg till mappen för det anpassade programmet och alla dess beroenden<br/><br/>Om du har beroenden som lagras i undermappar - det vill säga i en hierarkisk mappstruktur under *folderPath* - förenklas mappstrukturen för närvarande när filerna kopieras till Azure Batch. Det vill än, alla filer kopieras till en enda mapp utan undermappar. För att komma runt detta beteende, överväga att komprimera filerna, kopiera den komprimerade filen och sedan packa upp den med anpassad kod på önskad plats. | Inga &#42;       |
| referensObjekt      | En matris med befintliga länkade tjänster och datauppsättningar. De refererade länkade tjänsterna och datauppsättningarna skickas till det anpassade programmet i JSON-format så att din anpassade kod kan referera till datafabrikens resurser | Inga       |
| extendedProperties    | Användardefinierade egenskaper som kan skickas till det anpassade programmet i JSON-format så att din anpassade kod kan referera till ytterligare egenskaper | Inga       |
| retentionTimeInDays | Lagringstiden för de filer som skickas för anpassad aktivitet. Standardvärdet är 30 dagar. | Inga |

&#42; Egenskaper `resourceLinkedService` och `folderPath` måste antingen både anges eller båda utelämnas.

> [!NOTE]
> Om du skickar länkade tjänster som referensObjekt i anpassad aktivitet är det en bra säkerhetspraxis att skicka en Azure Key Vault-aktiverad länkad tjänst (eftersom den inte innehåller några säkra strängar) och hämta autentiseringsuppgifterna med hemligt namn direkt från Key Valv från koden. Du kan hitta ett exempel [här](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) som refererar till AKV-aktiverad länkad tjänst, hämtar autentiseringsuppgifterna från Key Vault och sedan kommer åt lagringen i koden.

## <a name="custom-activity-permissions"></a>Behörigheter för anpassad aktivitet

Den anpassade aktiviteten anger det automatiska användarkontot i Azure Batch till *icke-administratörsåtkomst med uppgiftsomfattning* (standardspecifikationen för automatisk användare). Du kan inte ändra behörighetsnivån för det automatiska användarkontot. Mer information finns i [Köra uppgifter under användarkonton i Batch | Konton för automatisk användare](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Köra kommandon

Du kan köra ett kommando direkt med anpassad aktivitet. I följande exempel körs kommandot "echo hello world" på azure batch pool-noder för målet och utskrifter utdata till stdout.

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

Det här exemplet visar hur du kan använda referenceObjects och extendedProperties för att skicka Data Factory-objekt och användardefinierade egenskaper till ditt anpassade program.

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

När aktiviteten körs lagras referensobjekt och extendedProperties i följande filer som distribueras till samma körningsmapp i SampleApp.exe:

- `activity.json`

  Lagrar utökadeegenskaper och egenskaper för den anpassade aktiviteten.

- `linkedServices.json`

  Lagrar en matris med länkade tjänster som definierats i egenskapen referenceObjects.

- `datasets.json`

  Lagrar en matris med datauppsättningar som definierats i egenskapen referenceObjects.

Följande exempelkod visar hur SampleApp.exe kan komma åt den information som krävs från JSON-filer:

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
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Hämta körningsutdata

Du kan starta en pipeline-körning med följande PowerShell-kommando:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

När pipelinen körs kan du kontrollera körningsutdata med följande kommandon:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

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

**Stdout** och **stderr** för ditt anpassade program sparas i **adfjobs-behållaren** i Azure Storage Linked Service som du definierade när du skapade Azure Batch Linked Service med ett GUID för uppgiften. Du kan hämta den detaljerade sökvägen från utdata för aktivitetskörning enligt följande kodavsnitt:

```
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
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Om du vill använda innehållet i stdout.txt i nedströmsaktiviteter kan du hämta sökvägen till filen\@stdout.txt i uttrycket "activity('MyCustomActivity").output.outputs[0]".

> [!IMPORTANT]
> - Activity.json, linkedServices.json och datasets.json lagras i körningsmappen för batch-aktiviteten. I det här exemplet lagras aktiviteten.json, linkedServices.json och datasets.json i `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` sökvägen. Om det behövs måste du städa upp dem separat.
> - För länkade tjänster som använder självvärdförd integrationskörning krypteras känslig information som nycklar eller lösenord av den självvärderade integrationskörningen för att säkerställa att autentiseringsuppgifterna finns kvar i kunddefinierad privat nätverksmiljö. Vissa känsliga fält kan saknas när de refereras av din anpassade programkod på det här sättet. Använd SecureString i extendedProperties i stället för att använda referens för länkad tjänst om det behövs.

## <a name="pass-outputs-to-another-activity"></a>Skicka utdata till en annan aktivitet

Du kan skicka anpassade värden från din kod i en anpassad aktivitet tillbaka till Azure Data Factory. Du kan göra det `outputs.json` genom att skriva in dem från din ansökan. Data Factory kopierar `outputs.json` innehållet i och lägger till det `customOutput` i aktivitetsutdata som värdet för egenskapen. (Storleksgränsen är 2 MB.) Om du vill använda `outputs.json` innehållet i nedströmsaktiviteter kan du hämta `@activity('<MyCustomActivity>').output.customOutput`värdet med hjälp av uttrycket .

## <a name="retrieve-securestring-outputs"></a>Hämta SecureString-utdata

Känsliga egenskapsvärden som anges som typen *SecureString*, som visas i några av exemplen i den här artikeln, maskeras på fliken Övervakning i användargränssnittet i Data Factory.  I faktisk pipeline-körning serialiseras dock en *SecureString-egenskap* som JSON i `activity.json` filen som oformaterad text. Ett exempel:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Denna serialisering är inte riktigt säker och är inte avsedd att vara säker. Avsikten är att tipsa till Data Factory att maskera värdet på fliken Övervakning.

Om du vill komma åt egenskaper av typen `activity.json` *SecureString* från en anpassad aktivitet läser du filen, som placeras i samma mapp som din . EXE, deserialisera JSON och sedan komma åt egenskapen JSON (extendedProperties => [propertyName] => värde).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a>Jämför v2 anpassad aktivitet och version 1 (anpassad) DotNet-aktivitet

I Azure Data Factory version 1 implementerar du en (anpassad) DotNet-aktivitet genom att `Execute` skapa `IDotNetActivity` ett .NET-klassbiblioteksprojekt med en klass som implementerar metoden för gränssnittet. Egenskaperna Länkade tjänster, datauppsättningar och utökade i JSON-nyttolasten för en (anpassad) DotNet-aktivitet skickas till körningsmetoden som starkt skrivna objekt. Mer information om versions 1-beteendet finns i [(Custom) DotNet i version 1](v1/data-factory-use-custom-activities.md). På grund av den här implementeringen måste din dotnet-aktivitetskod vara avsedd för .NET Framework 4.5.2. Version 1 DotNet-aktivitet måste också köras på Windows-baserade Azure Batch Pool-noder.

I Azure Data Factory V2 Custom Activity behöver du inte implementera ett .NET-gränssnitt. Du kan nu direkt köra kommandon, skript och din egen anpassade kod, kompilerad som en körbar. Om du vill konfigurera `Command` den här `folderPath` implementeringen anger du egenskapen tillsammans med egenskapen. Den anpassade aktiviteten överför den körbara `folderpath` och dess beroenden till och kör kommandot åt dig.

De länkade tjänsterna, datauppsättningarna (definierade i referensobjekt) och utökade egenskaper som definierats i JSON-nyttolasten för en anpassad datafabrik v2-aktivitet kan nås av din körbara som JSON-filer. Du kan komma åt de egenskaper som krävs med hjälp av en JSON-serialiserare som visas i föregående SampleApp.exe-kodexempel.

Med ändringarna som introducerades i custom activity för datafabrik V2 kan du skriva din anpassade kodlogik på önskat språk och köra den på Windows och Linux Operations Systems som stöds av Azure Batch.

I följande tabell beskrivs skillnaderna mellan den anpassade aktiviteten Data Factory V2 och Data Factory version 1 (Custom) DotNet Activity:

|Skillnader      | Anpassad aktivitet      | version 1 (anpassad) DotNet-aktivitet      |
| ---- | ---- | ---- |
|Hur anpassad logik definieras      |Genom att tillhandahålla en körbar      |Genom att implementera en .NET DLL      |
|Körningsmiljö för den anpassade logiken      |Windows eller Linux      |Windows (.NET Framework 4.5.2)      |
|Köra skript      |Stöder att skript körs direkt (till exempel "cmd /c echo hello world" på Windows VM)      |Kräver implementering i .NET DLL      |
|Datauppsättning krävs      |Valfri      |Krävs för att kedja aktiviteter och skicka information      |
|Skicka information från aktivitet till anpassad logik      |Genom ReferenceObjects (LinkedServices and Datasets) och ExtendedProperties (anpassade egenskaper)      |Genom ExtendedProperties (anpassade egenskaper), Indata och Utdatauppsättningar      |
|Hämta information i anpassad logik      |Tolkar activity.json, linkedServices.json och datasets.json lagrad i samma mapp i den körbara filen      |Genom .NET SDK (.NET Frame 4.5.2)      |
|Loggning      |Skriver direkt till STDOUT      |Implementera logger i .NET DLL      |

Om du har befintlig .NET-kod skriven för en version 1 (Anpassad) DotNet-aktivitet måste du ändra koden för att den ska fungera med den aktuella versionen av den anpassade aktiviteten. Uppdatera koden genom att följa dessa riktlinjer på hög nivå:

  - Ändra projektet från ett .NET-klassbibliotek till en konsolapp.
  - Starta programmet med `Main` metoden. Metoden `Execute` för `IDotNetActivity` gränssnittet behövs inte längre.
  - Läs och tolka de länkade tjänsterna, datauppsättningarna och aktiviteten med en JSON-seriellare och inte som starkt skrivna objekt. Skicka värdena för obligatoriska egenskaper till din huvudsakliga anpassade kodlogik. Se föregående SampleApp.exe-kod som ett exempel.
  - Logger-objektet stöds inte längre. Utdata från din körbara kan skrivas ut till konsolen och sparas i stdout.txt.
  - Paketet Microsoft.Azure.Management.DataFactories NuGet behövs inte längre.
  - Kompilera koden, ladda upp den körbara filen och dess beroenden till Azure Storage och definiera sökvägen i egenskapen. `folderPath`

Ett fullständigt exempel på hur det heltäckande DLL- och pipeline-exemplet som beskrivs i datafabriken version 1-artikeln [Använd anpassade aktiviteter i en Azure Data Factory-pipeline](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) kan skrivas om som ett datafabriksanpassningsaktivitet. [Data Factory Custom Activity sample](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample)

## <a name="auto-scaling-of-azure-batch"></a>Automatisk skalning av Azure Batch

Du kan också skapa en Azure Batch-pool med funktionen **för automatisk skalning.** Du kan till exempel skapa en azure batch-pool med 0 dedikerade virtuella datorer och en formel för automatisk skalning baserat på antalet väntande aktiviteter.

Exempelformeln här uppnår följande beteende: När poolen skapas börjar den med 1 virtuell dator. $PendingTasks mått definierar antalet aktiviteter i körning + aktivt tillstånd (i kö). Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger TargetDedicated därefter. Det säkerställer att TargetDedicated aldrig går längre än 25 virtuella datorer. Så, när nya uppgifter skickas, pool växer automatiskt och när uppgifter slutförs, virtuella datorer blir gratis en efter en och den automatiska skalning krymper dessa virtuella datorer. startingNumberOfVMs och maxNumberofVMs kan justeras efter dina behov.

Formel för automatisk skalning:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Mer information finns i Beräkna noder för [automatisk skalar i en Azure Batch-pool.](../batch/batch-automatic-scaling.md)

Om poolen använder standardvärdet [för automatisk skalningInvärdering](https://msdn.microsoft.com/library/azure/dn820173.aspx)kan batch-tjänsten ta 15-30 minuter att förbereda den virtuella datorn innan den anpassade aktiviteten körs. Om poolen använder en annan automatisk skalningInvärderingInterval kan batchtjänsten ta autoScaleEvaluationInterval + 10 minuter.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [Körning av maskininlärningsbatch](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
