---
title: Miljövariabler för aktivitetskörning – Azure Batch | Microsoft-dokument
description: Vägledning och referens för variabelt Azure Batch Analytics för aktivitetskörningsmiljö.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/12/2019
ms.author: labrenne
ms.openlocfilehash: ebaa06acf309a0f941b8b4efd76fa4e9e7460810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053955"
---
# <a name="azure-batch-runtime-environment-variables"></a>Azure Batch-miljövariabler

[Azure Batch-tjänsten](https://azure.microsoft.com/services/batch/) anger följande miljövariabler på beräkningsnoder. Du kan referera till dessa miljövariabler i aktivitetskommandorader och i de program och skript som körs av kommandoraderna.

Mer information om hur du använder miljövariabler med Batch finns i [Miljöinställningar för aktiviteter](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Synlighet för miljövariabel

Dessa miljövariabler visas endast i samband med **uppgiftsanvändaren,** användarkontot på den nod under vilken en uppgift utförs. Du ser dem *inte* om du [fjärransluter](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) till en beräkningsnod via RDP (Remote Desktop Protocol) eller SSH (Secure Shell) och visar en lista över miljövariablerna. Det beror på att användarkontot som används för fjärranslutning inte är samma som det konto som används av aktiviteten.

Om du vill hämta det aktuella värdet för en miljövariabel startar du `cmd.exe` på en Windows-beräkningsnod eller `/bin/sh` på en Linux-nod:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Kommandoradsutbyggnad av miljövariabler

Kommandoraderna som körs av uppgifter på beräkningsnoder körs inte under ett skal. Därför kan dessa kommandorader inte internt dra nytta av skalfunktioner `PATH`som miljövariabelexpansion (detta inkluderar ). Om du vill dra nytta av sådana funktioner måste du **anropa skalet** på kommandoraden. Till exempel `cmd.exe` starta på Windows-beräkningsnoder eller `/bin/sh` linuxnoder:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Miljövariabler

| Variabelnamn                     | Beskrivning                                                              | Tillgänglighet | Exempel |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Namnet på det batchkonto som aktiviteten tillhör.                  | Alla uppgifter.   | mybatchaccount (olika) |
| AZ_BATCH_ACCOUNT_URL            | Url:en för batchkontot. | Alla uppgifter. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Ett prefix för alla apppaketmiljövariabler. Om till exempel Application "FOO" version "1" är installerad på en pool, är miljövariabeln AZ_BATCH_APP_PACKAGE_FOO_1 (på Linux) eller AZ_BATCH_APP_PACKAGE_FOO #1 (i Windows). AZ_BATCH_APP_PACKAGE_FOO_1 pekar på den plats där paketet hämtades (en mapp). När du använder standardversionen av appaketet använder du den AZ_BATCH_APP_PACKAGE miljövariabeln utan versionsnummer. Om i Linux, och ansökan paketets namn är "Agent-linux-x64" och versionen är "1.1.46.0, är miljönamnet faktiskt: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, med understreck och gemener. Mer information finns [här](https://docs.microsoft.com/azure/batch/batch-application-packages#execute-the-installed-applications). | Alla uppgifter med ett associerat apppaket. Finns även för alla uppgifter om själva noden har programpaket. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) eller AZ_BATCH_APP_PACKAGE_FOO #1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | En autentiseringstoken som ger åtkomst till en begränsad uppsättning batchtjänståtgärder. Den här miljövariabeln finns bara om [autentiseringtokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) ställs in när [aktiviteten läggs till](/rest/api/batchservice/task/add#request-body). Tokenvärdet används i batch-API:erna som autentiseringsuppgifter för att skapa en batchklient, till exempel i [BatchClient.Open() .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Alla uppgifter. | OAuth2-åtkomsttoken |
| AZ_BATCH_CERTIFICATES_DIR       | En katalog i [uppgiftsarbetskatalogen][files_dirs] där certifikat lagras för Linux-beräkningsnoder. Den här miljövariabeln gäller inte för Windows-beräkningsnoder.                                                  | Alla uppgifter.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Listan över noder som allokeras till en aktivitet `nodeIP,nodeIP`med flera [instanser][multi_instance] i formatet . | Primär- och underaktiviteter med flera instanser. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Anger om den aktuella noden är huvudnoden för en [aktivitet med flera instanser][multi_instance]. Möjliga värden `true` `false`är och .| Primär- och underaktiviteter med flera instanser. | `true` |
| AZ_BATCH_JOB_ID                 | ID:t för jobbet som aktiviteten hör till. | Alla aktiviteter utom startuppgift. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Den fullständiga sökvägen till [uppgiftskatalogen][files_dirs] för jobbförberedelse på noden. | Alla aktiviteter utom startaktivitet och jobbförberedelseaktivitet. Endast tillgängligt om jobbet är konfigurerat med en jobbförberedelseaktivitet. | C:\user\tasks\workitems\jobpreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Den fullständiga sökvägen till jobbförberedelseaktivitetens [arbetskatalog][files_dirs] på noden. | Alla aktiviteter utom startaktivitet och jobbförberedelseaktivitet. Endast tillgängligt om jobbet är konfigurerat med en jobbförberedelseaktivitet. | C:\user\tasks\workitems\jobpreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | IP-adressen och porten för beräkningsnoden som den primära aktiviteten för en [aktivitet med flera instanser][multi_instance] körs. | Primär- och underaktiviteter med flera instanser. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | ID:et för den nod som aktiviteten har tilldelats. | Alla uppgifter. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Om `true`är den aktuella noden en dedikerad nod. Om `false`det är en [nod med låg prioritet](batch-low-pri-vms.md). | Alla uppgifter. | `true` |
| AZ_BATCH_NODE_LIST              | Listan över noder som allokeras till en aktivitet `nodeIP;nodeIP`med flera [instanser][multi_instance] i formatet . | Primär- och underaktiviteter med flera instanser. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Den fullständiga sökvägen till [filsystemmonteringsplatsen](virtual-file-mount.md) för nodnivå där alla monteringskataloger finns. Windows-filresurser använder en enhetsbeteckning, så för Windows är monteringsenheten en del av enheter och enheter.  |  Alla uppgifter inklusive startuppgift har åtkomst till användaren, med tanke på att användaren är medveten om monteringsbehörigheterna för den monterade katalogen. | I Ubuntu, till exempel, är platsen:`/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Den fullständiga sökvägen till roten för alla [batchkataloger][files_dirs] på noden. | Alla uppgifter. | C:\user\uppgifter |
| AZ_BATCH_NODE_SHARED_DIR        | Den fullständiga sökvägen till den [delade katalogen][files_dirs] på noden. Alla uppgifter som körs på en nod har läs-/skrivbehörighet till den här katalogen. Uppgifter som körs på andra noder har inte fjärråtkomst till den här katalogen (det är inte en "delad" nätverkskatalog). | Alla uppgifter. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Den fullständiga sökvägen till [startaktivitetskatalogen][files_dirs] på noden. | Alla uppgifter. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | ID:t för poolen som aktiviteten körs i. | Alla uppgifter. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Den fullständiga sökvägen till [aktivitetskatalogen][files_dirs] på noden. Den här `stdout.txt` katalogen `stderr.txt` innehåller uppgiften och för uppgiften och AZ_BATCH_TASK_WORKING_DIR. | Alla uppgifter. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | ID:t för den aktuella aktiviteten. | Alla aktiviteter utom startuppgift. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | En katalogsökväg som är identisk för den primära aktiviteten och varje underaktivitet i en [aktivitet med flera instanser][multi_instance]. Sökvägen finns på varje nod där multi-instance-aktiviteten körs och är läs-/skriv-tillgänglig för uppgiftskommandon som körs på noden (både [samordningskommandot][coord_cmd] och [programkommandot][app_cmd]). Underaktiviteter eller en primär uppgift som körs på andra noder har inte fjärråtkomst till den här katalogen (det är inte en "delad" nätverkskatalog). | Primär- och underaktiviteter med flera instanser. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Den fullständiga sökvägen till [uppgiftsarbetskatalogen][files_dirs] på noden. Aktiviteten som körs har läs-/skrivåtkomst till den här katalogen. | Alla uppgifter. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Listan över noder och antal kärnor per nod som allokeras till en [aktivitet med flera instanser][multi_instance]. Noder och kärnor visas i formatet`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, där antalet noder följs av en eller flera nod-IP-adresser och antalet kärnor för varje. |  Primär- och underaktiviteter med flera instanser. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
