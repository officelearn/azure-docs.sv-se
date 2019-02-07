---
title: Använda aktiviteter med flera instanser för att köra MPI-program – Azure Batch | Microsoft Docs
description: Lär dig hur du kör Message Passing Interface (MPI)-program med flera instanser uppgiftstypen i Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 06/12/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 420f69e246b322eff28b56237613cea250be7530
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766562"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Använda aktiviteter med flera instanser för att köra Message Passing Interface (MPI) program i Batch

Aktiviteter med flera instanser kan du köra ett Azure Batch-aktiviteter samtidigt på flera beräkningsnoder. Dessa uppgifter gör det möjligt för databehandling scenarier som Message Passing Interface (MPI) program i Batch med höga prestanda. I den här artikeln får du lära dig hur du kör aktiviteter med flera instanser med hjälp av den [Batch .NET] [ api_net] biblioteket.

> [!NOTE]
> Exemplen i den här artikeln fokuserar på Batch .NET, MS-MPI, och Windows-beräkningsnoder, är flera instanser uppgift begrepp som beskrivs här gäller för andra plattformar och tekniker (Python och Intel MPI på Linux-noder, till exempel).
>
>

## <a name="multi-instance-task-overview"></a>Översikt över flera instanser uppgifter
I batchen, varje uppgift är normalt körs på en enda beräkningsnod--du skickar in flera aktiviteter i ett jobb och Batch-tjänsten schemalägger uppgifterna för körning på en nod. Men genom att konfigurera en uppgift **flerinstansinställningar**, du berätta för Batch för att skapa en primär aktivitet och flera underuppgifter som sedan körs på flera noder i stället.

![Översikt över flera instanser uppgifter][1]

När du skickar in en aktivitet med flera instanser för att ett jobb, utför flera steg som är unika för aktiviteter med flera instanser i Batch:

