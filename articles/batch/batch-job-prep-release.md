---
title: "Skapa uppgifter för att förbereda jobb och fullständig jobb på datornoderna - Azure Batch | Microsoft Docs"
description: "Använd jobbet nivå förberedande uppgifter för att minimera överföring av data till Azure Batch-beräkningsnoder och släpp aktiviteter för rensning av noden på jobbet har slutförts."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a2525c02ce7bd3969469d2e28a5fccc948f89b1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Kör jobbförberedelse och jobbet versionen uppgifter på Batch-beräkningsnoder

 En Azure Batch-jobbet kräver ofta någon form av installationsprogrammet innan dess aktiviteter utförs och jobbet efter Underhåll när dess aktiviteter är slutförda. Du kan behöva hämta vanliga uppgiften indata till compute-noder eller överföra utdata för aktiviteten till Azure Storage när jobbet har slutförts. Du kan använda **jobbet förberedelse** och **jobbet versionen** åtgärder att utföra dessa åtgärder.

## <a name="what-are-job-preparation-and-release-tasks"></a>Vad är jobbförberedelseuppgiften och viktiga uppgifter?
Innan du kör ett jobb uppgifter jobbförberedelseuppgift jobb som körs på alla compute-noder som är schemalagda att köras minst en aktivitet. När jobbet är slutfört, körs jobbet versionen aktiviteten på varje nod i den pool som körs minst en aktivitet. Du kan ange en kommandorad som ska anropas när en förberedelse eller viktig aktivitet körs precis som med normal batchaktiviteter.

Jobbet förberedelse och version aktiviteter finns bekant Batch aktivitet funktioner, till exempel Filhämtning ([resursfiler][net_job_prep_resourcefiles]), utökade körning, anpassade miljövariabler, varaktighet för maximal körning av, försök igen antal och kvarhållningstiden för filen.

I följande avsnitt du lär dig hur du använder den [JobPreparationTask] [ net_job_prep] och [JobReleaseTask] [ net_job_release] klasser hittades i [ Batch-.NET] [ api_net] bibliotek.

> [!TIP]
> Jobbet förberedelse och version aktiviteter är särskilt användbart i miljöer med ”delat poolen”, som en pool av datornoderna kvarstår mellan jobbkörningar och används av många jobb.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>När du ska använda jobbförberedelseuppgiften och släpp aktiviteter
Jobbförberedelseuppgiften och versionen jobbuppgifter är passar bra för följande situationer:

**Hämta vanliga aktivitetsdata**

Batchjobb kräver ofta en gemensam uppsättning data som indata för jobbets uppgifter. Till exempel i dagliga risk analys beräkningar är marknaden data projektspecifika ännu gemensamma för alla uppgifter i jobbet. Informationen marknaden ofta flera gigabyte i storlek, ska hämtas till varje compute-nod bara en gång så att alla aktiviteter som körs på noden kan använda den. Använd en **förberedelse projektaktivitet** att hämta dessa data till varje nod innan körningen av jobbet har andra aktiviteter.

**Ta bort jobb- och utdata**

I en ”delad pool” miljö, där en pool compute-noder inte är inaktiverade mellan jobb, kan du behöva ta bort jobbdata mellan körs. Du kan behöva spara diskutrymme på noderna eller uppfyller organisationens säkerhetsprinciper. Använd en **versionen projektaktivitet** att ta bort data som hämtas av en projektaktivitet förberedelse, eller genereras under körning av aktiviteten.

**Kvarhållning av logg**

Du kanske vill behålla en kopia av loggfiler som dina aktiviteter genererar eller kanske kraschdumpfiler som genereras av misslyckade program. Använd en **versionen projektaktivitet** i sådana fall att komprimera och överföra data till en [Azure Storage] [ azure_storage] konto.

> [!TIP]
> Ett annat sätt att bevara loggar och andra jobb- och utdata data är att använda den [Azure Batch filen konventioner](batch-task-output.md) bibliotek.
> 
> 

## <a name="job-preparation-task"></a>Förberedelse för projektaktivitet
Innan körningen av ett jobb uppgifter utför Batch förberedelse projektaktivitet på varje beräkningsnod som kommer att köra en aktivitet. Som standard väntar Batch-tjänsten för förberedelse projektaktivitet ska slutföras innan du kör uppgifter som är schemalagda att köras på noden. Du kan dock konfigurera tjänsten som inte ska vänta. Om noden har startats om förberedelse projektaktivitet körs igen, men du kan också inaktivera det här beteendet.

Förberedelse av projektaktivitet körs bara på noder som är schemalagda att köra en aktivitet. Detta förhindrar att onödiga körningen av en jobbförberedelseuppgift om en nod inte har tilldelats en aktivitet. Detta kan inträffa när antalet aktiviteter för ett jobb är mindre än antalet noder i en pool. Det gäller även när [samtidiga uppgiftskörningen](batch-parallel-node-tasks.md) är aktiverad, vilket lämnar vissa noder inaktiv om antal uppgifter är lägre än totalt antal möjliga samtidiga uppgifter. Genom att inte köra förberedelse projektaktivitet på inaktiv noder kan lägga du mindre pengar på kostnader för överföring av data.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] skiljer sig från [CloudPool.StartTask] [ pool_starttask] i att JobPreparationTask körs i början av varje jobb medan startuppgift har ställts körs bara när en beräkningsnod först ansluter till en pool eller startar om.
> 
> 

