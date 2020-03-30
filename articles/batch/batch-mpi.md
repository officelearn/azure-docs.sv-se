---
title: Använd multiinstansuppgifter för att köra MPI-program - Azure Batch | Microsoft-dokument
description: Lär dig hur du kör MPI-program (Message Passing Interface) med aktivitetstypen multi-instance i Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 03/13/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1896fea3c401299b4f77235ab3c02d85708b7041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023675"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Använda multiinstansuppgifter för att köra MPI-program (Message Passing Interface) i Batch

Med multiinstansuppgifter kan du köra en Azure Batch-uppgift på flera beräkningsnoder samtidigt. Dessa uppgifter möjliggör högpresterande datorscenarier som MPI-program (Message Passing Interface) i Batch. I den här artikeln får du lära dig hur du kör multi-instance-uppgifter med batch [.NET-biblioteket.][api_net]

> [!NOTE]
> Medan exemplen i den här artikeln fokuserar på batch-, MS-MPI- och Windows-beräkningsnoder, gäller de aktivitetskoncept för flera instanser som beskrivs här för andra plattformar och tekniker (python och Intel MPI på Linux-noder, till exempel).
>
>

## <a name="multi-instance-task-overview"></a>Aktivitetsöversikt för flera instanser
I Batch körs varje aktivitet normalt på en enda beräkningsnod – du skickar flera aktiviteter till ett jobb och batch-tjänsten schemalägger varje aktivitet för körning på en nod. Genom att konfigurera en aktivitets **flerinstansinställningar**ber du batchen att i stället skapa en primär uppgift och flera underaktiviteter som sedan körs på flera noder.

![Aktivitetsöversikt för flera instanser][1]

När du skickar en aktivitet med inställningar för flera instanser till ett jobb utför Batch flera steg som är unika för uppgifter med flera instanser:

1. Batch-tjänsten skapar en **primär** och flera **underaktiviteter** baserat på inställningarna för flera instanser. Det totala antalet aktiviteter (primära plus alla underaktiviteter) matchar antalet **instanser (beräkningsnoder)** som du anger i inställningarna för flera instanser.
2. Batch anger en av beräkningsnoderna som **huvudsida**och schemalägger den primära aktiviteten som ska köras på huvudhanteraren. Den schemalägger underaktiviteterna så att de körs på resten av beräkningsnoderna som allokerats till multi-instance-aktiviteten, en undertas per nod.
3. De primära och alla underaktiviteter hämtar alla **vanliga resursfiler** som du anger i inställningarna för flera instanser.
4. När de vanliga resursfilerna har hämtats kör det primära och underaktiviteter det **samordningskommando** som du anger i inställningarna för flera instanser. Samordningskommandot används vanligtvis för att förbereda noder för att utföra uppgiften. Detta kan omfatta start av bakgrundstjänster (till exempel Microsoft `smpd.exe` [MPI)][msmpi_msdn]och verifiera att noderna är redo att bearbeta meddelanden mellan noder.
5. Den primära uppgiften kör **programkommandot** på huvudnoden *när* samordningskommandot har slutförts av primärt och alla underaktiviteter. Programkommandot är kommandoraden för själva multi-instance-aktiviteten och körs endast av den primära aktiviteten. I en [MS-MPI-baserad][msmpi_msdn]lösning kör du det MPI-aktiverade programmet med `mpiexec.exe`.

> [!NOTE]
> Även om det är funktionellt distinkt, är "multi-instans uppgift" inte en unik uppgift typ som [StartTask][net_starttask] eller [JobPreparationTask][net_jobprep]. Multi-instance-aktiviteten är helt enkelt en standardbatchuppgift[(CloudTask][net_task] i Batch .NET) vars flerinstansinställningar har konfigurerats. I den här artikeln hänvisar vi till detta som **multi-instance uppgift**.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>Krav för multi-instance-uppgifter
Multi-instance-uppgifter kräver en pool med **kommunikation mellan nod aktiverat**och samtidig körning av uppgifter **inaktiverat**. Om du vill inaktivera samtidig körning av uppgifter anger du egenskapen [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) till 1.

