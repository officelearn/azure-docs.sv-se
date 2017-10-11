---
title: "Azure Batch compute-nod miljövariabler | Microsoft Docs"
description: "Compute-nod miljö variabelreferens för Azure Batch Analytics."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/05/2017
ms.author: tamram
ms.openlocfilehash: 29f642754430957e77ef68946f721f8e15dba065
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-batch-compute-node-environment-variables"></a>Azure Batch compute-nod miljövariabler
Den [Azure Batch-tjänsten](https://azure.microsoft.com/services/batch/) anger följande miljövariabler för compute-noder. Du kan referera till dessa miljövariabler i aktiviteten kommandorader och i program och skript som körs av kommandorader.

Mer information om hur du använder miljövariabler med Batch finns [miljöinställningar för uppgifter](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Variabeln synlighet för miljö

Miljövariablerna syns bara i kontexten för den **aktivitet användaren**, användarkontot på noden som en aktivitet körs. Du ser dem *inte* om du [fjärransluter](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) till en beräkningsnod via RDP (Remote Desktop Protocol) eller SSH (Secure Shell) och visar en lista över miljövariablerna. Det beror på att användarkontot som används för fjärranslutning inte är samma som det konto som används av aktiviteten.

## <a name="command-line-expansion-of-environment-variables"></a>Kommandoradsverktyget expandering av miljövariabler

Kommandorader som körs av uppgifter på compute-noder gör inte köras under ett gränssnitt. Därför kan dessa kommandorader internt kan inte dra nytta av shell-funktioner, till exempel expandering (Detta omfattar den `PATH`). Om du vill dra nytta av dessa funktioner måste du **anropa gränssnittet** på kommandoraden. Till exempel starta `cmd.exe` på Windows compute-noder eller `/bin/sh` på Linux-noder:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Miljövariabler

| Variabelnamn                     | Beskrivning                                                              | Tillgänglighet | Exempel |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Namnet på Batch-kontot som aktiviteten tillhör.                  | Alla aktiviteter.   | mybatchaccount |
| AZ_BATCH_CERTIFICATES_DIR       | En katalog i den [arbetskatalog för aktiviteten] [ files_dirs] i vilka certifikat som lagras för Linux compute-noder. Observera att den här miljövariabeln inte gäller för Windows compute-noder.                                                  | Alla aktiviteter.   |  /mnt/batch/Tasks/workitems/batchjob001/Job-1/task001/certs |
| AZ_BATCH_JOB_ID                 | ID:t för jobbet som aktiviteten hör till. | Alla aktiviteter utom starta uppgiften. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Den fullständiga sökvägen till jobbförberedelseuppgiften [aktivitet directory] [ files_dirs] på noden. | Alla aktiviteter utom start och aktiviteten jobbet förberedelser. Endast tillgänglig om jobbet har konfigurerats med en förberedelse för projektaktivitet. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Den fullständiga sökvägen till jobbförberedelseuppgiften [arbetskatalog för aktiviteten] [ files_dirs] på noden. | Alla aktiviteter utom start och aktiviteten jobbet förberedelser. Endast tillgänglig om jobbet har konfigurerats med en förberedelse för projektaktivitet. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | ID för den nod som har tilldelats aktiviteten. | Alla aktiviteter. | TVM-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | Den fullständiga sökvägen till roten för alla [Batch-kataloger] [ files_dirs] på noden. | Alla aktiviteter. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Den fullständiga sökvägen till den [delade katalogen] [ files_dirs] på noden. Alla aktiviteter som utförs på en nod har åtkomst till den här katalogen. Uppgifter som utförs på andra noder har inte fjärråtkomst till den här katalogen (det inte är en ”delad” nätverkskatalog). | Alla aktiviteter. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Den fullständiga sökvägen till den [starta uppgiften directory] [ files_dirs] på noden. | Alla aktiviteter. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | ID:t för poolen som aktiviteten körs i. | Alla aktiviteter. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Den fullständiga sökvägen till den [aktivitet directory] [ files_dirs] på noden. Den här katalogen innehåller det `stdout.txt` och `stderr.txt` för uppgiften och AZ_BATCH_TASK_WORKING_DIR. | Alla aktiviteter. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | ID:t för den aktuella aktiviteten. | Alla aktiviteter utom starta uppgiften. | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | Den fullständiga sökvägen till den [arbetskatalog för aktiviteten] [ files_dirs] på noden. Aktiviteten körs för tillfället har åtkomst till den här katalogen. | Alla aktiviteter. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Listan över noder och antalet kärnor per nod som har tilldelats en [flera instansuppgift][multi_instance]. Noder och kärnor anges i formatet`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, där antalet noder som följs av en eller flera noden IP-adresser och antalet kärnor för varje. |  Flera instanser primära och underaktiviteter. |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | Listan över noder som har tilldelats en [flera instansuppgift] [ multi_instance] i formatet `nodeIP;nodeIP`. | Flera instanser primära och underaktiviteter. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | Listan över noder som har tilldelats en [flera instansuppgift] [ multi_instance] i formatet `nodeIP,nodeIP`. | Flera instanser primära och underaktiviteter. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | IP-adressen och porten för Beräkningsnoden där den främsta uppgiften för en [flera instansuppgift] [ multi_instance] körs. | Flera instanser primära och underaktiviteter. | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | En sökväg som är identisk för den primära aktiviteten och varje underaktivitet till en [flera instansuppgift][multi_instance]. Sökvägen finns på varje nod där flera instanser uppgiften körs och är tillgängliga för kommandona aktivitet som körs på noden skrivskyddstyp (både den [samordning kommandot] [ coord_cmd] och [ kommandot programmet][app_cmd]). Underaktiviteter eller en primär aktivitet som kör på andra noder har inte fjärråtkomst till den här katalogen (det inte är en ”delad” nätverkskatalog). | Flera instanser primära och underaktiviteter. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Anger om den aktuella noden är huvudnoden för en [flera instansuppgift][multi_instance]. Möjliga värden är `true` och `false`.| Flera instanser primära och underaktiviteter. | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | Om `true`, den aktuella noden är en dedikerad nod. Om `false`, är det en [låg prioritet nod](batch-low-pri-vms.md). | Alla aktiviteter. | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
