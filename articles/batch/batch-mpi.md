---
title: "Använda flera instanser uppgifter för att köra program MPI - Azure Batch | Microsoft Docs"
description: "Lär dig mer om att köra Message Passing Interface (MPI)-program med flera instanser aktivitetstypen i Azure Batch."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: 5/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01da017587aed7c0f2415786fdcbf6f64024cbe3
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Använda flera instanser aktiviteter för att köra program för Message Passing Interface (MPI) i Batch

Flera instanser uppgifter kan du köra en Azure Batch-uppgiften på flera compute-noder samtidigt. Dessa uppgifter gör det möjligt för höga prestanda scenarier som program för Message Passing Interface (MPI) i gruppen. I den här artikeln beskrivs hur du kan köra flera instanser aktiviteter med hjälp av den [Batch .NET] [ api_net] bibliotek.

> [!NOTE]
> Exemplen i den här artikeln fokuserar på Batch .NET, MS-MPI och Windows compute-noder, gäller flera instanser uppgiften begrepp som beskrivs här för andra plattformar och tekniker (Python och Intel MPI för Linux-noder, till exempel).
>
>

## <a name="multi-instance-task-overview"></a>Översikt över uppgifter för flera instanser
I batchen, varje uppgift är normalt körs på en enda beräkningsnod--du skickar in flera aktiviteter till ett jobb, och scheman varje aktivitet för körning på en nod för Batch-tjänsten. Men genom att konfigurera en aktivitet **inställningar för flera instanser**, anger du Batch för att skapa en primär aktivitet och flera underaktiviteter som sedan körs på flera noder i stället.

![Översikt över uppgifter för flera instanser][1]

När du skickar en aktivitet med flera instanser för att ett jobb utför Batch flera steg unika till flera instanser uppgifter:

