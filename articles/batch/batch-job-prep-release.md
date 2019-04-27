---
title: Skapa uppgifter för att förbereda jobb och fullständig jobb på beräkningsnoder – Azure Batch | Microsoft Docs
description: Använd jobbnivå förberedande uppgifter för att minimera dataöverföringen till Azure Batch compute-noder och publiceringsuppgifter för noden rensning när jobbet har slutförts.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 02/27/2017
ms.date: 06/29/2018
ms.author: v-junlch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 517ac0f612b9e5fc5909a7f0fe2ce088c9b367d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776206"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Kör jobbförberedelse- och jobbpubliceringsaktiviteter på Batch compute-noder

 Ett Azure Batch-jobb kräver ofta någon form av installationen innan dess aktiviteter utförs och efter jobbets Underhåll när dess aktiviteter har slutförts. Du kan behöva hämta vanliga indata för aktiviteten till compute-noder eller överför uppgift utdata till Azure Storage när jobbet har slutförts. Du kan använda **jobbförberedelse** och **jobbet versionen** uppgifter du utför dessa åtgärder.

## <a name="what-are-job-preparation-and-release-tasks"></a>Vad är jobbförberedelse och publiceringsuppgifter?
Innan aktiviteterna i ett jobb kör körs jobbförberedelseaktiviteten på alla beräkningsnoder som schemalagts att köra minst en aktivitet. När jobbet har slutförts körs jobbpubliceringsaktiviteten på alla noder i poolen som har kört minst en aktivitet. Du kan ange en kommandorad som ska anropas när en projektaktivitet för förberedelse eller versionen körs precis som med normal Batch-aktiviteterna.

Jobbförberedelse-och versionen stöder välbekanta Batch uppgift funktioner som Filhämtning ([resursfiler][net_job_prep_resourcefiles]), utökade körning, anpassade miljövariabler, högsta körningstid, försök igen antal och kvarhållningstid för filer.

I avsnitten nedan får du lära dig hur du använder den [JobPreparationTask] [ net_job_prep] och [JobReleaseTask] [ net_job_release] klasser finns i [ Batch .NET] [ api_net] biblioteket.

> [!TIP]
> Jobbförberedelse-och versionen är särskilt användbart i ”delade poolen” miljöer, som en pool med beräkningsnoder som kvarstår mellan körs och används av många jobb.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>När du ska använda jobbförberedelse och publiceringsuppgifter
Jobbförberedelse- och jobbpubliceringsaktiviteter är ett bra alternativ i följande situationer:

**Ladda ned vanliga uppgiftsinformation**

Batch-jobb kräver ofta en gemensam uppsättning data som indata för jobbets uppgifter. Till exempel är marknadsdata i dagliga riskberäkningarna över analys, projektspecifika, ännu gemensamma för alla aktiviteter i jobbet. Den här marknadsdata, ofta flera gigabyte, ska bara en gång laddas ned till varje beräkningsnod så att alla aktiviteter som körs på noden kan använda den. Använd en **jobbförberedelseaktiviteten** att hämta dessa data till varje nod innan körningen av jobbet är andra aktiviteter.

**Ta bort jobb- och uppgiftsutdata**

I en ”delad pool” miljö, där en poolens beräkningsnoder inte är inaktiverade mellan jobb kan du behöva ta bort jobbdata mellan körningar. Du kan behöva spara diskutrymme på noderna eller uppfylla organisationens säkerhetsprinciper. Använd en **jobbpubliceringsaktivitet** att ta bort data som hämtas av en jobbförberedelseaktivitet eller genereras under körning av aktiviteten.

**Kvarhållning av logg**

Du kanske vill behålla en kopia av loggfiler som dina aktiviteter genererar eller kanske kraschdumpfiler som genereras av misslyckade program. Använd en **jobbpubliceringsaktivitet** i sådana fall att komprimera och ladda upp dem till en [Azure Storage] [ azure_storage] konto.

> [!TIP]
> Ett annat sätt att spara loggar och andra jobb- och utdata i data är att använda den [Azure Batch File Conventions](batch-task-output.md) biblioteket.
> 
> 

## <a name="job-preparation-task"></a>Jobbförberedelseuppgift
Innan körningen av ett jobb och uppgifter utför Batch jobbförberedelseaktiviteten på varje beräkningsnod som har schemalagts att köra en uppgift. Som standard väntar Batch-tjänsten för jobbförberedelseaktiviteten ska slutföras innan du kör uppgifter som är schemalagd att köras på noden. Du kan dock konfigurera tjänsten inte vänta. Om noden startar om jobbförberedelseaktiviteten körs igen, men du kan också inaktivera det här beteendet.

Jobbförberedelseaktiviteten körs bara på noder som är schemalagda att köra en uppgift. Detta förhindrar onödig körningen av en förberedande aktivitet om en nod inte har tilldelats en uppgift. Detta kan inträffa när antalet uppgifter för ett jobb är mindre än antalet noder i poolen. Det gäller även när [för körning av samtidiga aktiviteten](batch-parallel-node-tasks.md) är aktiverad, så att vissa noder inaktiv om antal uppgifter är lägre än totalt antal möjliga samtidiga aktiviteter. Genom att inte köra jobbförberedelseaktiviteten på inaktiva noder, kan du spenderar mindre på kostnaderna för dataöverföring.