1. Batch-tjänsten skapar en **primära** och flera **underaktiviteter** baserat på inställningarna för flera instanser. Det totala antalet uppgifter (primär plus alla underaktiviteter) matchar antalet **instanser** (beräkningsnoder) du anger i inställningarna för flera instanser.
2. Batch anger en av beräkningsnoderna som den **master**, och schemalägger den primära aktiviteten ska köras i bakgrunden. Det schemalägger underaktiviteter att köra på resten av beräkningsnoder som allokeras till aktivitet med flera instanser, en underaktivitet per nod.
3. Den primära servern och alla underaktiviteter hämta någon **vanliga resursfiler** du anger i inställningarna för flera instanser.
4. Efter den vanliga resursen filer har laddats ned, primärt och underaktiviteter kör den **samordning kommandot** du anger i inställningarna för flera instanser. Kommandot samordning används vanligtvis för att förbereda noderna för att köra uppgiften. Detta kan ta med inledande Bakgrundstjänster (till exempel [Microsoft MPI][msmpi_msdn]'s `smpd.exe`) och verifiera att noderna är redo att bearbeta meddelanden mellan noder.
5. Den främsta uppgiften körs den **program kommandot** på huvudnoden *när* samordning-kommandot har slutförts genom att den primära servern och alla underaktiviteter. Program-kommandot är kommandoraden för aktiviteten flera instanser och körs bara av den primära aktiviteten. I en [MS-MPI][msmpi_msdn]-baserad lösning, det är där du kör MPI-aktiverat program med hjälp av `mpiexec.exe`.

> [!NOTE]
> Även om det är funktionellt distinkta ”flera instanser aktiviteten” är inte en unik Uppgiftstyp som den [StartTask] [ net_starttask] eller [JobPreparationTask] [ net_jobprep]. Aktivitet med flera instanser är helt enkelt en standard Batch-aktiviteter ([CloudTask] [ net_task] i Batch .NET) vars flerinstansinställningar har konfigurerats. I den här artikeln har vi refererar till detta som den **flerinstansuppgift**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Krav för aktiviteter med flera instanser
Aktiviteter med flera instanser kräver en pool med **mellan noder kommunikation aktiverat**, och med **inaktiverad för körning av samtidiga aktiviteten**. Om du vill inaktivera för körning av samtidiga aktiviteten, ange den [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) egenskapen till 1.

> [!NOTE]
> Batch [gränser](batch-quota-limit.md#other-limits) storleken på en pool med kommunikation mellan noder kommunikation aktiverat.


Det här kodstycket visar hur du skapar en pool för aktiviteter med flera instanser med hjälp av Batch .NET-biblioteket.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> Om du försöker köra en aktivitet med flera instanser i en pool kommunikation mellan inaktiverade eller med en *maxTasksPerNode* värde större än 1, aldrig schemaläggs--den finns kvar på obestämd tid i ”aktivt” tillstånd. 


### <a name="use-a-starttask-to-install-mpi"></a>Använda en StartTask för att installera MPI
För att köra MPI-program med en aktivitet med flera instanser, måste du först installera en MPI-implementering (MS-MPI eller Intel MPI, till exempel) på beräkningsnoderna i poolen. Det här är ett bra tillfälle att använda en [StartTask][net_starttask], som körs varje gång en nod ansluter till en pool eller startas. Det här kodfragmentet skapar en StartTask som anger installationspaketet för MS-MPI som en [resursfilen][net_resourcefile]. Startaktivitetens kommandorad körs efter resursfilen har laddats ner till noden. I det här fallet utför kommandoraden en obevakad installation av MS-MPI.

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
När du väljer en [RDMA-kompatibla storlek](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , till exempel A9 för beräkningsnoder i Batch-poolen MPI-program kan dra nytta av Azures höga prestanda och låg latens direkt fjärråtkomst till minne minnesåtkomst (RDMA)-nätverk.

Sök efter de storlekar som angetts som ”RDMA-stöd” i följande artiklar:

* **CloudServiceConfiguration** pooler

  * [Storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (endast Windows)
* **VirtualMachineConfiguration** pooler

  * [Storlekar för virtuella datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Storlekar för virtuella datorer i Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Dra nytta av RDMA [Linux-beräkningsnoder](batch-linux-nodes.md), måste du använda **Intel MPI** på noderna. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Skapa en aktivitet med flera instanser med Batch .NET
Nu när vi har gått igenom adresspoolkrav och MPI paketinstallation, nu ska vi skapa aktivitet med flera instanser. I det här kodfragmentet skapar vi en standard [CloudTask][net_task], konfigurerar dess [MultiInstanceSettings] [ net_multiinstance_prop] egenskapen. Som tidigare nämnts är aktiviteten med flera instanser inte en distinkt Uppgiftstyp, men en standard Batch-aktiviteter som konfigurerats med flerinstansinställningar.

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

## <a name="primary-task-and-subtasks"></a>Främsta uppgiften och underaktiviteter
När du skapar flerinstansinställningar för en aktivitet kan ange du antalet beräkningsnoder som ska köra aktiviteten. När du skickar in uppgiften till ett jobb, Batch-tjänsten skapar en **primära** uppgift och tillräckligt **underaktiviteter** som tillsammans motsvarar antalet noder som du har angett.

Dessa aktiviteter tilldelas ett id för heltal i intervallet 0 till *numberOfInstances* – 1. Uppgift med id 0 är den primära och alla andra ID: n är underaktiviteter. Till exempel om du skapar följande flerinstansinställningar för en aktivitet, den primära aktiviteten skulle ha ett id 0 och underaktiviteterna skulle ha ID: n 1 till 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Huvudnoden
När du skickar en flerinstansuppgift Batch-tjänsten anger en av beräkningsnoderna som ”master” noden och schemalägger primära aktiviteten ska köras på den överordnade noden. Underaktiviteter är schemalagda att köra på resten av noderna tilldelas aktivitet med flera instanser.

## <a name="coordination-command"></a>Samordning kommando
Den **samordning kommandot** körs av både den primära servern och underaktiviteter.

Av kommandot samordning blockerar--batchen inte körs kommandot programmet tills kommandot samordning har returnerades för alla underaktiviteter. Kommandot samordning bör därför startar alla nödvändiga Bakgrundstjänster, kontrollera att de är klara för användning och avsluta. Till exempel avslutas det här kommandot för samordning för en lösning med hjälp av MS-MPI version 7 startar tjänsten SMPD på noden och sedan:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observera användningen av `start` i det här kommandot för samordning. Detta är nödvändigt eftersom den `smpd.exe` programmet inte returnerar omedelbart efter körningen. Utan att använda den [starta] [ cmd_start] kommandot samordning kommandot returneras inte och därför skulle blockera kommandot program från att köras.

## <a name="application-command"></a>Programkommando
När den primära aktiviteten och alla underaktiviteter är klar med kommandot samordning, flera instanser aktivitetens kommandorad körs av aktiviteten primära *endast*. Vi kallar detta den **program kommandot** att skilja den från kommandot samordning.

För MS-MPI-program, använder du kommandot program för att köra dina MPI-aktiverat program med `mpiexec.exe`. Här är till exempel ett programkommando för en lösning med hjälp av MS-MPI version 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Eftersom MS-MPI `mpiexec.exe` använder den `CCP_NODES` variabeln som standard (se [miljövariabler](#environment-variables)) exempel programmet från kommandoraden ovan omfattar inte den.
>
>

## <a name="environment-variables"></a>Miljövariabler
Batch skapar flera [miljövariabler] [ msdn_env_var] specifika för aktiviteter med flera instanser på de beräkningsnoder som allokeras till en aktivitet med flera instanser. Din kommandorader samordning och program kan referera till dessa miljövariabler som kan göra skript och program som de kör.

Följande miljövariabler skapas av Batch-tjänsten för användning av aktiviteter med flera instanser:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Fullständig information om dessa och andra Batch beräkna nodmiljövariabler, inklusive innehållet och synlighet, finns i [beräkna nodmiljövariabler][msdn_env_var].

> [!TIP]
> Batch Linux MPI-kodexempel innehåller ett exempel på hur flera av dessa miljövariabler kan användas. Den [samordning cmd] [ coord_cmd_example] Bash-skript hämtar vanliga program och indata-filer från Azure Storage, aktiverar en resurs för Network File System (NFS) på den överordnade noden och konfigurerar de andra noderna allokeras till aktivitet med flera instanser som NFS-klienter.
>
>

## <a name="resource-files"></a>Resursfiler
Det finns två uppsättningar resursfiler att tänka på för aktiviteter med flera instanser: **vanliga resursfiler** som *alla* aktiviteterna hämtar (både primära servern och underaktiviteter), och **resursfiler** angetts för flera instanser uppgift, vilket *bara primärt* uppgift nedladdningar.

Du kan ange en eller flera **vanliga resursfiler** i inställningarna för flera instanser för en aktivitet. Dessa vanliga resursfiler laddas ned från [Azure Storage](../storage/common/storage-introduction.md) till varje nod **delade aktivitetskatalogen** av den primära servern och alla underaktiviteter. Du kan komma åt den delade katalogen från program och koordination kommandorader med hjälp av den `AZ_BATCH_TASK_SHARED_DIR` miljövariabeln. Den `AZ_BATCH_TASK_SHARED_DIR` sökvägen är identiska på alla noder som allokerats till aktivitet med flera instanser, så du kan dela ett enda samordning kommando mellan den primära servern och alla underaktiviteter. Batch ”delar inte” katalogen i en mening för fjärråtkomst, men du kan använda den som en monteringspunkt eller dela punkt som nämnts tidigare i tips på miljövariabler.

Resursfiler som du anger för flerinstansuppgift själva laddas ned till aktivitetens arbetskatalog, `AZ_BATCH_TASK_WORKING_DIR`, som standard. Som tidigare nämnts, till skillnad från vanliga resursfiler hämtar endast primär aktiviteten resursfiler som angetts för aktiviteten flera instanser.

> [!IMPORTANT]
> Använd alltid miljövariablerna `AZ_BATCH_TASK_SHARED_DIR` och `AZ_BATCH_TASK_WORKING_DIR` att referera till dessa kataloger i din kommandorader. Försök inte att konstruera sökvägarna manuellt.
>
>

## <a name="task-lifetime"></a>Aktiviteternas livslängd
Livslängden för den primära aktiviteten styr livslängden för hela flerinstansuppgift. När den primära databasen avslutas avslutas alla underaktiviteter. Slutkoden för primärt är slutkoden för aktiviteten och därför används för att fastställa har lyckats eller misslyckats för uppgiften för återförsök.

Om någon av underaktiviteterna misslyckas avslutas koden inte är noll, till exempel misslyckas hela flerinstansuppgift. Aktivitet med flera instanser är sedan avslutas och göras upp till sin gräns för återförsök.

När du tar bort en flerinstansuppgift raderas även den primära servern och alla underaktiviteter av Batch-tjänsten. Alla underuppgift kataloger och deras filer tas bort från beräkningsnoder, precis som vid en uppgift som standard.

[TaskConstraints] [ net_taskconstraints] för en aktivitet med flera instanser, till exempel den [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime] [ net_taskconstraint_maxwallclock], och [RetentionTime] [ net_taskconstraint_retention] egenskaper, respekteras när de är för en standard som gäller för den primära servern och alla underaktiviteter. Men om du ändrar den [RetentionTime] [ net_taskconstraint_retention] egenskapen när du lägger till aktivitet med flera instanser för jobbet, den här ändringen tillämpas endast på den primära aktiviteten. Alla underaktiviteter fortsätta att använda ursprungligt [RetentionTime][net_taskconstraint_retention].

En beräkningsnod senaste uppgiftslista visar id för en underaktivitet om den senaste aktiviteten var en del av en aktivitet med flera instanser.

## <a name="obtain-information-about-subtasks"></a>Hämta information om underaktiviteter
Du kan hämta information om underaktiviteter med hjälp av Batch .NET-biblioteket genom att anropa den [CloudTask.ListSubtasks] [ net_task_listsubtasks] metod. Den här metoden returnerar information om alla underaktiviteter och information om Beräkningsnoden som körs av aktiviteterna. Från den här informationen kan bestämma du rotkatalogen för varje underaktivitet, pool-id, det aktuella tillståndet, slutkoden och mer. Du kan använda den här informationen i kombination med den [PoolOperations.GetNodeFile] [ poolops_getnodefile] metod för att hämta den underaktivitet filer. Observera att den här metoden returnerar information om den primära aktiviteten (id 0).

> [!NOTE]
> Om inte annat anges, Batch .NET-metoder som fungerar på flera instanser [CloudTask] [ net_task] själva tillämpa *endast* till den primära aktiviteten. Till exempel när du anropar den [CloudTask.ListNodeFiles] [ net_task_listnodefiles] metod på en aktivitet med flera instanser, returneras bara den primära aktiviteten filer.
>
>

Följande kodfragment visar hur du hämta underaktivitet information, samt begära filinnehållet från noder som de körs.

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
1. Följ de två första stegen i [kompilera och köra en enkel MS-MPI-program][msmpi_howto]. Det här uppfyller kraven för följande steg.
2. Skapa en *versionen* version av den [MPIHelloWorld] [ helloworld_proj] MPI exempelprogrammet. Detta är det program som körs på beräkningsnoder av aktivitet med flera instanser.
3. Skapa en zip-fil som `MPIHelloWorld.exe` (som du skapat steg 2) och `MSMpiSetup.exe` (som du har hämtat steg 1). Du kan ladda upp zip-fil som ett programpaket i nästa steg.
4. Använd den [Azure-portalen] [ portal] att skapa en Batch [program](batch-application-packages.md) kallas ”MPIHelloWorld” och ange zip-filen som du skapade i föregående steg som versionen ”1.0” av den programpaket. Se [ladda upp och hantera program](batch-application-packages.md#upload-and-manage-applications) för mer information.

> [!TIP]
> Skapa en *versionen* version av `MPIHelloWorld.exe` så att du inte behöver inkludera eventuella ytterligare beroenden (till exempel `msvcp140d.dll` eller `vcruntime140d.dll`) i programpaketet.
>
>

### <a name="execution"></a>Körnings-
1. Ladda ned den [azure-batch-samples] [ github_samples_zip] från GitHub.
2. Öppna MultiInstanceTasks **lösning** i Visual Studio 2017. Den `MultiInstanceTasks.sln` lösningsfilen finns i:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Ange dina autentiseringsuppgifter för Batch och Storage-konto i `AccountSettings.settings` i den **Microsoft.Azure.Batch.Samples.Common** projekt.
4. **Skapa och köra** MultiInstanceTasks lösningen att köra MPI exempelprogrammet på beräkningsnoder i en Batch-pool.
5. *Valfritt*: Använd den [Azure-portalen] [ portal] eller [Batch Explorer] [ batch_labs] att undersöka de exempelpool, jobb och uppgift (”MultiInstanceSamplePool” ”, MultiInstanceSampleJob ”,” MultiInstanceSampleTask ”) innan du tar bort resurserna.

> [!TIP]
> Du kan ladda ned [Visual Studio Community] [ visual_studio] kostnadsfritt om du inte har Visual Studio.
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
* Blogg för Microsoft HPC och Azure Batch-teamet beskriver [MPI stöd för Linux på Azure Batch][blog_mpi_linux], och innehåller information om hur du använder [OpenFOAM] [ openfoam] med Batch. Du kan hitta Python-kodexempel för den [OpenFOAM exempel på GitHub][github_mpi].
* Lär dig hur du [skapa pooler med beräkningsnoder för Linux](batch-linux-nodes.md) för användning i dina Azure Batch MPI-lösningar.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
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
