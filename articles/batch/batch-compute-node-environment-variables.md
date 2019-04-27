---
title: Azure Batch - miljövariabler beräkningsnod | Microsoft Docs
description: Beräkna nod miljö variabelreferens för Azure Batch-analyser.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/07/2019
ms.author: lahugh
ms.openlocfilehash: 9902f38ddfd3035adcce697c2eb5b77bdc1d8c9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782239"
---
# <a name="azure-batch-compute-node-environment-variables"></a>Azure Batch compute miljövariabler

Den [Azure Batch-tjänsten](https://azure.microsoft.com/services/batch/) anger följande miljövariabler för beräkningsnoder. Du kan referera till dessa miljövariabler i aktivitetens kommandorader och i program och skript som körs av kommandoraderna.

Mer information om hur du använder miljövariabler med Batch finns i [miljöinställningar för aktiviteter](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Variabeln synlighet för miljö

Dessa miljövariabler syns bara i samband med den **aktivitetsanvändaren**, användarkonto på noden som en aktivitet körs. Du ser dem *inte* om du [fjärransluter](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) till en beräkningsnod via RDP (Remote Desktop Protocol) eller SSH (Secure Shell) och visar en lista över miljövariablerna. Det beror på att användarkontot som används för fjärranslutning inte är samma som det konto som används av aktiviteten.

## <a name="command-line-expansion-of-environment-variables"></a>Kommandoradsverktyget expansion av miljövariabler

Kommandorader som körs av aktiviteter på compute-noder gör inte körs under ett gränssnitt. Därför kan dessa kommandorader kan inte inbyggt stöd för shell-funktioner, till exempel expandering (Detta omfattar de `PATH`). Om du vill dra nytta av dessa funktioner, måste du **anropar du gränssnittet** på kommandoraden. Till exempel starta `cmd.exe` på Windows-beräkningsnoder eller `/bin/sh` på Linux-noder:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Miljövariabler

| Variabelnamn                     | Beskrivning                                                              | Tillgänglighet | Exempel |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Namnet på Batch-konto som aktiviteten tillhör.                  | Alla aktiviteter.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | URL till Batch-kontot. | Alla aktiviteter. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Ett prefix för alla app-paketet miljövariabler. Om programmet ”Foo” version ”1” har installerats på en pool är miljövariabeln AZ_BATCH_APP_PACKAGE_FOO_1. AZ_BATCH_APP_PACKAGE_FOO_1 pekar på platsen som paketet var laddas ned (en mapp). | Alla aktiviteter med tillhörande app-paketet. Också tillgängligt för alla uppgifter om noden själva har programpaket. | AZ_BATCH_APP_PACKAGE_FOO_1 |
| AZ_BATCH_AUTHENTICATION_TOKEN   | En autentiseringstoken som beviljar åtkomst till en begränsad uppsättning batchåtgärder för tjänsten. Den här miljövariabeln är endast tillgänglig om den [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) anges när den [uppgift läggs till](/rest/api/batchservice/task/add#request-body). Token-värde används i Batch-API: er som autentiseringsuppgifter för att skapa en Batch-klient, till exempel i den [BatchClient.Open() .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Alla aktiviteter. | OAuth2-åtkomsttoken |
| AZ_BATCH_CERTIFICATES_DIR       | En katalog i den [arbetskatalogen] [ files_dirs] i vilka certifikat som lagras för Linux-beräkningsnoder. Observera att den här miljövariabeln inte gäller för Windows-beräkningsnoder.                                                  | Alla aktiviteter.   |  /mnt/batch/Tasks/workitems/batchjob001/Job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Listan över noder som har tilldelats en [flerinstansuppgift] [ multi_instance] i formatet `nodeIP,nodeIP`. | Flera instanser primära och underaktiviteter. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Anger om den aktuella noden är huvudnoden för ett [flerinstansuppgift][multi_instance]. Möjliga värden är `true` och `false`.| Flera instanser primära och underaktiviteter. | `true` |
| AZ_BATCH_JOB_ID                 | ID:t för jobbet som aktiviteten hör till. | Alla aktiviteter förutom startaktivitet. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Den fullständiga sökvägen till jobbförberedelseuppgiften [aktivitetskatalogen] [ files_dirs] på noden. | Alla aktiviteter utom startaktiviteten och jobbförberedelseaktiviteten. Endast tillgänglig om jobbet har konfigurerats med en jobbförberedelseaktivitet. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Den fullständiga sökvägen till jobbförberedelseuppgiften [arbetskatalogen] [ files_dirs] på noden. | Alla aktiviteter utom startaktiviteten och jobbförberedelseaktiviteten. Endast tillgänglig om jobbet har konfigurerats med en jobbförberedelseaktivitet. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | IP-adressen och porten för Beräkningsnoden där den främsta uppgiften för en [flerinstansuppgift] [ multi_instance] körs. | Flera instanser primära och underaktiviteter. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | ID för den nod som har tilldelats aktiviteten. | Alla aktiviteter. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Om `true`, den aktuella noden är en särskild nod. Om `false`, det är en [med låg prioritet noden](batch-low-pri-vms.md). | Alla aktiviteter. | `true` |
| AZ_BATCH_NODE_LIST              | Listan över noder som har tilldelats en [flerinstansuppgift] [ multi_instance] i formatet `nodeIP;nodeIP`. | Flera instanser primära och underaktiviteter. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_ROOT_DIR          | Den fullständiga sökvägen till roten för alla [Batch-kataloger] [ files_dirs] på noden. | Alla aktiviteter. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Den fullständiga sökvägen till den [delade katalogen] [ files_dirs] på noden. Alla aktiviteter som utförs på en nod har läs-/ skrivbehörighet till den här katalogen. Aktiviteter som utförs på andra noder har inte fjärråtkomst till den här katalogen (det inte är en ”delad” nätverkskatalog). | Alla aktiviteter. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Den fullständiga sökvägen till den [starta aktivitetskatalogen] [ files_dirs] på noden. | Alla aktiviteter. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | ID:t för poolen som aktiviteten körs i. | Alla aktiviteter. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Den fullständiga sökvägen till den [aktivitetskatalogen] [ files_dirs] på noden. Den här katalogen innehåller den `stdout.txt` och `stderr.txt` för aktiviteten och AZ_BATCH_TASK_WORKING_DIR. | Alla aktiviteter. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | ID:t för den aktuella aktiviteten. | Alla aktiviteter förutom startaktivitet. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | En sökväg som är identisk för den primära aktiviteten och varje underaktivitet för en [flerinstansuppgift][multi_instance]. Sökvägen finns på varje nod där flera instanser uppgiften körs och är tillgänglig för uppgift-kommandon som körs på noden skrivbara (både den [samordning kommandot] [ coord_cmd] och [ programmet kommandot][app_cmd]). Underaktiviteter eller en primär aktivitet som utförs på andra noder har inte fjärråtkomst till den här katalogen (det inte är en ”delad” nätverkskatalog). | Flera instanser primära och underaktiviteter. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Den fullständiga sökvägen till den [arbetskatalogen] [ files_dirs] på noden. Pågående aktivitet har läs-/ skrivbehörighet till den här katalogen. | Alla aktiviteter. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Listan över noder och antalet kärnor per nod som har tilldelats en [flerinstansuppgift][multi_instance]. Noder och kärnor anges i formatet `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, där antalet noder som följs av en eller flera noden IP-adresser och antalet kärnor för varje. |  Flera instanser primära och underaktiviteter. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