> [!NOTE]
> [JobPreparationTask] [ net_job_prep_cloudjob] skiljer sig från [CloudPool.StartTask] [ pool_starttask] eftersom JobPreparationTask körs i början av varje jobb medan StartTask körs bara när en beräkningsnod först ansluter till en pool eller startar om.
> 
> 

## <a name="job-release-task"></a>Jobbpubliceringsuppgift
När ett jobb har markerats som slutförd, körs jobbpubliceringsaktiviteten på varje nod i poolen som har kört minst en aktivitet. Du kan markera ett jobb som slutförd genom att utfärda en avslutningsbegäran. Batch-tjänsten anger sedan jobbets status till *avslutande*avslutas alla aktiva eller pågående aktiviteter som är kopplade till jobbet och körs jobbpubliceringsaktiviteten. Jobbet därefter flyttas till den *slutförts* tillstånd.

> [!NOTE]
> Borttagning av jobbet körs också jobbpubliceringsaktiviteten. Men om ett jobb har redan avslutats, version aktiviteten körs inte en gång om jobbet tas bort senare.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Jobbet prep och släpper aktiviteter med Batch .NET
Om du vill använda en jobbförberedelseaktivitet, tilldela en [JobPreparationTask] [ net_job_prep] objekt för jobbets [CloudJob.JobPreparationTask] [ net_job_prep_cloudjob] egenskapen. På samma sätt kan initiera en [JobReleaseTask] [ net_job_release] och tilldela den till ditt jobb [CloudJob.JobReleaseTask] [ net_job_prep_cloudjob] egenskapen att ställa in jobbets Släpp uppgiften.

I det här kodfragmentet `myBatchClient` är en instans av [BatchClient][net_batch_client], och `myPool` är en befintlig pool i Batch-konto.

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
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Som tidigare nämnts körs den version uppgiften när ett jobb avslutas eller tas bort. Avsluta ett jobb med [JobOperations.TerminateJobAsync][net_job_terminate]. Ta bort ett jobb med [JobOperations.DeleteJobAsync][net_job_delete]. Du kan vanligtvis avsluta eller ta bort ett jobb när dess aktiviteter har slutförts, eller när en tidsgräns som du har definierat har uppnåtts.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Kodexempel på GitHub
Om du vill se jobbförberedelse och publiceringsuppgifter i praktiken, Kolla in den [JobPrepRelease] [ job_prep_release_sample] exempelprojektet på GitHub. Den här konsolappen gör följande:

1. Skapar en pool med två noder.
2. Skapar ett jobb med jobbförberedelse, versionen och standardaktiviteter.
3. Kör jobbförberedelseaktiviteten, som först skriver nod-ID till en textfil i en nod ”delade” katalogen.
4. Kör en aktivitet på varje nod som skriver dess aktivitets-ID till samma textfilen.
5. När alla aktiviteter har slutförts (eller tidsgränsen har nåtts), skriver du ut innehållet i varje nod textfil till konsolen.
6. När jobbet har slutförts körs jobbpubliceringsaktiviteten för att ta bort filen från noden.
7. Skriver ut slutkoder över projektaktiviteter för jobbförberedelse- och jobbpubliceringsaktiviteter för varje nod som de körs.
8. Pausar körningen så att en bekräftelse av borttagning av jobb och/eller pool.

Utdata från exempelprogrammet ser ut ungefär så här:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
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
> På grund av variabel skapas och börja tidpunkten för noder i en ny pool (vissa noder är redo för uppgifter före andra), kan du se olika utdata. Eftersom aktiviteterna slutförs snabbt, kan en av noderna i poolen mer specifikt kan köra alla jobbets aktiviteter. Om detta händer ser du att jobbet Förbered och publiceringsuppgifter finns inte för den nod som körs inga aktiviteter.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspektera jobbförberedelse och publiceringsuppgifter i Azure portal
När du kör exempelprogrammet, du kan använda den [Azure-portalen] [ portal] visa egenskaperna för jobbets och aktiviteternas eller även hämta delade textfilen som ändras av jobbets aktiviteter.

Skärmbilden nedan visar den **förberedelse uppgifter bladet** i Azure-portalen när du har en körning av exempelprogrammet. Navigera till den *JobPrepReleaseSampleJob* egenskaper efter dina aktiviteter har slutförts (men innan du tar bort i jobbet och poolen) och klicka på **förberedelseuppgifter** eller **publiceringsuppgifter**visa deras egenskaper.

![Egenskaper för förberedelser i Azure-portalen][1]

## <a name="next-steps"></a>Nästa steg
### <a name="application-packages"></a>Programpaket
Förutom jobbförberedelseaktiviteten, du kan också använda den [programpaket](batch-application-packages.md) i Batch för att förbereda beräkningsnoder för körning av aktiviteten. Den här funktionen är särskilt användbar för att distribuera program som inte behöver köra ett installationsprogram, program som innehåller många (100 +) filer eller program som kräver strikt versionskontroll.

### <a name="installing-applications-and-staging-data"></a>Installera program, organiserar data
Den här MSDN-foruminlägg innehåller en översikt över flera metoder för att förbereda noderna för att köra uppgifter:

[Installera program, organiserar data på Batch compute-noder][forum_post]

Skrivna med ett Azure Batch-gruppmedlemmar beskrivs den flera metoder som du kan använda för att distribuera program och data för att beräkna noder.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://www.azure.cn/home/features/storage/
[portal]: https://portal.azure.cn
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

<!-- Update_Description: wording update -->