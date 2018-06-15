---
title: Använd aktiviteten beroenden för att köra uppgifter baserat på slutförande av andra åtgärder - Azure Batch | Microsoft Docs
description: Skapa uppgifter som är beroende av andra uppgifter för bearbetning av MapReduce format och liknande stordata slutförs arbetsbelastningar i Azure Batch.
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
ms.openlocfilehash: ba85e075c39251b0b3d7c4b8bc3f8d53a1afadf7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316825"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Skapa uppgift beroenden för att köra uppgifter som är beroende av andra aktiviteter

Du kan definiera beroenden aktivitet om du vill köra en aktivitet eller en uppsättning uppgifter som endast när en överordnad aktivitet har slutförts. Vissa scenarier där aktiviteten beroenden är användbara är:

* MapReduce-format arbetsbelastningar i molnet.
* Jobb vars databearbetningsuppgifter kan uttryckas som en riktat acykliskt diagram (DAG).
* Förrendering och efter återgivning processer, där varje uppgift måste slutföras innan nästa uppgift kan börja.
* Ett annat jobb som underordnade aktiviteter beror på utdata från överordnad uppgifter.

Du kan skapa aktiviteter som är schemalagda för körning på datornoderna efter slutförandet av en eller flera överordnade uppgifter med Batch uppgiften beroenden. Du kan till exempel skapa ett jobb som återger varje bildruta i en 3D-film med separat, parallella aktiviteter. Slutliga uppgift--”merge uppgiften”--sammanslagningar renderade ramar fullständig film förrän alla ramar har gjorts korrekt.

