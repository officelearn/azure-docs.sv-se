---
title: Skapa uppgifter för att förbereda & slutförde jobb på beräkningsnoder - Azure Batch
description: Använd förberedelseuppgifter på jobbnivå för att minimera dataöverföring till Azure Batch-beräkningsnoder och släpp uppgifter för nodrensning vid slutförande av jobbet.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: d9f6f015c210592d5d8053b1b34d5357bb357629
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586792"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Kör jobbförberedelse- och jobbfrisättningsuppgifter på batchberäkningsnoder

 Ett Azure Batch-jobb kräver ofta någon form av installation innan dess uppgifter körs och underhåll efter jobbet när dess uppgifter har slutförts. Du kan behöva hämta vanliga indata för uppgift till dina beräkningsnoder eller överföra uppgiftsutdata till Azure Storage när jobbet har slutförts. Du kan använda **jobbförberedelser** och **jobbfrisättningsuppgifter** för att utföra dessa åtgärder.

## <a name="what-are-job-preparation-and-release-tasks"></a>Vad är jobbförberedelser och utgivningsuppgifter?
Innan ett jobbs aktiviteter körs körs jobbförberedelseaktiviteten på alla beräkningsnoder som är schemalagda att köra minst en aktivitet. När jobbet har slutförts körs jobbfrisläppningsaktiviteten på varje nod i poolen som utförde minst en aktivitet. Precis som med vanliga batchaktiviteter kan du ange en kommandorad som ska anropas när en jobbförberedelse eller versionsaktivitet körs.

Jobbförberedelse- och utgivningsuppgifter erbjuder välbekanta batchaktivitetsfunktioner som filhämtning ([resursfiler),][net_job_prep_resourcefiles]förhöjd körning, anpassade miljövariabler, maximal körningsvaraktighet, antal försök igen och filkvarhållningstid.

I följande avsnitt får du lära dig hur du använder klasserna [JobPreparationTask][net_job_prep] och [JobReleaseTask][net_job_release] som finns i [batch-BIBLIOTEKET .NET.][api_net]

> [!TIP]
> Jobbförberedelse- och utgivningsuppgifter är särskilt användbara i miljöer med delad pool, där en pool med beräkningsnoder finns kvar mellan jobbkörningar och används av många jobb.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>När du ska använda jobbförberedelser och utgivningsuppgifter
Jobbförberedelser och jobbutgåvan är en bra passform för följande situationer:

**Hämta vanliga uppgiftsdata**

Batch-jobb kräver ofta en gemensam uppsättning data som indata för jobbets aktiviteter. I dagliga riskanalysberäkningar är marknadsdata till exempel jobbspecifika, men ändå gemensamma för alla aktiviteter i jobbet. Dessa marknadsdata, ofta flera gigabyte i storlek, bör hämtas till varje beräkningsnod endast en gång så att alla aktiviteter som körs på noden kan använda den. Använd en **jobbförberedelseuppgift** för att hämta dessa data till varje nod innan jobbets andra aktiviteter utförs.

**Ta bort jobb- och aktivitetsutdata**

I en "delad pool"-miljö, där en pools beräkningsnoder inte inaktiveras mellan jobb, kan du behöva ta bort jobbdata mellan körningar. Du kan behöva spara diskutrymmet på noderna eller uppfylla organisationens säkerhetsprinciper. Använd en **jobbutgåva för** att ta bort data som har hämtats av en jobbförberedelseaktivitet eller genererats under aktivitetskörningen.

**Loggkvarhållning**

Du kanske vill behålla en kopia av loggfiler som dina uppgifter genererar, eller kanske kraschdumpfiler som kan genereras av misslyckade program. Använd en **jobbutgåva** i sådana fall för att komprimera och överföra dessa data till ett [Azure Storage-konto.][azure_storage]

> [!TIP]
> Ett annat sätt att spara loggar och andra jobb- och uppgiftsdata är att använda [Azure Batch File Conventions-biblioteket.](batch-task-output.md)
>
>

## <a name="job-preparation-task"></a>Uppgift om förberedelse av arbete


Före körning av ett jobbs aktiviteter kör Batch jobbförberedelseaktiviteten för varje beräkningsnod som schemalagts för att köra en aktivitet. Som standard väntar Batch på att jobbförberedelseaktiviteten ska slutföras innan de aktiviteter som schemalagts ska köras på noden körs. Du kan dock konfigurera tjänsten att inte vänta. Om noden startas om körs jobbförberedelseaktiviteten igen. Du kan också inaktivera det här beteendet. Om du har ett jobb med en jobbförberedelseuppgift och en jobbhanterare aktivitet konfigurerad, körs jobbförberedelseuppgiften före jobbhanterarens uppgift, precis som det gör för alla andra aktiviteter. Jobbförberedelseuppgiften körs alltid först.

Jobbförberedelseaktiviteten körs endast på noder som är schemalagda att köra en aktivitet. Detta förhindrar onödig körning av en förberedelseuppgift om en nod inte tilldelas en aktivitet. Detta kan inträffa när antalet aktiviteter för ett jobb är mindre än antalet noder i en pool. Det gäller även när [samtidig körning av aktiviteter](batch-parallel-node-tasks.md) är aktiverat, vilket gör att vissa noder är inaktiva om antalet aktiviteter är lägre än det totala antalet samtidiga aktiviteter. Genom att inte köra jobbförberedelseuppgiften på inaktiva noder kan du spendera mindre pengar på dataöverföringsavgifter.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] skiljer sig från [CloudPool.StartTask][pool_starttask] däri JobPreparationTask körs i början av varje jobb, medan StartTask körs endast när en beräkningsnod först ansluter till en pool eller startar om.
>


