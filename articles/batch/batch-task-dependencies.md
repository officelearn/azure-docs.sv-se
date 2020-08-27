---
title: Skapa aktivitets beroenden för att köra uppgifter
description: Skapa uppgifter som är beroende av slut för ande av andra uppgifter för att bearbeta MapReduce-format och liknande stor data arbets belastningar i Azure Batch.
ms.topic: how-to
ms.date: 05/22/2017
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 05b1bb289c215208be448d8ca7de144c82b313b8
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936987"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Skapa aktivitets beroenden för att köra uppgifter som är beroende av andra aktiviteter

Du kan definiera aktivitets beroenden för att köra en uppgift eller en uppsättning aktiviteter när en överordnad uppgift har slutförts. Vissa scenarier där aktivitets beroenden är användbara är:

- MapReduce-arbetsbelastningar i molnet.
- Jobb vars data bearbetnings uppgifter kan uttryckas som ett riktat acykliska diagram (DAG).
- Processer för för åter givning och efter åter givning, där varje aktivitet måste slutföras innan nästa aktivitet kan börja.
- Andra jobb där underordnade aktiviteter är beroende av utdata från överordnade aktiviteter.

Med aktivitets beroenden för batch kan du skapa aktiviteter som är schemalagda för körning på datornoderna när en eller flera överordnade aktiviteter har slutförts. Du kan till exempel skapa ett jobb som återger varje bild ruta i en 3D-film med separata, parallella aktiviteter. Den sista uppgiften – sammanfognings uppgiften--sammanfogar de åter givningar som återges i den fullständiga filmen när alla ramar har Render ATS.