1. Batch-tjänsten skapar en **primära** och flera **underaktiviteter** baserat på inställningarna för flera instanser. Det totala antalet aktiviteter (primära plus alla underaktiviteter) stämmer med antalet **instanser** (datornoder) du anger i inställningarna för flera instanser.
2. Batch anger en av compute-noder som den **master**, och scheman för den primära aktiviteten ska köras i bakgrunden. Den schemaläggs underaktiviteter ska köras på resten av compute-noder som tilldelats aktiviteten med flera instanser, en delaktivitet per nod.
3. Den primära servern och alla underaktiviteter hämta någon **vanliga resursfiler** du anger i inställningarna för flera instanser.
4. Efter den vanliga resursen filer har laddats ned, primärt och underaktiviteter kör den **samordning kommandot** du anger i inställningarna för flera instanser. Kommandot samordning används vanligtvis för att förbereda noder för att köra uppgiften. Detta kan inkludera startar Bakgrundstjänster (exempelvis [Microsoft MPI][msmpi_msdn]'s `smpd.exe`) och verifiera att noderna är redo att bearbeta meddelanden mellan noder.
5. Den främsta uppgiften körs den **kommandot programmet** på huvudnoden *när* samordning-kommandot har slutförts som den primära servern och alla underaktiviteter. Kommandot program är kommandoraden för aktiviteten med flera instanser och utförs endast för den primära aktiviteten. I en [MS-MPI][msmpi_msdn]-baserad lösning, det är där du kör MPI-aktiverade appen med `mpiexec.exe`.

> [!NOTE]
> Även om det är funktionellt distinkta ”flera instanser aktiviteten” är inte en unik Uppgiftstyp som den [startuppgift har ställts] [ net_starttask] eller [JobPreparationTask] [ net_jobprep]. Aktiviteten med flera instanser är helt enkelt en standard batchaktiviteten ([CloudTask] [ net_task] i Batch .NET) vars flera instanser inställningar har konfigurerats. I den här artikeln vi refererar till detta som den **flera instanser uppgiften**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Krav för flera instanser uppgifter
Flera instanser uppgifter kräver en pool med **kommunikationen mellan noder aktiverat**, och med **samtidiga uppgiftskörningen inaktiveras**. Om du vill inaktivera samtidiga uppgiftskörningen, ange den [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool#Microsoft_Azure_Batch_CloudPool_MaxTasksPerComputeNode) egenskap till 1.

Det här kodstycket visar hur du skapar en pool för flera instanser aktiviteter med hjälp av Batch .NET-bibliotek.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Om du försöker köra en aktivitet för flera instanser i en pool med dessa kommunikation har inaktiverats, eller med en *maxTasksPerNode* värde större än 1, aldrig schemaläggs--på obestämd tid förblir det ”aktiv”. 
>
> Flera instanser uppgifter kan köra endast på noder i pooler som skapats efter 14 December 2015.
>
>

### <a name="use-a-starttask-to-install-mpi"></a>Använd en startuppgift har ställts för att installera MPI
För att köra MPI program med flera instanser uppgiften, måste du först installera en MPI-implementering (MS-MPI eller Intel MPI, till exempel) på beräkningsnoder i poolen. Detta är ett bra tillfälle att använda en [startuppgift har ställts][net_starttask], som körs varje gång en nod ansluter till en pool eller startas. Det här kodstycket skapar en startuppgift har ställts som anger installationspaketet för MS-MPI som en [resursfilen][net_resourcefile]. Aktiviteten starta kommandoraden körs efter resursfilen laddas ned till noden. I det här fallet utför kommandoraden en obevakad installation av MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Direktåtkomst till fjärrminne (RDMA)
När du väljer en [RDMA-kompatibla storlek](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , till exempel A9 för beräkningsnoder i Batch-pool MPI-program kan dra nytta av nätverk för Azures hög prestanda, låg latens direkt fjärrminne access (RDMA).

Leta efter de storlekar som angetts som ”RDMA-kompatibla” i följande artiklar:

* **CloudServiceConfiguration** pooler

  * [Storlekar för molntjänster](../cloud-services/cloud-services-sizes-specs.md) (endast Windows)
* **VirtualMachineConfiguration** pooler

  * [Storlekar för virtuella datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Storlekar för virtuella datorer i Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Dra nytta av RDMA [Linux datornoder](batch-linux-nodes.md), måste du använda **Intel MPI** på noderna. Mer information om CloudServiceConfiguration och VirtualMachineConfiguration pooler finns i avsnittet Pool av den [Batch funktionsöversikt](batch-api-basics.md).
>
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Skapa en flerinstans-uppgift med Batch .NET
Nu när vi har omfattas poolen kraven och MPI installationen kan vi skapa aktiviteten med flera instanser. I det här kodstycket vi skapa en standard [CloudTask][net_task], konfigurera dess [MultiInstanceSettings] [ net_multiinstance_prop] egenskapen. Som tidigare nämnts kan flera instanser aktiviteten är inte en distinkta aktivitetstyp, men en standard Batch-aktivitet som konfigurerats med inställningar för flera instanser.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Primära aktiviteten och underaktiviteterna
När du skapar flera instansinställningarna för en aktivitet kan ange du antalet compute-noder som ska köra aktiviteten. När du skickar aktiviteten till ett jobb, Batch-tjänsten skapar en **primära** aktiviteten och det finns tillräckligt med **underaktiviteter** som tillsammans motsvarar antalet noder som du angav.

Dessa aktiviteter tilldelas ett id för heltal i intervallet 0 till *numberOfInstances* - 1. Uppgift med id 0 är den primära och alla andra ID: n är underaktiviteter. Till exempel om du skapar följande inställningar för flera instanser för en aktivitet, främsta uppgiften skulle ha ett id 0 och underaktiviteterna skulle ha ID 1 till 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Huvudnod
När du skickar en aktivitet med flera instanser, Batch-tjänsten anger en datornoderna som ”” huvudnoden och schemalägger primära aktiviteten ska köras på huvudnoden. Underaktiviteter är schemalagda att köras på resten av noderna tilldelas aktiviteten med flera instanser.

## <a name="coordination-command"></a>Samordning kommando
Den **samordning kommandot** körs av både den primära servern och underaktiviteter.

Anrop av kommandot samordning blockerar--batchen inte körs kommandot programmet tills kommandot samordning har returnerat har på alla underaktiviteter. Kommandot samordning bör därför startar alla nödvändiga Bakgrundstjänster, kontrollera att de är klara för användning och avsluta. Till exempel avslutas kommandot samordning för en lösning med hjälp av MS-MPI version 7 startar tjänsten SMPD på noden, sedan:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observera användningen av `start` i det här kommandot samordning. Detta är nödvändigt eftersom den `smpd.exe` program inte returnerar omedelbart efter körning. Utan att använda den [starta] [ cmd_start] kommandot samordning kommandot returnerar inte och därför skulle blockera kommandot program från att köras.

## <a name="application-command"></a>Kommandot programmet
När den primära aktiviteten och alla underaktiviteter är klar med kommandot samordning, körs kommandoraden för aktiviteten med flera instanser av den primära aktiviteten *endast*. Vi kallar detta den **kommandot programmet** att skilja den från kommandot samordning.

För MS-MPI program kan använda kommandot program för att köra ditt MPI-aktiverade program med `mpiexec.exe`. Här är till exempel ett programkommando för en lösning med hjälp av MS-MPI version 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Eftersom MS-MPI `mpiexec.exe` använder den `CCP_NODES` variabeln som standard (se [miljövariabler](#environment-variables)) exempel programmet kommandoraden ovan utesluter den.
>
>

## <a name="environment-variables"></a>Miljövariabler
Batch skapar flera [miljövariabler] [ msdn_env_var] specifika för flera instanser uppgifter på datornoderna som allokerats till en aktivitet med flera instanser. Din kommandorader samordning och program kan referera till dessa miljövariabler som kan skript och program som de körs.

Följande miljövariabler skapas av Batch-tjänsten för användning av flera instanser uppgifter:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Fullständig information om dessa och andra batchen compute-nod miljövariabler, inklusive innehållet och synlighet finns [Compute-nod miljövariabler][msdn_env_var].

> [!TIP]
> Batch Linux MPI kodexemplet innehåller ett exempel på hur många av de här miljövariablerna kan användas. Den [samordning cmd] [ coord_cmd_example] Bash-skript hämtar vanlig tillämpning och indata-filer från Azure Storage, aktiverar en Network File System (NFS) nätverksresurs på huvudnoden och konfigurerar de andra noderna allokeras till aktiviteten med flera instanser som NFS-klienterna.
>
>

## <a name="resource-files"></a>Resursfiler
Det finns två uppsättningar med resursfiler väga in för flera instanser uppgifter: **vanliga resursfiler** som *alla* uppgifter hämta (både primär och underaktiviteter), och **resursfiler** angetts för flera instanser uppgift sig själv, vilket *endast primärt* uppgift hämtningar.

Du kan ange en eller flera **vanliga resursfiler** i inställningarna för flera instanser för en aktivitet. Dessa vanliga resursfiler hämtas från [Azure Storage](../storage/common/storage-introduction.md) på varje nod **aktivitet delade katalogen** av den primära servern och alla underaktiviteter. Du kan komma åt den delade katalogen aktivitet från program och samordning kommandorader med hjälp av den `AZ_BATCH_TASK_SHARED_DIR` miljövariabeln. Den `AZ_BATCH_TASK_SHARED_DIR` sökvägen är identiska på alla noder som tilldelats aktiviteten med flera instanser, därför kan du dela ett enda samordning kommando mellan den primära servern och alla underaktiviteter. Batch ”delar inte” katalogen i en mening för fjärråtkomst, men du kan använda den som en monteringspunkt eller dela punkt som nämnts tidigare i tips på miljövariabler.

Resursfiler som du anger för aktiviteten med flera instanser hämtas till aktivitetens arbetskatalogen `AZ_BATCH_TASK_WORKING_DIR`, som standard. Som tidigare nämnts, till skillnad från vanliga resursfiler hämtar bara den primära aktiviteten resursfiler som angetts för aktiviteten med flera instanser.

> [!IMPORTANT]
> Använd alltid miljövariablerna `AZ_BATCH_TASK_SHARED_DIR` och `AZ_BATCH_TASK_WORKING_DIR` att referera till de här katalogerna i din kommandorader. Försök inte att konstruera sökvägarna manuellt.
>
>

## <a name="task-lifetime"></a>Livslängd för aktiviteten
Livslängden för den primära aktiviteten styr livslängden för aktiviteten hela flera instanser. När primärt avslutas avslutas alla underaktiviteter. Slutkoden för primärt är slutkoden för aktiviteten och därför används för att avgöra lyckats eller misslyckats i aktiviteten för försök igen.

Om någon av underaktiviteterna inte avslutas med koden inte är noll, till exempel hela flera instanser aktiviteten misslyckas. Aktiviteten med flera instanser sedan avslutas och ett nytt försök, upp till gränsen för återförsök.

När du tar bort en aktivitet med flera instanser bort den primära servern och alla underaktiviteter också av Batch-tjänsten. Alla underaktivitet kataloger och filer tas bort från datornoderna, precis som för en aktivitet som standard.

[TaskConstraints] [ net_taskconstraints] för en aktivitet med flera instanser, som den [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], och [RetentionTime] [ net_taskconstraint_retention] gäller egenskaper, som de är för en aktivitet som standard och tillämpas på den primära servern och alla underaktiviteter. Men om du ändrar den [RetentionTime] [ net_taskconstraint_retention] egenskapen när du lägger till flera instanser uppgiften i jobbet ändringen tillämpas endast på den primära aktiviteten. Alla underaktiviteter fortsätta att använda ursprungligt [RetentionTime][net_taskconstraint_retention].

En beräkningsnod senaste uppgiftslista visar id för en delaktivitet om den senaste aktiviteten var en del av en aktivitet med flera instanser.

## <a name="obtain-information-about-subtasks"></a>Hämta information om underaktiviteter
Om du vill få information om underaktiviteter genom att använda Batch .NET-bibliotek, anropa den [CloudTask.ListSubtasks] [ net_task_listsubtasks] metod. Den här metoden returnerar information om alla underaktiviteter och information om Beräkningsnoden som körts uppgifterna. Från den här informationen kan bestämma du rotkatalogen för varje underaktivitet, pool-id, det aktuella tillståndet, slutkod med mera. Du kan använda den här informationen i kombination med den [PoolOperations.GetNodeFile] [ poolops_getnodefile] metod för att hämta den underaktivitet filer. Observera att den här metoden inte returnera information för den primära aktiviteten (id 0).

> [!NOTE]
> Om inte annat anges Batch .NET-metoder som fungerar på flera instanser [CloudTask] [ net_task] själva gäller *endast* till den primära aktiviteten. Till exempel när du anropar den [CloudTask.ListNodeFiles] [ net_task_listnodefiles] metoden för en uppgift med flera instanser bara den primära aktiviteten filer som ska returneras.
>
>

Följande kodavsnitt visar hur du hämtar information om underaktiviteter som begär innehållet från noder som de körs.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Kodexempel
Den [MultiInstanceTasks] [ github_mpi] kodexempel på GitHub visar hur du använder en aktivitet med flera instanser för att köra en [MS-MPI] [ msmpi_msdn] på Batch-beräkningsnoder. Följ stegen i [förberedelse](#preparation) och [körning](#execution) att köra exemplet.

### <a name="preparation"></a>Förberedelse
1. Följ de två första stegen i [så att kompilera och köra ett enkelt program MS-MPI][msmpi_howto]. Det här uppfyller kraven för följande steg.
2. Skapa en *versionen* version av den [MPIHelloWorld] [ helloworld_proj] MPI exempelprogrammet. Detta är det program som körs på datornoderna av aktiviteten med flera instanser.
3. Skapa en zip-filen med `MPIHelloWorld.exe` (som du skapat i steg 2) och `MSMpiSetup.exe` (som du hämtade i steg 1). Du måste ladda upp zip-fil som ett programpaket i nästa steg.
4. Använd den [Azure-portalen] [ portal] att skapa en Batch [programmet](batch-application-packages.md) kallas ”MPIHelloWorld” och ange zip-filen som du skapade i föregående steg versionsnummer ”1.0” i programpaket. Se [ladda upp och hantera program](batch-application-packages.md#upload-and-manage-applications) för mer information.

> [!TIP]
> Skapa en *versionen* version av `MPIHelloWorld.exe` så att du inte inkludera eventuella ytterligare beroenden (till exempel `msvcp140d.dll` eller `vcruntime140d.dll`) i programpaketet.
>
>

### <a name="execution"></a>Körning
1. Hämta den [azure-batch-samples] [ github_samples_zip] från GitHub.
2. Öppna MultiInstanceTasks **lösning** i Visual Studio 2015 eller senare. Den `MultiInstanceTasks.sln` lösningsfilen finns:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Ange Batch- och autentiseringsuppgifterna för ditt konto i `AccountSettings.settings` i den **Microsoft.Azure.Batch.Samples.Common** projekt.
4. **Skapa och köra** MultiInstanceTasks-lösningen för att köra MPI exempelprogram på compute-noder i en Batch-pool.
5. *Valfria*: Använd den [Azure-portalen] [ portal] eller [BatchLabs] [ batch_labs] att undersöka exempel pool, jobb och aktivitet (” MultiInstanceSamplePool ”,” MultiInstanceSampleJob ”,” MultiInstanceSampleTask ”) innan du tar bort resurserna.

> [!TIP]
> Du kan hämta [Visual Studio Community] [ visual_studio] kostnadsfritt om du inte har Visual Studio.
>
>

Utdata från `MultiInstanceTasks.exe` liknar följande:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Nästa steg
* Microsoft HPC & Azure Batch-teamets blogg beskrivs [MPI stöd för Linux på Azure Batch][blog_mpi_linux], och innehåller information om hur du använder [OpenFOAM] [ openfoam] med Batch. Du kan hitta Python kodexempel för den [OpenFOAM exempel på GitHub][github_mpi].
* Lär dig hur du [skapa pooler med Linux datornoderna](batch-linux-nodes.md) för användning i Azure Batch MPI-lösningar.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Översikt över flera instanser"