> [!NOTE]
> Batch [begränsar](batch-quota-limit.md#pool-size-limits) storleken på en pool som har kommunikation mellan nod aktiverat.


Det här kodavsnittet visar hur du skapar en pool för multiinstansuppgifter med batch-NET-biblioteket.

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
> Om du försöker köra en aktivitet med flera instanser i en pool med internodekommunikation inaktiverad, eller med ett *maxTasksPerNode-värde* som är större än 1, är aktiviteten aldrig schemalagd – den förblir på obestämd tid i "aktivt" tillstånd. 


### <a name="use-a-starttask-to-install-mpi"></a>Använda en StartTask för att installera MPI
Om du vill köra MPI-program med en multi-instance-uppgift måste du först installera en MPI-implementering (MS-MPI eller Intel MPI, till exempel) på beräkningsnoderna i poolen. Det här är ett bra tillfälle att använda en [StartTask][net_starttask], som körs när en nod ansluter till en pool eller startas om. Det här kodavsnittet skapar en StartTask som anger MS-MPI-installationspaketet som en [resursfil][net_resourcefile]. Startaktivitetens kommandorad körs när resursfilen har hämtats till noden. I det här fallet utför kommandoraden en obevakad installation av MS-MPI.

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

### <a name="remote-direct-memory-access-rdma"></a>Fjärråtkomst till direkt minne (RDMA)
När du väljer en [RDMA-kompatibel storlek,](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) till exempel A9 för beräkningsnoderna i batch-poolen, kan mpi-programmet dra nytta av Azures högpresterande RDMA-nätverk (Low-lateency Remote Memory Access).

Leta efter de storlekar som anges som "RDMA-kan" i följande artiklar:

* **Pooler för CloudServiceKonfigurering**

  * [Storlekar för molntjänster](../cloud-services/cloud-services-sizes-specs.md) (endast Windows)
* **VirtualMachineKonfigureringspooler**

  * [Storlekar för virtuella datorer i Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux)
  * [Storlekar för virtuella datorer i Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows)

> [!NOTE]
> Om du vill dra nytta av RDMA på [Linux-beräkningsnoder](batch-linux-nodes.md)måste du använda **Intel MPI** på noderna. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Skapa en aktivitet med flera instanser med Batch .NET
Nu när vi har täckt poolkraven och MPI-paketinstallationen ska vi skapa multi-instance-uppgiften. I det här kodavsnittet skapar vi en standard [CloudTask][net_task]och konfigurerar sedan egenskapen [MultiInstanceSettings.][net_multiinstance_prop] Som tidigare nämnts är multi-instance-aktiviteten inte en distinkt aktivitetstyp, utan en standardbatchuppgift som konfigurerats med inställningar för flera instanser.

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

## <a name="primary-task-and-subtasks"></a>Primär uppgift och underaktiviteter
När du skapar flerinstansinställningarna för en aktivitet anger du hur många beräkningsnoder som ska utföras av aktiviteten. När du skickar aktiviteten till ett jobb skapar tjänsten Batch en **primär** uppgift och tillräckligt många **underaktiviteter** som tillsammans matchar antalet noder som du har angett.

Dessa aktiviteter tilldelas ett heltals-ID i intervallet 0 till *numberOfInstances* - 1. Aktiviteten med id 0 är den primära aktiviteten och alla andra id:er är underaktiviteter. Om du till exempel skapar följande inställningar för flera instanser för en aktivitet skulle den primära uppgiften ha ett id på 0 och underaktiviteterna skulle ha id 1 till 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Huvudnod
När du skickar en aktivitet med flera instanser anger batch-tjänsten en av beräkningsnoderna som "huvudnod" och schemalägger den primära aktiviteten som ska köras på huvudnoden. Underaktiviteterna är schemalagda att köras på resten av noderna som tilldelats aktiviteten med flera instanser.

## <a name="coordination-command"></a>Kommandot Samordning
**Samordningskommandot** körs av både primär- och underaktiviteter.

Anrop av samordningskommandot blockerar - Batch kör inte programkommandot förrän samordningskommandot har returnerats för alla underaktiviteter. Samordningskommandot bör därför starta alla nödvändiga bakgrundstjänster, kontrollera att de är klara att användas och sedan avsluta. Det här samordningskommandot för en lösning som använder MS-MPI version 7 startar SMPD-tjänsten på noden och avslutar sedan:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observera användningen `start` av i detta samordningskommando. Detta krävs eftersom `smpd.exe` programmet inte returneras omedelbart efter körningen. Utan att använda [startkommandot][cmd_start] skulle det här samordningskommandot inte returneras och skulle därför blockera programkommandot från att köras.

## <a name="application-command"></a>Program, kommando
När den primära uppgiften och alla underaktiviteter har körts har utfört samordningskommandot körs kommandoraden för flera instanser endast av den primära *uppgiften*. Vi kallar detta **programmet kommandot** för att skilja den från samordning kommandot.

För MS-MPI-program använder du programkommandot för att `mpiexec.exe`köra ditt MPI-aktiverade program med . Här är till exempel ett programkommando för en lösning som använder MS-MPI version 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> Eftersom MS-MPI `mpiexec.exe` använder `CCP_NODES` variabeln som standard (se [Miljövariabler)](#environment-variables)utesluter exempelprogrammets kommandorad ovan den.
>
>

## <a name="environment-variables"></a>Miljövariabler
Batch skapar flera [miljövariabler][msdn_env_var] som är specifika för multiinstansaktiviteter på beräkningsnoderna som allokerats till en aktivitet med flera instanser. Dina samordnings- och programkommandorader kan referera till dessa miljövariabler, liksom de skript och program som de kör.

Följande miljövariabler skapas av batch-tjänsten för användning av multi-instance-uppgifter:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Fullständig information om dessa och andra batch-beräkningsnodmiljövariabler, inklusive deras innehåll och synlighet, finns i [Beräkningsnodmiljövariabler][msdn_env_var].

> [!TIP]
> Exemplet batch linux MPI-kod innehåller ett exempel på hur flera av dessa miljövariabler kan användas. Det [samordnings cmd Bash-skriptet][coord_cmd_example] hämtar vanliga program- och indatafiler från Azure Storage, aktiverar en NFS-resurs (Network File System) på huvudnoden och konfigurerar de andra noderna som tilldelats multi-instance-uppgiften som NFS-klienter.
>
>

## <a name="resource-files"></a>Resursfiler
Det finns två uppsättningar resursfiler att ta hänsyn till för multi-instance-aktiviteter: **vanliga resursfiler** som *alla* aktiviteter hämtas (både primära och underaktiviteter) och **de resursfiler** som har angetts för själva aktiviteten med flera instanser, som *bara den primära* aktiviteten hämtar.

Du kan ange en eller **flera vanliga resursfiler** i flerinstansinställningarna för en aktivitet. Dessa vanliga resursfiler hämtas från [Azure Storage](../storage/common/storage-introduction.md) till varje nods **uppgift delad katalog** av de primära och alla underaktiviteter. Du kan komma åt den delade katalogen för `AZ_BATCH_TASK_SHARED_DIR` uppgift från program- och samordningskommandorader med hjälp av miljövariabeln. Sökvägen `AZ_BATCH_TASK_SHARED_DIR` är identisk på varje nod som allokeras till multi-instance-aktiviteten, vilket innebär att du kan dela ett enda samordningskommando mellan primärt och alla underaktiviteter. Batch "delar" inte katalogen i en fjärråtkomst mening, men du kan använda den som en montera eller resurs som tidigare nämnts i spetsen på miljövariabler.

Resursfiler som du anger för själva multi-instance-aktiviteten hämtas till `AZ_BATCH_TASK_WORKING_DIR`aktivitetens arbetskatalog, som standard. Som nämnts, i motsats till vanliga resursfiler, hämtar endast den primära uppgiften resursfiler som angetts för själva aktiviteten med flera instanser.

> [!IMPORTANT]
> Använd alltid miljövariablerna `AZ_BATCH_TASK_SHARED_DIR` och `AZ_BATCH_TASK_WORKING_DIR` för att referera till dessa kataloger på kommandoraderna. Försök inte att konstruera banorna manuellt.
>
>

## <a name="task-lifetime"></a>Livslängd för uppgiften
Livslängden för den primära aktiviteten styr livslängden för hela multi-instance-aktiviteten. När den primära avslutar avslutas alla underaktiviteter. Avslutningskoden för den primära är uppgiftens avslutningskod och används därför för att avgöra uppgiftens lyckade eller misslyckade fel i återförsökssyfte.

Om någon av underaktiviteterna misslyckas misslyckas, misslyckas du med en returkod som inte är noll, till exempel, misslyckas hela multiinstansaktiviteten. Multi-instance-aktiviteten avslutas sedan och görs om, upp till gränsen för återförsök.

När du tar bort en aktivitet med flera instanser tas även de primära och alla underaktiviteter bort av batch-tjänsten. Alla underaktivitetskataloger och deras filer tas bort från beräkningsnoderna, precis som för en standarduppgift.

[TaskConstraints][net_taskconstraints] för en multi-instance-aktivitet, till exempel [egenskaperna MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]och [RetentionTime,][net_taskconstraint_retention] respekteras som de är för en standarduppgift och gäller för primär- och alla underaktiviteter. Om du däremot ändrar egenskapen [RetentionTime][net_taskconstraint_retention] efter att ha lagt till multiinstansaktiviteten i jobbet tillämpas den här ändringen endast på den primära aktiviteten. Alla underaktiviteter fortsätter att använda den ursprungliga [RetentionTime][net_taskconstraint_retention].

En beräkningsnods senaste uppgiftslista återspeglar id:t för en underuppgift om den senaste aktiviteten ingick i en multi-instance-aktivitet.

## <a name="obtain-information-about-subtasks"></a>Få information om underaktiviteter
Om du vill hämta information om underaktiviteter med hjälp av batch .NET-biblioteket anropar du metoden [CloudTask.ListSubtasks.][net_task_listsubtasks] Den här metoden returnerar information om alla underaktiviteter och information om beräkningsnoden som utförde aktiviteterna. Från den här informationen kan du bestämma varje underaktivitets rotkatalog, pool-ID, dess aktuella tillstånd, avslutningskod med mera. Du kan använda den här informationen i kombination med [metoden PoolOperations.GetNodeFile][poolops_getnodefile] för att hämta underskolningens filer. Observera att den här metoden inte returnerar information för den primära uppgiften (id 0).

> [!NOTE]
> Om inget annat anges gäller batch .NET-metoder som fungerar på själva [CloudTask][net_task] med flera instanser *endast* för den primära aktiviteten. När du till exempel anropar metoden [CloudTask.ListNodeFiles][net_task_listnodefiles] för en aktivitet med flera instanser returneras endast den primära aktivitetens filer.
>
>

Följande kodavsnitt visar hur du hämtar information om underaktivitet samt begär filinnehåll från de noder som de utförde.

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
[MultiInstanceTasks-kodexemplet][github_mpi] på GitHub visar hur du använder en multi-instance-aktivitet för att köra ett [MS-MPI-program][msmpi_msdn] på batchberäkningsnoder. Följ stegen i [Förberedelse](#preparation) och [körning](#execution) för att köra exemplet.

### <a name="preparation"></a>Förberedelse
1. Följ de två första stegen i [Hur man kompilerar och kör ett enkelt MS-MPI-program][msmpi_howto]. Detta uppfyller förutsättningarna för följande steg.
2. Bygg en *release* version av [MPIHelloWorld][helloworld_proj] provet MPI-program. Det här är programmet som ska köras på beräkningsnoder av multi-instance-aktiviteten.
3. Skapa en zip-fil som innehåller `MPIHelloWorld.exe` (som `MSMpiSetup.exe` du byggde steg 2) och (som du hämtade steg 1). Du laddar upp den här zip-filen som ett programpaket i nästa steg.
4. Använd [Azure-portalen][portal] för att skapa ett [batchprogram](batch-application-packages.md) som heter "MPIHelloWorld" och ange zip-filen som du skapade i föregående steg som version "1.0" av programpaketet. Mer information [finns i Ladda upp och hantera program.](batch-application-packages.md#upload-and-manage-applications)

> [!TIP]
> Skapa en *versionsversion* av `MPIHelloWorld.exe` så att du inte behöver inkludera några `msvcp140d.dll` `vcruntime140d.dll`ytterligare beroenden (till exempel eller) i programpaketet.
>
>

### <a name="execution"></a>Körnings-
1. Hämta [azure-batch-samples][github_samples_zip] från GitHub.
2. Öppna MultiInstanceTasks-lösningen i Visual Studio 2019. **solution** Lösningsfilen `MultiInstanceTasks.sln` finns i:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. Ange dina batch- och `AccountSettings.settings` lagringskontouppgifter i projektet **Microsoft.Azure.Batch.Samples.Common.**
4. **Skapa och kör** MultiInstanceTasks-lösningen för att köra MPI-exempelprogrammet på beräkningsnoder i en batchpool.
5. *Valfritt*: Använd [Azure-portalen][portal] eller [Batch Explorer][batch_labs] för att undersöka exempelpoolen, jobbet och uppgiften ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") innan du tar bort resurserna.

> [!TIP]
> Du kan ladda ner [Visual Studio Community][visual_studio] gratis om du inte har Visual Studio.
>
>

Utdata `MultiInstanceTasks.exe` från liknar följande:

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
* I bloggen Microsoft HPC & Azure Batch Team beskrivs [MPI-stöd för Linux på Azure Batch][blog_mpi_linux]och information om hur du använder [OpenFOAM][openfoam] med Batch. Du hittar Python-kodexempel för [OpenFOAM-exemplet på GitHub][github_mpi].
* Lär dig hur du [skapar pooler av Linux-beräkningsnoder](batch-linux-nodes.md) för användning i dina Azure Batch MPI-lösningar.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: https://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: https://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
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
