---
title: Fel sökning – Azure Automation hybrid Runbook Worker
description: Den här artikeln innehåller information om fel sökning Azure Automation hybrid Runbook Worker
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 137623e4c52d24061aec8ec11fca0fc02ca54c7f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372512"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Felsöka hybrid Runbook Worker

Den här artikeln innehåller information om fel sökning av problem med hybrid Runbook Worker.

## <a name="general"></a>Allmänt

Hybrid Runbook Worker är beroende av en agent för att kommunicera med ditt Automation-konto för att registrera arbetaren, ta emot Runbook-jobb och rapportera status. För Windows är den här agenten Log Analytics agent för Windows, även kallat Microsoft Monitoring Agent (MMA). För Linux är det Log Analytics-agenten för Linux.

### <a name="runbook-execution-fails"></a>Scenario: Runbook-körningen Miss lyckas

#### <a name="issue"></a>Problem

Runbook-körningen Miss lyckas och följande fel meddelande visas.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Din Runbook har pausats strax efter det att den försökt köras tre gånger. Det finns villkor som kan avbryta Runbook-flödet från att slutföras. Det relaterade fel meddelandet kanske inte innehåller ytterligare information.

#### <a name="cause"></a>Orsak

Följande är möjliga orsaker:

* Runbooks kan inte autentiseras med lokala resurser.

* Hybrid Worker ligger bakom en proxy eller brand vägg.

* Datorn som kon figurer ATS för att köra Hybrid Runbook Worker-funktionen uppfyller inte minimi kraven för maskin vara.

#### <a name="resolution"></a>Lösning

Kontrol lera att datorn har utgående åtkomst till *. azure-automation.net på port 443.

Datorer som kör Hybrid Runbook Worker bör uppfylla minimi kraven för maskin vara innan arbets tagaren har kon figurer ATS för att vara värd för den här funktionen. Runbooks och bakgrunds processen som de använder kan orsaka att systemet överanvänds och orsakar fördröjningar eller tids gränser för Runbook-jobbet.

Bekräfta att datorn som ska köra Hybrid Runbook Worker funktionen uppfyller minimi kraven för maskin vara. Om det gör det kan du övervaka processor-och minnes användning för att fastställa eventuella korrelationer mellan prestandan för Hybrid Runbook Worker processer och Windows. Eventuell minnes-eller processor belastning kan indikera att du behöver uppgradera resurser. Du kan också välja en annan beräknings resurs som uppfyller minimi kraven och skala när arbets belastningen kräver att en ökning är nödvändig.

Kontrol lera händelse loggen för **Microsoft-SMA** för att se om en motsvarande händelse har slutförts med en beskrivning av *Win32-processen med koden [4294967295]* . Orsaken till det här felet är att du inte har konfigurerat autentisering i dina runbooks eller angett kör som-autentiseringsuppgifterna för Hybrid Runbook Worker gruppen. Granska Runbook-behörigheter i [köra Runbooks på en hybrid Runbook Worker](../automation-hrw-run-runbooks.md) för att bekräfta att du har konfigurerat autentisering för dina runbooks korrekt.

### <a name="no-cert-found"></a>Scenario: det gick inte att hitta något certifikat i certifikat arkivet på Hybrid Runbook Worker

#### <a name="issue"></a>Problem

En Runbook som körs på en Hybrid Runbook Worker Miss lyckas med följande fel meddelande.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Orsak

Det här felet uppstår när du försöker använda ett [Kör som-konto](../manage-runas-account.md) i en Runbook som körs på en hybrid Runbook Worker där kör som-kontots certifikat saknas. Hybrid Runbook Worker har inte certifikat till gången lokalt som standard, vilket krävs för att kör som-kontot ska fungera korrekt.

#### <a name="resolution"></a>Lösning

