---
title: Använd aktivitetsberoenden för att köra aktiviteter baserat på slutförande av andra aktiviteter – Azure Batch | Microsoft Docs
description: Skapa uppgifter som är beroende av andra aktiviteter för bearbetning av MapReduce stil och liknande stordata slutförs arbetsbelastningar i Azure Batch.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a9b44ed56774466bae2f0f5d48b5e012382721b
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865241"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Skapa aktivitetssamband för att köra uppgifter som är beroende av andra aktiviteter

Du kan definiera aktivitetsberoenden om du vill köra en aktivitet eller en uppsättning uppgifter som endast när en överordnad aktivitet har slutförts. Vissa scenarier där aktivitetsberoenden är användbara är:

* MapReduce-style arbetsbelastningar i molnet.
* Jobb vars databearbetningsaktiviteter kan uttryckas som en riktad Acyklisk graf (DAG).
* Rendering före och efter rendering processer, där varje uppgift måste slutföras innan nästa aktivitet kan börja.
* Alla andra jobb där nedströms aktiviteter beror på utdata från överordnad uppgifter.

Med aktivitetsberoenden i Batch kan skapa du aktiviteter som är schemalagda för körning vid beräkningsnoder efter att en eller flera överordnade uppgifter. Du kan till exempel skapa ett jobb som återger varje bildruta från en 3D-film med separata, parallella uppgifter. Sista uppgiften--”merge uppgiften”--sammanslagningar renderade bildrutorna fullständig film förrän alla ramar har gjorts har.