>## <a name="job-release-task"></a>Uppgift om projektfrisläppning

När ett jobb har markerats som slutfört körs jobbfrisläppningsuppgiften på varje nod i poolen som utförde minst en aktivitet. Du markerar ett jobb som slutfört genom att utfärda en avsluta-begäran. Batch-tjänsten anger sedan jobbtillståndet till att *avsluta*, avslutar alla aktiva eller köra aktiviteter som är associerade med jobbet och kör jobbfrisläppningsaktiviteten. Jobbet flyttas sedan till det *slutförda* tillståndet.

> [!NOTE]
> Jobbborttagning kör också jobbfrisläppningsuppgiften. Men om ett jobb redan har avslutats körs inte versionsaktiviteten en andra gång om jobbet tas bort senare.

Jobbreleaseaktiviteter kan köras i högst 15 minuter innan de avslutas av batch-tjänsten. Mer information finns i [REST API-referensdokumentationen](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Jobbförberedelse- och utgivningsuppgifter med Batch .NET
Om du vill använda en jobbförberedelseuppgift tilldelar du ett [JobPreparationTask-objekt][net_job_prep] till jobbets [CloudJob.JobPreparationTask-egenskap.][net_job_prep_cloudjob] På samma sätt initierar du en [JobReleaseTask][net_job_release] och tilldelar den till jobbets [CloudJob.JobReleaseTask-egenskap][net_job_prep_cloudjob] för att ange jobbets versionsuppgift.

I det här kodavsnittet `myBatchClient` är en instans av [BatchClient][net_batch_client]och `myPool` är en befintlig pool i batch-kontot.

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

Som tidigare nämnts körs versionsaktiviteten när ett jobb avslutas eller tas bort. Avsluta ett jobb med [JobOperations.TerminateJobAsync][net_job_terminate]. Ta bort ett jobb med [JobOperations.DeleteJobAsync][net_job_delete]. Du avslutar vanligtvis eller tar bort ett jobb när dess aktiviteter är slutförda eller när en timeout som du har definierat har nåtts.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Kodexempel på GitHub
Om du vill se jobbförberedelser och utgivning av aktiviteter i praktiken kan du läsa exempelprojektet [JobPrepRelease][job_prep_release_sample] på GitHub. Det här konsolprogrammet gör följande:

1. Skapar en pool med två noder.
2. Skapar ett jobb med jobbförberedelser, utgivning och standarduppgifter.
3. Kör jobbförberedelseuppgiften, som först skriver nod-ID:t till en textfil i en nods "delade" katalog.
4. Kör en aktivitet på varje nod som skriver sitt aktivitets-ID till samma textfil.
5. När alla uppgifter har slutförts (eller tidsgränsen har nåtts) skrivs innehållet i varje nods textfil ut till konsolen.
6. När jobbet är slutfört kör jobbfrisläppningsuppgiften för att ta bort filen från noden.
7. Skriver ut avslutningskoderna för jobbförberedelser och utgivningsuppgifter för varje nod som de utförde.
8. Pausar körningen för att tillåta bekräftelse av jobb och/eller borttagning av pool.

Utdata från exempelprogrammet liknar följande:

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
> På grund av variabeln skapas och starttiden för noder i en ny pool (vissa noder är redo för aktiviteter före andra), kan du se olika utdata. Eftersom aktiviteterna slutförs snabbt kan en av poolens noder köra alla jobbets uppgifter. Om detta inträffar kommer du att märka att jobbförberedelse- och utgivningsuppgifterna inte finns för noden som inte utförde några aktiviteter.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Granska jobbförberedelser och utgivningsuppgifter i Azure-portalen
När du kör exempelprogrammet kan du använda [Azure-portalen][portal] för att visa egenskaperna för jobbet och dess uppgifter, eller till och med hämta den delade textfilen som ändras av jobbets uppgifter.

Skärmbilden nedan visar **bladet Förberedelseuppgifter** i Azure-portalen efter en körning av exempelprogrammet. Navigera till egenskaperna *JobPrepReleaseSampleJob* när aktiviteterna har slutförts (men innan du tar bort jobbet och poolen) och klicka på **Förberedelseaktiviteter** eller **Frigör uppgifter** för att visa deras egenskaper.

![Egenskaper för förberedelse av jobb i Azure-portalen][1]

## <a name="next-steps"></a>Nästa steg
### <a name="application-packages"></a>Programpaket
Förutom jobbförberedelseuppgiften kan du också använda [funktionen programpaket](batch-application-packages.md) i Batch för att förbereda beräkningsnoder för körning av aktiviteter. Den här funktionen är särskilt användbar för att distribuera program som inte kräver att köra en installationsprogram, program som innehåller många (100 +) filer eller program som kräver strikt versionskontroll.

### <a name="installing-applications-and-staging-data"></a>Installera program och mellanlagringsdata
Detta MSDN forum inlägg ger en översikt över flera metoder för att förbereda dina noder för att köra uppgifter:

[Installera program och mellanlagringsdata på batchberäkningsnoder][forum_post]

Den är skriven av en av Azure Batch-gruppmedlemmarna och diskuterar flera tekniker som du kan använda för att distribuera program och data för att beräkna noder.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
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