Om din Hybrid Runbook Worker är en virtuell Azure-dator kan du i stället använda [hanterade identiteter för Azure-resurser](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Det här scenariot fören klar autentiseringen genom att du kan autentisera till Azure-resurser med hjälp av den hanterade identiteten för den virtuella Azure-datorn i stället för kör som-kontot När Hybrid Runbook Worker är en lokal dator måste du installera kör som-användarkontot på datorn. Information om hur du installerar certifikatet finns i stegen för att köra PowerShell Runbook export-RunAsCertificateToHybridWorker i [köra Runbooks på en hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="error-403-on-registration"></a>Scenario: fel 403 vid registrering av Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Arbetarens inledande registrerings fas Miss lyckas och du får följande fel (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Orsak

Följande är möjliga orsaker:
* Det finns ett felangett arbetsyte-ID eller en nyckel för arbets ytan (primär) i agentens inställningar. 
* Hybrid Runbook Worker kan inte ladda ned konfigurationen, vilket leder till ett konto länknings fel. När Azure aktiverar lösningar stöder den bara vissa regioner för länkning av en Log Analytics-arbetsyta och ett Automation-konto. Det är också möjligt att ett felaktigt datum och/eller tid har angetts på datorn. Om tiden är +/-15 minuter från den aktuella tiden, Miss lyckas onboarding.

#### <a name="resolution"></a>Lösning

##### <a name="mistyped-workspace-idkey"></a>Felangett arbetsyte-ID/nyckel
För att kontrol lera att agentens arbetsyte-ID eller arbets ytans nyckel har skrivits in, se [lägga till eller ta bort en arbets yta – Windows-agent](../../azure-monitor/platform/agent-manage.md#windows-agent) för Windows-agenten eller [lägga till eller ta bort en arbets yta – Linux-Agent](../../azure-monitor/platform/agent-manage.md#linux-agent) för Linux-agenten.  Se till att välja hela strängen från Azure Portal och kopiera och klistra in den noggrant.

##### <a name="configuration-not-downloaded"></a>Konfigurationen laddas inte ned

Din Log Analytics arbets yta och Automation-konto måste vara i en länkad region. En lista över regioner som stöds finns i [Azure Automation och Log Analytics mappningar för arbets ytor](../how-to/region-mappings.md).

Du kan också behöva uppdatera datum och eller tidszon på datorn. Om du väljer ett anpassat tidsintervall kontrollerar du att intervallet är UTC, vilket kan skilja sig från den lokala tids zonen.

## <a name="linux"></a>Linux

Linux-Hybrid Runbook Worker är beroende av [Log Analytics-agenten för Linux](../../azure-monitor/platform/log-analytics-agent.md) för att kunna kommunicera med ditt Automation-konto för att registrera arbetaren, ta emot Runbook-jobb och rapportera status. Om registreringen av arbets processen Miss lyckas kan du göra det här för att göra något av följande:

### <a name="oms-agent-not-running"></a>Scenario: Log Analytics agent för Linux körs inte

#### <a name="issue"></a>Problem

Log Analytics agenten för Linux körs inte

#### <a name="cause"></a>Orsak

Om agenten inte körs förhindrar Linux-Hybrid Runbook Worker att kommunicera med Azure Automation. Agenten kanske inte körs av olika orsaker.

#### <a name="resolution"></a>Lösning

 Kontrol lera att agenten körs genom att ange följande kommando: `ps -ef | grep python`. Du bör se utdata som liknar följande, python-processerna med **nxautomation** -användarkonto. Om Uppdateringshantering-eller Azure Automation-lösningarna inte är aktiverade körs ingen av följande processer.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

I följande lista visas de processer som startas för en Linux-Hybrid Runbook Worker. De finns i `/var/opt/microsoft/omsagent/state/automationworker/`s katalogen.

* **OMS. conf** – Worker Manager-processen. Den startas direkt från DSC.

* **Worker. conf** – den automatiskt registrerade hybrid Worker-processen. Den startas av Worker Manager. Den här processen används av Uppdateringshantering och är transparent för användaren. Den här processen är inte tillgänglig om Uppdateringshantering-lösningen inte är aktive rad på datorn.

* **gör det själv/Worker. conf** – gör det själv hybrid Worker-processen. GÖR det själv hybrid Worker-processen används för att köra användar-Runbooks på Hybrid Runbook Worker. Det skiljer sig bara från den automatiskt registrerade hybrid Worker-processen i den nyckel information som den använder en annan konfiguration. Den här processen är inte tillgänglig om Azure Automation-lösningen är inaktive rad och gör det själv Linux Hybrid Worker inte är registrerad.

Om agenten inte körs kör du följande kommando för att starta tjänsten: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scenario: den angivna klassen finns inte

Om du ser felet finns **inte den angivna klassen...** Log Analytics-agenten för Linux måste uppdateras i `/var/opt/microsoft/omsconfig/omsconfig.log`. Kör följande kommando för att installera om agenten:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker är beroende av [Log Analytics-agenten för att Windows](../../azure-monitor/platform/log-analytics-agent.md) ska kunna kommunicera med ditt Automation-konto för att registrera arbetaren, ta emot Runbook-jobb och rapportera status. Om registreringen av arbets processen Miss lyckas kan du göra det här för att göra något av följande:

### <a name="mma-not-running"></a>Scenario: Microsoft Monitoring Agent körs inte

#### <a name="issue"></a>Problem

Tjänsten `healthservice` körs inte på den Hybrid Runbook Worker datorn.

#### <a name="cause"></a>Orsak

Om Microsoft Monitoring Agent Microsoft-tjänsten inte körs förhindrar det här läget att Hybrid Runbook Worker kommunicerar med Azure Automation.

#### <a name="resolution"></a>Lösning

Kontrol lera att agenten körs genom att ange följande kommando i PowerShell: `Get-Service healthservice`. Om tjänsten har stoppats anger du följande kommando i PowerShell för att starta tjänsten: `Start-Service healthservice`.

### <a name="event-4502"></a>Scenario: händelse 4502 i Operations Manager logg

#### <a name="issue"></a>Problem

I händelse loggen **program och tjänster loggar \ Operations Manager** visas händelse 4502 och EventMessage som innehåller **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** med följande beskrivning: *certifikatet som presenteras av tjänsten \<wsid\>. OMS.OpInsights.Azure.com har inte utfärdats av en certifikat utfärdare som används för Microsoft-tjänster. Kontakta nätverks administratören för att se om de kör en proxy som fångar upp TLS/SSL-kommunikation.*

#### <a name="cause"></a>Orsak

Det här problemet kan bero på att proxyn eller nätverks brand väggen blockerar kommunikationen till Microsoft Azure. Kontrol lera att datorn har utgående åtkomst till *. azure-automation.net på portarna 443. 

#### <a name="resolution"></a>Lösning

Loggar lagras lokalt på varje hybrid Worker på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Du kan kontrol lera om det finns några varnings-eller fel händelser i händelse loggen **program-och tjänst Logs\Microsoft-SMA\Operations** och **program-och tjänst loggar \ Operations Manager** som indikerar en anslutning eller ett annat problem som påverkar onboarding av rollen för att Azure Automation eller problem vid normal drift. Mer hjälp fel söknings problem med Log Analytics agent finns i [Felsöka problem med Log Analytics Windows-agenten](../../azure-monitor/platform/agent-windows-troubleshoot.md).

[Runbook-utdata och meddelanden](../automation-runbook-output-and-messages.md) skickas till Azure Automation från hybrid Worker precis som Runbook-jobb som körs i molnet. Du kan också aktivera utförlig och pågående strömmar på samma sätt som för andra Runbooks.

### <a name="corrupt-cache"></a>Scenario: Hybrid Runbook Worker inte rapportering

#### <a name="issue"></a>Problem

Din Hybrid Runbook Worker dator körs, men inga pulsslags data visas för datorn i arbets ytan.

I följande exempel fråga visas datorerna i en arbets yta och deras senaste pulsslag:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Orsak

Det här problemet kan orsakas av ett skadat cacheminne på Hybrid Runbook Worker.

#### <a name="resolution"></a>Lösning

Lös problemet genom att logga in på Hybrid Runbook Worker och köra följande skript. Det här skriptet stoppar Microsoft Monitoring Agent, tar bort dess cacheminne och startar om tjänsten. Den här åtgärden tvingar Hybrid Runbook Worker att hämta om konfigurationen från Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Scenario: du kan inte lägga till en Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Följande meddelande visas när du försöker lägga till en Hybrid Runbook Worker med hjälp av `Add-HybridRunbookWorker`-cmdleten.

```error
Machine is already registered
```

#### <a name="cause"></a>Orsak

Det här problemet kan uppstå om datorn redan har registrerats med ett annat Automation-konto eller om du försöker läsa Hybrid Runbook Worker efter att ha tagit bort den från en dator.

#### <a name="resolution"></a>Lösning

Lös problemet genom att ta bort följande register nyckel och starta om `HealthService` och försök `Add-HybridRunbookWorker` cmdlet igen:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.