Som standard har beroende aktiviteter schemalagts för körning endast när den överordnade uppgiften har slutförts. Du kan ange en beroende-åtgärd för att åsidosätta standardbeteendet och köra aktiviteter när den överordnade uppgiften misslyckas. Se den [beroende åtgärder](#dependency-actions) information.  

Du kan skapa uppgifter som är beroende av andra aktiviteter i en 1: 1 eller en-till-många-relation. Du kan också skapa ett intervall beroende där en uppgift beroende av slutförandet av en grupp med aktiviteter inom ett visst intervall för uppgiften ID: N. Du kan kombinera dessa tre grundläggande scenarier för att skapa många-till-många-relationer.

## <a name="task-dependencies-with-batch-net"></a>Aktivitetsberoenden med Batch .NET
I den här artikeln diskuterar vi hur du konfigurerar aktivitetsberoenden med hjälp av den [Batch .NET] [ net_msdn] biblioteket. Först visar vi dig hur till [aktivera aktivitetssamband](#enable-task-dependencies) för dina projekt och sedan visar hur du [konfigurera en uppgift med beroenden](#create-dependent-tasks). Vi beskriver också hur du anger en beroende-åtgärd för att köra beroende aktiviteter om överordnat misslyckas. Slutligen kan vi diskutera det [beroende scenarier](#dependency-scenarios) som har stöd för Batch.

## <a name="enable-task-dependencies"></a>Aktivera aktivitetssamband
Om du vill använda aktivitetsberoenden i Batch-program, måste du först konfigurera jobbet om du vill använda aktivitetssamband. I Batch .NET och aktivera det på din [CloudJob] [ net_cloudjob] genom att ange dess [UsesTaskDependencies] [ net_usestaskdependencies] egenskap `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

I föregående kodfragment ”batchClient” är en instans av den [BatchClient] [ net_batchclient] klass.

## <a name="create-dependent-tasks"></a>Skapa beroende aktiviteter
Om du vill skapa en uppgift som är beroende av en eller flera överordnade aktiviteter slutförs, kan du ange att uppgiften ”beroende” de andra aktiviteterna. I Batch .NET, konfigurera den [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] egenskap med en instans av den [TaskDependencies] [ net_taskdependencies] klass:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Det här kodfragmentet skapar en beroende aktivitet med aktivitets-ID ”blommor”. ”Blommor”-aktivitet är beroende av uppgifter ”Regnmolnsfelet” och ”Sun”. Uppgiften ”blommor” kommer att schemaläggas att köra på en beräkningsnod bara efter aktiviteter som ”Regnmolnsfelet” och ”Sun” har slutförts.

> [!NOTE]
> Som standard anses en uppgift ska kunna slutföras när den har den **slutförts** tillstånd och dess **slutkod** är `0`. I Batch .NET innebär en [CloudTask][net_cloudtask].[ Tillstånd] [ net_taskstate] egenskapsvärdet för `Completed` och CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ExitCode] [ net_exitcode] egenskapsvärdet är `0`. Att ändra detta genom att se den [beroende åtgärder](#dependency-actions) avsnittet.
> 
> 

## <a name="dependency-scenarios"></a>Scenarier för beroende
Det finns tre grundläggande uppgiften beroende scenarier som du kan använda i Azure Batch: 1: 1, 1: n och uppgifts-ID variera beroende. Dessa kan kombineras för att ge ett fjärde scenario, många-till-många.

| Scenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exempel |  |
|:---:| --- | --- |
|  [1: 1](#one-to-one) |*Aktivitetb* beror på *Aktiviteta* <p/> *Aktivitetb* kommer inte schemaläggas för körning tills *Aktiviteta* har slutförts |![Diagram: 1: 1-aktivitetssamband][1] |
|  [En-till-många](#one-to-many) |*aktivitetC* är beroende av både *aktivitetA* och *aktivitetB*. <p/> *Aktivitetc* kommer inte schemaläggas för körning förrän både *Aktiviteta* och *Aktivitetb* har slutförts |![Diagram: en till många aktivitetssamband][2] |
|  [ID-intervall för uppgift](#task-id-range) |*Aktivitetd* är beroende av en serie aktiviteter <p/> *Aktivitetd* kommer inte schemaläggas för körning tills aktiviteterna med ID: N *1* via *10* har slutförts |![Diagram: Aktivitetssamband id-intervall][3] |

> [!TIP]
> Du kan skapa **många-till-många** relationer, till exempel där uppgifter C, D, E och F varje beroende aktiviteter A och B. Detta är användbart, till exempel i parallelliserad förbearbetning scenarier där nedströms aktiviteter beror på utdata från flera överordnade aktiviteter.
> 
> I exemplen i det här avsnittet är körs en beroende aktivitet endast när de överordnade uppgifterna slutföras. Detta är standardbeteendet för en beroende aktivitet. Du kan köra en beroende aktivitet när en överordnad aktivitet misslyckas genom att ange en beroende-åtgärd för att åsidosätta standardbeteendet. Se den [beroende åtgärder](#dependency-actions) information.

### <a name="one-to-one"></a>1: 1
I ett förhållande en uppgift som beror på en överordnad aktiviteten slutförs. Om du vill skapa beroendet, ange en enda aktivitets-ID till den [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] statisk metod när du ska fylla i [DependsOn] [ net_dependson] egenskapen för [CloudTask] [ net_cloudtask].

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
I en en-till-många-relation kan en aktivitet är beroende av flera överordnade aktiviteter slutförs. Om du vill skapa beroendet tillhandahåller en uppsättning aktivitets-ID till den [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] statisk metod när du ska fylla i [DependsOn] [ net_dependson] egenskapen för [CloudTask] [ net_cloudtask].

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

### <a name="task-id-range"></a>ID-intervall för uppgift
I ett beroende på en serie överordnade aktiviteter beroende en uppgift slutförs uppgifter vars ID: N finns inom ett intervall.
Om du vill skapa beroendet, ange först och sista aktivitets-ID: N i intervallet till den [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] statisk metod när du ska fylla i [DependsOn] [ net_dependson] egenskapen för [CloudTask] [ net_cloudtask].

> [!IMPORTANT]
> När du använder aktiviteten ID-intervall för dina beroenden, markerade endast aktiviteter med ID: N som representerar heltalsvärden som intervallet. Så intervallet `1..10` väljer uppgifter `3` och `7`, men inte `5flamingoes`. 
> 
> Inledande nollor inte är signifikant vid utvärdering av intervallet beroenden, så uppgifter med sträng identifierare `4`, `04` och `004` blir alla *inom* intervallet och de alla behandlas som uppgiften `4`, så att den första mallen för att slutföra att uppfylla beroendet.
> 
> Varje aktivitet i intervallet måste uppfylla beroende, antingen genom att slutförs eller om du har slutfört med fel som är mappad till en beroende åtgärder som angetts som **Satisfy**. Se den [beroende åtgärder](#dependency-actions) information.
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

## <a name="dependency-actions"></a>Beroende åtgärder

Som standard körs en beroende aktivitet eller en uppsättning aktiviteter endast när en överordnad aktivitet har slutförts. I vissa situationer kan du köra beroende aktiviteter även om den överordnade uppgiften misslyckas. Du kan åsidosätta standardbeteendet genom att ange en beroende-åtgärd. En beroende åtgärden anger om en beroende aktivitet är berättigade att köra, baserat på lyckad eller misslyckad för den överordnade uppgiften. 

Anta att en beroende aktivitet väntar på att data från att den överordnade uppgiften. Om den överordnade aktiviteten misslyckas, kanske fortfarande beroende aktiviteten kan köras med hjälp av äldre data. I det här fallet kan en beroende-åtgärd ange att aktiviteten kan användas för att köra trots felet för den överordnade uppgiften.

En beroende-åtgärden är baserad på ett avsluta-villkor för den överordnade uppgiften. Du kan ange en beroende-åtgärd för någon av följande avslutningsvillkor. .NET, finns det [ExitConditions] [ net_exitconditions] klass information:

- När ett förbearbetningsfel inträffar.
- Felet uppstår när en filöverföring. Om uppgiften avslutas med slutkoden som har angetts via **exitCodes** eller **exitCodeRanges**, och sedan möten filöverföring fel, den åtgärd som anges av slutkoden företräde.
- När uppgiften avslutas med en slutkod som definieras av den **ExitCodes** egenskapen.
- När uppgiften avslutas med slutkoden som ligger inom ett intervall som anges av den **ExitCodeRanges** egenskapen.
- Standard-fall, om uppgiften avslutas med en slutkod som inte definieras av **ExitCodes** eller **ExitCodeRanges**, eller om uppgiften avslutas med ett förbearbetningsfel och **PreProcessingError** egenskapen inte har angetts eller om uppgiften misslyckas med en fil laddar du upp fel och **FileUploadError** egenskapen har inte angetts. 

Om du vill ange en beroende-åtgärd i .NET, ange den [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] -egenskapen för Avsluta-villkor. Den **DependencyAction** egenskapen antar ett av två värden:

- Ange den **DependencyAction** egenskap **Satisfy** indikerar att beroende aktiviteter är berättigade att köra om den överordnade uppgiften avslutas med ett angivet fel.
- Ange den **DependencyAction** egenskap **blockera** indikerar att beroende aktiviteter inte har behörighet att köra.

Standardinställningen för den **DependencyAction** egenskapen är **Satisfy** för slutkoden 0, och **blockera** för alla andra avslutningsvillkor.

I följande kod kodfragmentet anger den **DependencyAction** egenskap för en överordnad aktivitet. Om den överordnade uppgiften avslutas med ett förbearbetningsfel eller med de angivna felkoderna, blockeras aktiviteten. Om den överordnade uppgiften avslutas med ett noll-fel, kan aktiviteten användas för att köra.

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
Den [TaskDependencies] [ github_taskdependencies] exempelprojektet är en av de [Azure Batch-kodexempel] [ github_samples] på GitHub. Den här Visual Studio-lösningen visar:

- Aktivera aktivitetssamband på ett jobb
- Så här skapar du uppgifter som är beroende av andra aktiviteter
- Hur du utför aktiviteterna i en pool med beräkningsnoder.

## <a name="next-steps"></a>Nästa steg
### <a name="application-deployment"></a>Programdistribution
Den [programpaket](batch-application-packages.md) i Batch är enkelt att både distribuera och version som de program som aktiviteterna kör på compute-noder.

### <a name="installing-applications-and-staging-data"></a>Installera program, organiserar data
Se [installera program, organiserar data på Batch-beräkningsnoder] [ forum_post] i Azure Batch-forumet för en översikt över metoder för att förbereda dina noder att köra aktiviteter. Det här inlägget är skrivs av en av Azure Batch-gruppmedlemmar en bra introduktion på olika sätt att kopiera program, indata för aktiviteten och andra filer till beräkningsnoderna.

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
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions#Microsoft_Azure_Batch_ExitOptions_DependencyAction
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: 1: 1-beroende"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: en-till-många-beroende"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: aktivitetssamband id-intervall"
