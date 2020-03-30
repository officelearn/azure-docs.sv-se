---
title: Skapa aktivitetssamband för att köra uppgifter - Azure Batch
description: Skapa uppgifter som är beroende av slutförandet av andra uppgifter för bearbetning av MapReduce-stil och liknande stordataarbetsbelastningar i Azure Batch.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca771117e889afc8e143c4ca4626ab2d3bb4da2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022910"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Skapa aktivitetssamband för att köra aktiviteter som är beroende av andra aktiviteter

Du kan definiera aktivitetssamband för att köra en aktivitet eller uppsättning aktiviteter först när en överordnad aktivitet har slutförts. Några scenarier där aktivitetssamband är användbara är:

* MapReduce-liknande arbetsbelastningar i molnet.
* Jobb vars databehandlingsuppgifter kan uttryckas som ett riktat acykliskt diagram (DAG).
* Förrendering och efter rendering processer, där varje uppgift måste slutföras innan nästa uppgift kan börja.
* Alla andra jobb där underordnade aktiviteter är beroende av utdata från aktiviteter i föregående led.

Med Batch-aktivitetssamband kan du skapa aktiviteter som schemaläggs för körning på beräkningsnoder efter att en eller flera överordnade aktiviteter har slutförts. Du kan till exempel skapa ett jobb som återger varje bildruta i en 3D-film med separata, parallella uppgifter. Den slutliga uppgiften – den "sammanfogade uppgiften" – sammanfogar de renderade bildrutorna till hela filmen först efter att alla bildrutor har återges.