## <a name="job-release-task"></a>Versionen för projektaktivitet
När ett jobb har markerats som slutförd, körs jobbet viktig aktivitet på varje nod i den pool som körs minst en aktivitet. Du kan markera ett jobb som slutförd genom att utfärda en avsluta begäran. Batch-tjänsten sedan anger jobbets status till *avslutar*, avbryts alla aktiva eller köra uppgifter som är kopplad till jobbet och kör jobbet versionen aktiviteten. Jobbet flyttas sedan till den *slutförts* tillstånd.

> [!NOTE]
> Borttagning av jobbet körs också viktig projektaktivitet. Men om ett jobb har redan avslutats, körs släpp uppgift inte en gång om jobbet senare tas bort.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Jobbet prep och släpp aktiviteter med Batch .NET
Om du vill använda en projektaktivitet förberedelse, tilldela en [JobPreparationTask] [ net_job_prep] objekt till ditt jobb [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] egenskapen. På liknande sätt kan initiera en [JobReleaseTask] [ net_job_release] och tilldela den till ditt jobb [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] egenskapen anges jobbet Släpp uppgiften.

I det här kodstycket `myBatchClient` är en instans av [BatchClient][net_batch_client], och `myPool` är en befintlig adresspool i Batch-kontot.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Som tidigare nämnts är körs den här uppgiften när ett jobb avslutas eller tas bort. Avsluta ett jobb med [JobOperations.TerminateJobAsync][net_job_terminate]. Ta bort ett jobb med [JobOperations.DeleteJobAsync][net_job_delete]. Du vanligtvis avsluta eller ta bort ett jobb när dess aktiviteter har slutförts eller när en tidsgräns som du har definierat har uppnåtts.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Kodexempel på GitHub
Om du vill se jobbförberedelseuppgiften och viktiga uppgifter i praktiken, ta en titt på [JobPrepRelease] [ job_prep_release_sample] exempelprojektet på GitHub. Detta konsolprogram gör följande:

1. Skapar en pool med två noder som ”liten”.
2. Skapar ett jobb med jobbförberedelseuppgiften, version och standarduppgifter.
3. Kör jobbet förberedelse aktiviteten som först skriver nod-ID till en textfil i en nod ”delade” katalogen.
4. Kör en aktivitet på varje nod som skriver dess aktivitets-ID till samma textfil.
5. När alla aktiviteter har slutförts (eller tidsgränsen uppnås), skriver du innehållet på varje nod textfil till konsolen.
6. När jobbet har slutförts körs projektaktiviteten versionen om du vill ta bort filen från noden.
7. Skriver ut slutkoder jobbet förberedelse och version uppgifter för varje nod som de körs.
8. Pausar körningen för att bekräfta borttagning av jobbet och/eller poolen.

Utdata från exempelprogrammet som liknar följande:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> På grund av variabeln skapa och starta när noder i en ny pool (vissa noder är redo för uppgifter före andra), kan du se olika utdata. Eftersom uppgifterna slutföra snabbt, kan en av noderna i den poolen specifikt köra alla uppgifter i jobbet. Om detta händer ser du att jobbet Förbered dig och versionen uppgifter finns inte för noden som körs inga aktiviteter.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Granska jobbförberedelseuppgiften och versionen uppgifter i Azure-portalen
När du kör exempelprogrammet som du kan använda den [Azure-portalen] [ portal] att visa egenskaperna för jobbet och dess uppgifter eller även hämta den delade textfil som ändras av jobbets uppgifter.

Skärmbilden nedan visar den **förberedelse uppgifter bladet** i Azure portal efter en körning av exempelprogrammet. Navigera till den *JobPrepReleaseSampleJob* egenskaper när aktiviteterna har slutförts (men innan du tar bort dina jobb och poolen) och klicka på **förberedande uppgifter** eller **viktiga uppgifter**visa deras egenskaper.

![Förberedelse av jobbegenskaper i Azure-portalen][1]

## <a name="next-steps"></a>Nästa steg
### <a name="application-packages"></a>Programpaket
Förutom projektaktiviteten förberedelse, du kan också använda den [programpaket](batch-application-packages.md) funktion i Batch för att förbereda compute-noder för körning av aktiviteten. Den här funktionen är särskilt användbar för att distribuera program som inte kräver kör ett installationsprogram, program som innehåller många (100 +) filer eller program som kräver strikt versionskontroll.

### <a name="installing-applications-and-staging-data"></a>Installera program och mellanlagring av data
Den här MSDN-foruminlägg innehåller en översikt över flera metoder för att förbereda din noder för pågående aktiviteter:

[Installera program och mellanlagring av data på Batch-beräkningsnoder][forum_post]

Skrivs av en Azure Batch-gruppmedlemmar beskrivs den flera metoder som du kan använda för att distribuera program och data för att compute-noder.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