Som standard är beroende aktiviteter schemalagda för körning efter att den överordnade uppgiften har slutförts. Du kan ange en beroende-åtgärd för att åsidosätta standardbeteendet och köra uppgifter om överordnade misslyckas. Finns det [beroende åtgärder](#dependency-actions) information.  

Du kan skapa uppgifter som är beroende av andra aktiviteter i en-till-en- eller en-till-många-relation. Du kan också skapa ett intervall beroende där en aktivitet beror på slutförande av en grupp aktiviteter inom ett angivet intervall för aktiviteten ID: N. Du kan kombinera dessa tre grundläggande scenarier för att skapa många-till-många-relationer.

## <a name="task-dependencies-with-batch-net"></a>Beroenden för uppgift med Batch .NET
I den här artikeln diskuterar vi hur du konfigurerar samband med hjälp av den [Batch .NET] [ net_msdn] bibliotek. Vi först att visa hur till [aktivera sambandet](#enable-task-dependencies) för dina projekt och sedan visa hur du [konfigurera en aktivitet med beroenden](#create-dependent-tasks). Vi beskriver också hur du anger ett beroende åtgärden att köra beroende aktiviteter om överordnat misslyckas. Dessutom diskuterar vi den [beroende scenarier](#dependency-scenarios) som har stöd för Batch.

## <a name="enable-task-dependencies"></a>Aktivera aktivitet beroenden
Om du vill använda aktiviteten beroenden i Batch-program, måste du först konfigurera jobbet om du vill använda aktiviteten beroenden. I Batch .NET aktivera det på din [CloudJob] [ net_cloudjob] genom att ange dess [UsesTaskDependencies] [ net_usestaskdependencies] egenskapen `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

I det föregående kodfragmentet ”batchClient” är en instans av den [BatchClient] [ net_batchclient] klass.

## <a name="create-dependent-tasks"></a>Skapa beroende aktiviteter
Du kan ange att aktiviteten ”beror på” andra uppgifter för att skapa en aktivitet som är beroende av slutförandet av en eller flera överordnade uppgifter. Batch .NET, konfigurera den [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] egenskap med en instans av den [TaskDependencies] [ net_taskdependencies] klass:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Det här kodstycket skapas en beroende aktivitet med aktivitets-ID ”blommor”. Aktiviteten ”blommor” är beroende av uppgifter ”regn” och ”Sun”. Uppgiften ”blommor” kommer att schemaläggas att köras på en beräkningsnod bara efter aktiviteter som ”regn” och ”Sun” har slutförts.

> [!NOTE]
> En aktivitet anses vara har slutförts när den är i den **slutförts** tillstånd och dess **slutkod** är `0`. I Batch .NET detta innebär en [CloudTask][net_cloudtask].[ Tillstånd] [ net_taskstate] egenskapsvärdet `Completed` och CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ExitCode] [ net_exitcode] egenskapsvärdet är `0`.
> 
> 

## <a name="dependency-scenarios"></a>Beroende scenarier
Det finns tre grundläggande uppgiften beroende scenarier som du kan använda i Azure Batch: 1, en-till-många och aktivitets-ID vara beroende. Dessa kan kombineras för att ge en fjärde scenario, många-till-många.

| scenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exempel |  |
|:---:| --- | --- |
|  [One-to-one](#one-to-one) |*taskB* beror på *taskA* <p/> *taskB* kommer inte schemaläggas för körning tills *taskA* har slutförts |![Diagram: en uppgift beroende][1] |
|  [En-till-många](#one-to-many) |*aktivitetC* är beroende av både *aktivitetA* och *aktivitetB*. <p/> *taskC* kommer inte schemaläggas för körning förrän både *taskA* och *taskB* har slutförts |![Diagram: en-till-många-uppgiften beroende][2] |
|  [ID-intervall för aktiviteten](#task-id-range) |*taskD* beror på ett antal uppgifter <p/> *taskD* kommer inte schemaläggas för körning tills uppgifter med ID: N *1* via *10* har slutförts |![Diagram: ID-intervallet sambandet][3] |

> [!TIP]
> Du kan skapa **många-till-många** relationer, till exempel där uppgifter C, D, E och F varje beroende aktiviteter A och b Detta är användbart, till exempel i paralleliserad förbearbetning scenarier där underordnade aktiviteter beror på utdata från flera överordnade uppgifter.
> 
> I exemplen i det här avsnittet är körs en beroende aktiviteten endast när de överordnade uppgifterna slutföras. Detta är standardbeteendet för beroende aktivitet. Du kan köra en beroende aktivitet när en överordnad aktivitet inte genom att ange en beroende-åtgärd om du vill åsidosätta standardbeteendet. Finns det [beroende åtgärder](#dependency-actions) information.

### <a name="one-to-one"></a>1
En aktivitet beror på slutförande av en överordnad aktivitet i en-till-en-relation. Ange en enda aktivitets-ID till för att skapa beroendet av [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] statisk metod när du fylla i den [DependsOn] [ net_dependson] -egenskapen för [CloudTask] [ net_cloudtask].

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
En aktivitet beror på slutförande av flera överordnade uppgifter i en en-till-många-relation. Om du vill skapa beroendet tillhandahåller en uppsättning aktivitets-ID för den [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] statisk metod när du fylla i den [DependsOn] [ net_dependson] -egenskapen för [CloudTask] [ net_cloudtask].

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

### <a name="task-id-range"></a>ID-intervall för aktiviteten
Ett beroende på ett intervall med överordnade uppgifter en aktivitet är beroende av slutförande av uppgifter vars ID-nummer som ligger inom ett intervall.
Ange först och sista uppgifts-ID i intervallet till för att skapa beroendet av [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] statisk metod när du fylla i den [DependsOn] [ net_dependson] -egenskapen för [CloudTask] [ net_cloudtask].

> [!IMPORTANT]
> När du använder aktiviteten ID-intervall för dina beroenden, aktivitets-ID i intervallet *måste* vara en sträng som representerar heltalsvärden.
> 
> Varje aktivitet i intervallet måste uppfylla beroendet av slutförs eller genom att fylla i med ett fel som är mappad till en beroende-åtgärder som har angetts som **Satisfy**. Finns det [beroende åtgärder](#dependency-actions) information.
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

Som standard körs en beroende aktivitet eller uppsättning aktiviteter bara när en överordnad aktivitet har slutförts. I vissa fall kanske du vill köra beroende aktiviteter även om den överordnade aktiviteten misslyckas. Du kan åsidosätta standardbeteendet genom att ange en beroende-åtgärd. En beroende åtgärd anger om en beroende aktivitet kan köras, baserat på lyckad eller misslyckad på den överordnade aktiviteten. 

Anta exempelvis att en beroende aktivitet väntar på data från den överordnade aktiviteten slutförs. Om den överordnade aktiviteten misslyckas, kan den beroende aktiviteten fortfarande att kunna köras med äldre data. I det här fallet kan en beroende-åtgärd ange att den beroende aktiviteten kan köras trots fel på den överordnade aktiviteten.

En beroende-åtgärden är baserad på ett avsluta-villkor för den överordnade aktiviteten. Du kan ange en beroende-åtgärd för någon av följande avslutningsvillkor; för .NET, finns det [ExitConditions] [ net_exitconditions] klass för ytterligare information:

- När en före bearbetning fel uppstår.
- Felet uppstår när en filöverföring. Om aktiviteten avslutas med slutkoden som angetts via **exitCodes** eller **exitCodeRanges**, och sedan möten filöverföring fel, åtgärden som angetts av slutkoden företräde.
- När aktiviteten avslutas med slutkoden definieras av den **ExitCodes** egenskapen.
- När aktiviteten avslutas med slutkoden som ligger inom ett intervall som anges av den **ExitCodeRanges** egenskapen.
- Standardfallet om aktiviteten avslutas med slutkoden inte definieras av **ExitCodes** eller **ExitCodeRanges**, eller om aktiviteten avslutas med ett fel som före bearbetning och **PreProcessingError** egenskapen inte har angetts eller om aktiviteten misslyckas med en fil överföringsfel och **FileUploadError** egenskapen har inte angetts. 

Om du vill ange en beroende-åtgärd i .NET, ange den [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] -egenskapen för villkoret för avslutning. Den **DependencyAction** egenskap tar ett av två värden:

- Ange den **DependencyAction** egenskapen **Satisfy** anger att beroende aktiviteter som kan köra om den överordnade aktiviteten avslutas med ett angivet fel.
- Ange den **DependencyAction** egenskapen **Block** anger beroende aktiviteter inte som kan köra.

Standardinställningen för den **DependencyAction** egenskapen är **Satisfy** för slutkoden 0, och **Block** för alla andra avslutningsvillkor.

I följande kod fragment anger den **DependencyAction** -egenskapen för en överordnad aktivitet. Om den överordnade aktiviteten avslutas före bearbetning fel eller med de angivna felkoderna, blockeras den beroende aktiviteten. Om den överordnade aktiviteten avslutas med ett noll-fel, är den beroende aktiviteten kan köras.

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
Den [TaskDependencies] [ github_taskdependencies] exempelprojektet är en av de [kodexempel för Azure Batch] [ github_samples] på GitHub. Visual Studio-lösning visar:

- Så här aktiverar du aktiviteten beroende av ett jobb
- Så här skapar du aktiviteter som är beroende av andra aktiviteter
- Hur du utför aktiviteterna i en pool med compute-noder.

## <a name="next-steps"></a>Nästa steg
### <a name="application-deployment"></a>Programdistribution
Den [programpaket](batch-application-packages.md) funktion i Batch ger ett enkelt sätt att både distribuera och de program som dina aktiviteter att köra datornoder version.

### <a name="installing-applications-and-staging-data"></a>Installera program och mellanlagring av data
Se [installera program och mellanlagring av data på Batch-beräkningsnoder] [ forum_post] i Azure Batch-forum för en översikt över metoderna för att förbereda din noder för att köra uppgifter. Denna post är skrivs av en av gruppmedlemmarna Azure Batch en bra introduktion på olika sätt att kopiera program, indata för aktivitet och andra filer till compute-noder.

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

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: 1-samband"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: en-till-många-beroende"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: ID-intervallet sambandet"