Som standard schemaläggs beroende aktiviteter för körning först efter att den överordnade aktiviteten har slutförts. Du kan ange en sambandsåtgärd för att åsidosätta standardbeteendet och köra aktiviteter när den överordnade aktiviteten misslyckas. Mer information finns i avsnittet [Beroendeåtgärder.](#dependency-actions)  

Du kan skapa uppgifter som är beroende av andra aktiviteter i en 1:1- eller 1:1-relation. Du kan också skapa ett intervallberoende där en aktivitet är beroende av att en grupp aktiviteter slutförs inom ett angivet intervall med aktivitets-ID: n. Du kan kombinera dessa tre grundläggande scenarier för att skapa många-till-många-relationer.

## <a name="task-dependencies-with-batch-net"></a>Aktivitetssamband med batch .NET
I den här artikeln diskuterar vi hur du konfigurerar aktivitetssamband med hjälp av [batch .NET-biblioteket.][net_msdn] Vi visar först hur du [aktiverar aktivitetssamband](#enable-task-dependencies) på dina jobb och sedan visar hur du [konfigurerar en aktivitet med samband](#create-dependent-tasks). Vi beskriver också hur du anger en beroendeåtgärd för att köra beroende aktiviteter om den överordnade misslyckas. Slutligen diskuterar vi [beroendescenarier](#dependency-scenarios) som Batch stöder.

## <a name="enable-task-dependencies"></a>Aktivera aktivitetssamband
Om du vill använda aktivitetssamband i batch-programmet måste du först konfigurera jobbet så att aktivitetssamband används. I Batch .NET aktiverar du den på ditt [CloudJob][net_cloudjob] genom att `true`ange egenskapen [UsesTaskDependencies][net_usestaskdependencies] på:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

I föregående kodavsnitt är "batchClient" en förekomst av klassen [BatchClient.][net_batchclient]

## <a name="create-dependent-tasks"></a>Skapa beroende aktiviteter
Om du vill skapa en aktivitet som är beroende av att en eller flera överordnade aktiviteter har slutförts kan du ange att aktiviteten "beror på" de andra aktiviteterna. Konfigurera [CloudTask][net_cloudtask]i Batch .NET . [Egenskapen DependsOn][net_dependson] med en instans av klassen [TaskDependencies:][net_taskdependencies]

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Kodavsnittet skapar en beroende uppgift med uppgifts-ID "Blommor". Aktiviteten "Blommor" beror på uppgifterna "Rain" och "Sun". Aktiviteten "Blommor" kommer att schemaläggas för att köras på en beräkningsnod först efter att aktiviteterna "Rain" och "Sun" har slutförts.

> [!NOTE]
> Som standard anses en aktivitet vara slutförd när **completed** den är i slutfört `0`tillstånd och dess **avslutningskod** är . I Batch .NET innebär detta en [CloudTask][net_cloudtask]. [State][net_taskstate] Tillståndsegenskapsvärdet `Completed` för och CloudTasks [TaskExecutionInformation][net_taskexecutioninformation]. [Egenskapsvärdet ExitCode][net_exitcode] är `0`. Hur du ändrar detta finns i avsnittet [Beroendeåtgärder.](#dependency-actions)
> 
> 

## <a name="dependency-scenarios"></a>Beroendescenarier
Det finns tre grundläggande aktivitetssambandsscenarier som du kan använda i Azure Batch: en-till-en, en-till-många och aktivitets-ID-intervallberoende. Dessa kan kombineras för att ge ett fjärde scenario, många-till-många.

| Scenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exempel |  |
|:---:| --- | --- |
|  [En-till-en](#one-to-one) |*taskB* beror på *taskA* <p/> *taskB* kommer inte att schemaläggas för körning förrän *taskA* har slutförts |![Diagram: 1:1-aktivitetssamband][1] |
|  [En-till-många](#one-to-many) |*taskC* beror på både *taskA* och *taskB* <p/> *taskC* kommer inte att schemaläggas för körning förrän både *taskA* och *taskB* har slutförts |![Diagram: 1:N-aktivitetssamband][2] |
|  [Aktivitets-ID-intervall](#task-id-range) |*taskD* beror på en rad aktiviteter <p/> *taskD* kommer inte att schemaläggas för körning förrän aktiviteterna med ID *1* till *10* har slutförts |![Diagram: Aktivitets-ID-områdessamband][3] |

> [!TIP]
> Du kan skapa **många till många** relationer, till exempel där aktiviteterna C, D, E och F är beroende av aktiviteterna A och B. Detta är användbart, till exempel i parallelliserade förbearbetningsscenarier där dina underordnade aktiviteter är beroende av utdata från flera uppströmsaktiviteter.
> 
> I exemplen i det här avsnittet körs en beroende aktivitet först när de överordnade aktiviteterna har slutförts. Det här beteendet är standardbeteendet för en beroende aktivitet. Du kan köra en beroende aktivitet när en överordnad aktivitet misslyckas genom att ange en beroendeåtgärd för att åsidosätta standardbeteendet. Mer information finns i avsnittet [Beroendeåtgärder.](#dependency-actions)

### <a name="one-to-one"></a>En-till-en
I en 1:1-relation är en aktivitet beroende av att en överordnad aktivitet har slutförts. Om du vill skapa sambandet anger du ett enda aktivitets-ID till [TaskDependencies][net_taskdependencies]. [OnId][net_onid] statisk metod när du fyller i [egenskapen DependsOn][net_dependson] i [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>En-till-många
I en 1:1-relation beror en aktivitet på att flera överordnade aktiviteter slutförs. Om du vill skapa sambandet anger du en samling aktivitets-ID:n till [TaskDependencies][net_taskdependencies]. [OnIds][net_onids] statisk metod när du fyller i [egenskapen DependsOn][net_dependson] i [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Aktivitets-ID-intervall
I ett samband med ett intervall med överordnade aktiviteter beror en aktivitet på slutförandet av aktiviteter vars ID ligger inom ett intervall.
Om du vill skapa sambandet anger du de första och sista aktivitets-ID:n i intervallet till [TaskDependencies][net_taskdependencies]. [OnIdRange][net_onidrange] statisk metod när du fyller i [egenskapen DependsOn][net_dependson] i [CloudTask][net_cloudtask].

> [!IMPORTANT]
> När du använder aktivitets-ID-områden för dina samband markeras endast aktiviteter med ID:n som representerar heltalsvärden av intervallet. Så intervallet `1..10` kommer `3` att `7`välja `5flamingoes`uppgifter och , men inte . 
> 
> Inledande nollor är inte signifikanta när du utvärderar `4`intervallberoenden, så aktiviteter med strängidentifierare och `04` `004` kommer alla att ligga *inom* intervallet och de kommer alla att behandlas som aktivitet `4`, så den första som ska slutföras kommer att uppfylla sambandet.
> 
> Varje aktivitet i intervallet måste uppfylla sambandet, antingen genom att slutföra framgångsrikt eller genom att slutföra med ett fel som är mappat till en beroendeåtgärd inställd på **Att uppfylla**. Mer information finns i avsnittet [Beroendeåtgärder.](#dependency-actions)
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Beroendeåtgärder

Som standard körs en beroende aktivitet eller uppsättning aktiviteter först när en överordnad aktivitet har slutförts. I vissa fall kanske du vill köra beroende aktiviteter även om den överordnade aktiviteten misslyckas. Du kan åsidosätta standardbeteendet genom att ange en beroendeåtgärd. En beroendeåtgärd anger om en beroende aktivitet kan köras, baserat på den överordnade aktivitetens lyckade eller misslyckade. 

Anta till exempel att en beroende aktivitet väntar på data från slutförandet av aktiviteten i föregående led. Om aktiviteten uppströms misslyckas kan den beroende aktiviteten fortfarande köras med äldre data. I det här fallet kan en beroendeåtgärd ange att den beroende aktiviteten kan köras trots att den överordnade aktiviteten inte fungerar.

En sambandsåtgärd baseras på ett avslutningsvillkor för den överordnade aktiviteten. Du kan ange en beroendeåtgärd för något av följande avslutningsvillkor. För .NET finns i klassen [ExitConditions][net_exitconditions] för mer information:

- När ett förbearbetningsfel inträffar.
- När ett filöverföringsfel inträffar. Om aktiviteten avslutas med en avslutningskod som har angetts via **exitCodes** eller **exitCodeRanges**och sedan stöter på ett filöverföringsfel, har den åtgärd som anges av avslutningskoden företräde.
- När aktiviteten avslutas med en avslutningskod som definieras av egenskapen **ExitCodes.**
- När aktiviteten avslutas med en avslutningskod som faller inom ett intervall som anges av egenskapen **ExitCodeRanges.**
- Standardfall, om aktiviteten avslutas med en avslutningskod som inte definieras av **ExitCodes** eller **ExitCodeRanges**, eller om aktiviteten avslutas med ett förbearbetningsfel och egenskapen **PreProcessingError** inte har angetts, eller om aktiviteten misslyckas med ett filöverföringsfel och egenskapen **FileUploadError** inte har angetts. 

Om du vill ange en beroendeåtgärd i .NET anger du [ExitOptions][net_exitoptions]. [Egenskapen DependencyAction][net_dependencyaction] för avslutningsvillkoret. Egenskapen **DependencyAction** tar ett av två värden:

- Om du ställer in egenskapen **DependencyAction** som **uppfyller** anger att beroende aktiviteter kan köras om den överordnade aktiviteten avslutas med ett angivet fel.
- Om du ställer in egenskapen **DependencyAction** på **Blockera** anges att beroende aktiviteter inte kan köras.

Standardinställningen för egenskapen **DependencyAction** är **Uppfyller** för avslutningskod 0 och **Blockera** för alla andra avslutningsvillkor.

Följande kodavsnitt anger egenskapen **DependencyAction** för en överordnad aktivitet. Om den överordnade aktiviteten avslutas med ett förbearbetningsfel eller med de angivna felkoderna blockeras den beroende aktiviteten. Om den överordnade aktiviteten avslutas med något annat fel som inte är noll kan den beroende aktiviteten köras.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Kodexempel
[Exempelprojektet TaskDependencies][github_taskdependencies] är ett av [Azure Batch-kodexemplen][github_samples] på GitHub. Den här Visual Studio-lösningen visar:

- Aktivera aktivitetssamband för ett jobb
- Så här skapar du uppgifter som är beroende av andra aktiviteter
- Så här kör du dessa uppgifter på en pool med beräkningsnoder.

## <a name="next-steps"></a>Nästa steg
### <a name="application-deployment"></a>Appdistribution
[Funktionen programpaket](batch-application-packages.md) i Batch är ett enkelt sätt att både distribuera och version av de program som dina uppgifter körs på beräkningsnoder.

### <a name="installing-applications-and-staging-data"></a>Installera program och mellanlagringsdata
Se [Installera program och mellanlagringsdata på batchberäkningsnoder][forum_post] i Azure Batch-forumet för en översikt över metoder för att förbereda dina noder för att köra uppgifter. Det här inlägget är skrivet av en av Azure Batch-gruppmedlemmarna och är en bra primer på olika sätt att kopiera program, indata för uppgift och andra filer till dina beräkningsnoder.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: ett-till-en-beroende"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: beroende av 1:N"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: aktivitets-ID-områdessamband"