Som standard schemaläggs beroende aktiviteter endast för körning efter att den överordnade aktiviteten har slutförts. Du kan ange en beroende åtgärd för att åsidosätta standard beteendet och köra aktiviteter när den överordnade aktiviteten Miss lyckas. Mer information finns i avsnittet [beroende åtgärder](#dependency-actions) .  

Du kan skapa uppgifter som är beroende av andra uppgifter i en en-till-en-till-många-relation. Du kan också skapa ett intervall beroende där en aktivitet är beroende av att en grupp av aktiviteter har slutförts inom ett angivet intervall med aktivitets-ID: n. Du kan kombinera dessa tre grundläggande scenarier för att skapa många-till-många-relationer.

## <a name="task-dependencies-with-batch-net"></a>Aktivitets beroenden med batch .NET

I den här artikeln diskuterar vi hur du konfigurerar aktivitets beroenden med hjälp av [batch .net][net_msdn] -biblioteket. Först visar vi hur du [aktiverar aktivitets beroenden](#enable-task-dependencies) för dina jobb och visar hur du [konfigurerar en aktivitet med beroenden](#create-dependent-tasks). Vi beskriver också hur du anger en beroende åtgärd för att köra beroende uppgifter om överordnat Miss lyckas. Slutligen diskuterar vi de [beroende scenarier](#dependency-scenarios) som batch stöder.

## <a name="enable-task-dependencies"></a>Aktivera aktivitets beroenden

Om du vill använda aktivitets beroenden i batch-programmet måste du först konfigurera jobbet så att det använder aktivitets beroenden. I batch .NET aktiverar du det på din [CloudJob][net_cloudjob] genom att ställa in dess [UsesTaskDependencies][net_usestaskdependencies] -egenskap till `true` :

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

I föregående kodfragment är "metoden batchclient" en instans av klassen [metoden batchclient][net_batchclient] .

## <a name="create-dependent-tasks"></a>Skapa beroende uppgifter

Om du vill skapa en uppgift som är beroende av att en eller flera överordnade aktiviteter har slutförts, kan du ange att aktiviteten är beroende av "de andra aktiviteterna. I batch .NET konfigurerar du [CloudTask][net_cloudtask]. [DependsOn][net_dependson] -egenskap med en instans av klassen [TaskDependencies][net_taskdependencies] :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Det här kodfragmentet skapar en beroende uppgift med aktivitets-ID "blommor". Uppgiften "blommor" är beroende av aktiviteterna "regn" och "Sun". Uppgiften "blommor" schemaläggs att köras på en Compute-nod först efter att aktiviteterna "regn" och "Sun" har slutförts.

> [!NOTE]
> Som standard anses en aktivitet vara slutförd när den är i **slutfört** tillstånd och dess **slut kod** är `0` . I batch .NET innebär detta en [CloudTask][net_cloudtask]. Värdet för egenskapen [State][net_taskstate] för `Completed` och CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ][net_exitcode] Värdet för egenskapen ExitCode är `0` . Information om hur du ändrar detta finns i avsnittet [beroende åtgärder](#dependency-actions) .

## <a name="dependency-scenarios"></a>Beroende scenarier

Det finns tre grundläggande scenarier för aktivitets beroenden som du kan använda i Azure Batch: ett-till-ett-till-ett-ett-till-många-och aktivitets-ID-intervall beroende. Dessa kan kombineras för att ge ett fjärde scenario, många-till-många.

| Situationen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exempel | Exemplet |
|:---:| --- | --- |
|  [En-till-en](#one-to-one) |*aktivitetb* är beroende av *uppgiften* in <p/> *aktivitetb* kommer inte att schemaläggas för körning förrän *uppgiften* i har slutförts |![Diagram: ett-till-ett-aktivitets beroende][1] |
|  [En-till-många](#one-to-many) |*aktivitetc* är beroende av både *Aktiviteta* och *aktivitetb* <p/> *aktivitetc* kommer inte att schemaläggas för körning förrän båda *aktiviteterna* och *aktivitetb* har slutförts |![Diagram: ett-till-många-aktivitets beroende][2] |
|  [Aktivitets-ID-intervall](#task-id-range) |*taskd* är beroende av en mängd aktiviteter <p/> *tasked* kommer inte att schemaläggas för körning förrän uppgifterna med ID *1* till och med *10* har slutförts |![Diagram: aktivitets-ID intervall beroende][3] |

> [!TIP]
> Du kan skapa **många-till-många-** relationer, till exempel var aktiviteterna C, D, E och F var beroende av aktiviteterna A och B. Detta är användbart, till exempel i parallella för bearbetnings scenarier där de underordnade uppgifterna är beroende av utdata från flera överordnade aktiviteter.
> 
> I exemplen i det här avsnittet körs en beroende aktivitet bara när de överordnade aktiviteterna har slutförts. Det här beteendet är standard beteendet för en beroende uppgift. Du kan köra en beroende uppgift när en överordnad aktivitet Miss lyckas genom att ange en beroende åtgärd för att åsidosätta standard beteendet. Mer information finns i avsnittet [beroende åtgärder](#dependency-actions) .

### <a name="one-to-one"></a>En-till-en

I en en-till-en-relation är en uppgift beroende av att en överordnad aktivitet har slutförts. Om du vill skapa beroendet anger du ett enda aktivitets-ID för [TaskDependencies][net_taskdependencies]. [OnId][net_onid] statisk metod när du fyller i egenskapen [DependsOn][net_dependson] för [CloudTask][net_cloudtask].

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

I en en-till-många-relation är en uppgift beroende av att flera överordnade aktiviteter har slutförts. Skapa ett beroende genom att ange en samling aktivitets-ID: n till [TaskDependencies][net_taskdependencies]. [OnIds][net_onids] statisk metod när du fyller i egenskapen [DependsOn][net_dependson] för [CloudTask][net_cloudtask].

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

En aktivitet är beroende av ett antal överordnade aktiviteter, beroende på slutförandet av aktiviteter vars ID ligger inom ett intervall.
Om du vill skapa beroendet anger du det första och sista aktivitets-ID: t i intervallet till [TaskDependencies][net_taskdependencies]. [OnIdRange][net_onidrange] statisk metod när du fyller i egenskapen [DependsOn][net_dependson] för [CloudTask][net_cloudtask].

> [!IMPORTANT]
> När du använder aktivitets-ID-intervall för dina beroenden väljs endast uppgifter med ID: n som representerar heltals värden av intervallet. Det innebär att intervallet `1..10` väljer uppgifter `3` och `7` , men inte `5flamingoes` . 
>
> Inledande nollor är inte signifikanta vid utvärdering av intervall beroenden, så uppgifter med sträng identifierare `4` , `04` och `004` kommer att vara *inom* intervallet och de kommer att behandlas som `4` en uppgift, så den första som slutföras kommer att uppfylla beroendet.
>
> Varje aktivitet i intervallet måste uppfylla beroendet, antingen genom att den slutförs eller genom att ett fel som har mappats till en beroende åtgärd angetts som **uppfylls**. Mer information finns i avsnittet [beroende åtgärder](#dependency-actions) .

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

Som standard körs en beroende uppgift eller en uppsättning aktiviteter bara efter att en överordnad aktivitet har slutförts. I vissa fall kanske du vill köra beroende uppgifter även om den överordnade aktiviteten Miss lyckas. Du kan åsidosätta standard beteendet genom att ange en beroende åtgärd. En beroende åtgärd anger om en beroende uppgift är giltig att köras, baserat på lyckad eller misslyckad aktivitet för den överordnade aktiviteten. 

Anta till exempel att en beroende uppgift väntar på data från slut för ande av den överordnade aktiviteten. Om den överordnade aktiviteten Miss lyckas kan det hända att den beroende aktiviteten fortfarande kan köras med äldre data. I det här fallet kan en beroende åtgärd ange att den beroende uppgiften är giltig att köras trots att den överordnade aktiviteten Miss lyckas.

En beroende åtgärd baseras på ett avslutnings villkor för den överordnade aktiviteten. Du kan ange en beroende åtgärd för något av följande avslutnings villkor; för .NET, se [ExitConditions][net_exitconditions] -klassen för mer information:

- När ett för bearbetnings fel inträffar.
- När ett fil överförings fel inträffar. Om aktiviteten avslutas med en slutkod som har angetts via **exitCodes** eller **exitCodeRanges**, och sedan påträffar ett fil överförings fel, har åtgärden som anges av slut koden företräde.
- När aktiviteten avslutas med en slutkod som definieras av egenskapen **ExitCodes** .
- När aktiviteten avslutas med en slutkod som ligger inom ett intervall som anges av egenskapen **ExitCodeRanges** .
- Standard fallet om aktiviteten avslutas med en slutkod som inte har definierats av **ExitCodes** eller **ExitCodeRanges**, eller om aktiviteten avslutas med ett för bearbetnings fel och egenskapen **PreProcessingError** inte har angetts, eller om aktiviteten Miss lyckas med ett fil överförings fel och **FileUploadError** -egenskapen inte har angetts. 

Ange en beroende åtgärd i .NET genom att ange [ExitOptions][net_exitoptions]. [DependencyAction][net_dependencyaction] -egenskap för avslutnings villkoret. Egenskapen **DependencyAction** använder ett av två värden:

- Att ange egenskapen **DependencyAction** för att **tillfredsställa** anger att beroende aktiviteter är berättigade att köras om den överordnade aktiviteten avslutas med ett angivet fel.
- Om du ställer in egenskapen **DependencyAction** på **block** anger det att beroende aktiviteter inte är berättigade att köras.

Standardvärdet för egenskapen **DependencyAction** **är för** slutkod 0, och **blockera** för alla andra avslutnings villkor.

I följande kodfragment anges egenskapen **DependencyAction** för en överordnad aktivitet. Om den överordnade aktiviteten avslutas med ett för bearbetnings fel, eller med de angivna fel koderna, blockeras den beroende uppgiften. Om den överordnade aktiviteten avslutas med andra fel än noll, är den beroende uppgiften giltig att köras.

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

[TaskDependencies][github_taskdependencies] -exempelprojektet är ett av [Azure Batch kod exempel][github_samples] på GitHub. Den här Visual Studio-lösningen visar:

- Så här aktiverar du aktivitets beroende för ett jobb
- Så här skapar du uppgifter som är beroende av andra aktiviteter
- Så här kör du dessa uppgifter i en pool med datornoder.

## <a name="next-steps"></a>Nästa steg

- Funktionen [programpaket](batch-application-packages.md) i batch ger ett enkelt sätt att både distribuera och version av programmen som dina aktiviteter kör på Compute-noder.
- Se [installera program och mellanlagrings data i batch Compute-noder][forum_post] i Azure Batch-forumet för en översikt över metoder för att förbereda noderna för att köra uppgifter. Det här inlägget har skrivits av någon av de Azure Batch grupp medlemmarna, och är ett utmärkt sätt att kopiera program, uppgifts data och andra filer till dina Compute-noder.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_dependson]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_exitcode]: /dotnet/api/microsoft.azure.batch.taskexecutioninformation
[net_exitconditions]: /dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: /dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: /dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: /dotnet/api/microsoft.azure.batch
[net_onid]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_onids]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_onidrange]: /dotnet/api/microsoft.azure.batch.taskdependencies
[net_taskexecutioninformation]: /dotnet/api/microsoft.azure.batch.taskexecutioninformation
[net_taskstate]: /dotnet/api/microsoft.azure.batch.common.taskstate
[net_usestaskdependencies]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_taskdependencies]: /dotnet/api/microsoft.azure.batch.taskdependencies

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: ett-till-ett-beroende"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: ett-till-många-beroende"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: aktivitets-ID intervall beroende"
