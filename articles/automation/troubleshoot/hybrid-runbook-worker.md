---
title: Felsökning – Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information som felsökning av Azure Automation Hybrid Runbook Worker
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 12/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0ec099e0f210fc267a0a34f76136a517e0ae6ccc
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744525"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Felsöka Hybrid Runbook Worker

Den här artikeln innehåller information om felsökning av problem med Hybrid Runbook Worker.

## <a name="general"></a>Allmänt

Hybrid Runbook Worker är beroende av en agent för att kommunicera med ditt Automation-konto för att registrera worker, ta emot runbook-jobb och rapportera status. För Windows är den här agenten Microsoft Monitoring Agent. För Linux är det OMS-agenten för Linux.

### <a name="runbook-execution-fails"></a>Scenario: Runbook-körning misslyckas

#### <a name="issue"></a>Problem

Runbook-körning inte och följande felmeddelande:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Din runbook pausas strax efter att den försöker köra det tre gånger. Det finns villkor som kan avbryta runbook från att slutföras. När detta sker får relaterade felmeddelandet inte innehålla ytterligare information som talar om varför.

#### <a name="cause"></a>Orsak

Här följer möjliga orsaker:

* Runbooks inte kan autentiseras med lokala resurser

* Hybrid worker finns bakom en proxyserver eller brandvägg

* Runbooks inte kan autentiseras med lokala resurser

* Att datorn konfigureras för att köra funktionen Hybrid Runbook Worker uppfyller inte minimikraven på maskinvara.

#### <a name="resolution"></a>Lösning

Kontrollera att datorn har utgående åtkomst till *.azure automation.net på port 443.

Datorer som kör Hybrid Runbook Worker ska uppfylla minimikraven för maskinvara innan den är konfigurerad att vara värd för den här funktionen. Runbooks och bakgrundsprocesser som de använder kan orsaka att systemet kan vara överbelastad och orsaka förseningar för runbook-jobb eller tidsgränser.

Bekräfta att den dator som ska köra Hybrid Runbook Worker-funktionen uppfyller minimikraven på maskinvara. I annat fall använda övervaka CPU och minne för att fastställa alla samband mellan prestanda för Hybrid Runbook Worker-processer och Windows. Om minne eller hög CPU-belastning kan detta tyda på att uppgradera resurser. Du kan också välja en annan beräkningsresurs som stöder de minimikrav och skala när arbetsbelastning visar en ökning krävs.

Kontrollera den **Microsoft SMA** händelseloggen för motsvarande händelse med beskrivningen *Win32 processen avslutades med koden [4294967295]*. Orsaken till felet är du inte har konfigurerat autentisering i dina runbooks eller angivna kör som-autentiseringsuppgifterna för Hybrid worker-gruppen. Granska [Runbook-behörigheter](../automation-hrw-run-runbooks.md#runbook-permissions) att bekräfta att du har korrekt konfigurerat autentisering för dina runbooks.

### <a name="no-cert-found"></a>Scenario: Inget certifikat hittades i certifikatarkivet på Hybrid Runbook Worker

#### <a name="issue"></a>Problem

En runbook som körs på en Hybrid Runbook Worker misslyckas med följande felmeddelande visas:

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Orsak

Det här felet uppstår när du försöker använda en [kör som-konto](../manage-runas-account.md) i en runbook som körs på en Hybrid Runbook Worker där certifikatet för Kör som-konto inte är tillgänglig. Hybrid Runbook Worker har inte certifikattillgången lokalt som standard, vilket krävs av kör som-kontot ska fungera korrekt.

#### <a name="resolution"></a>Lösning

Om din Hybrid Runbook Worker är en Azure-dator, kan du använda [hanterade identiteter för Azure-resurser](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) i stället. Det här scenariot kan du autentisera till Azure-resurser med Azure VM hanterad identitet i stället för Kör som-kontot, vilket förenklar autentisering. När Hybrid Runbook Worker är en lokal dator kan behöva du installera certifikatet för Kör som-konto på datorn. Om du vill lära dig mer om att installera certifikatet, se hur du kör den [Export RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script) runbook.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker beror på OMS-agenten för Linux för att kommunicera med ditt Automation-konto att registrera worker, ta emot runbook-jobb och rapportera status. Om registreringen av arbetaren misslyckas följer här några tänkbara orsaker till felet:

### <a name="oms-agent-not-running"></a>Scenario: OMS-agenten för Linux körs inte

#### <a name="issue"></a>Problem

OMS-agenten för Linux körs inte

#### <a name="cause"></a>Orsak

Om OMS-agenten för Linux inte körs förhindras Linux Hybrid Runbook Worker från att kommunicera med Azure Automation. Agenten kan inte köras av olika anledningar.

#### <a name="resolution"></a>Lösning

 Verifiera agenten körs genom att ange följande kommando: `ps -ef | grep python`. Du bör se utdata som liknar följande, python-processer med **nxautomation** användarkonto. Om hantering av uppdateringar eller Azure Automation-lösningar inte är aktiverad, körs ingen av följande processer.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

I följande lista visas de processer som startas för en Linux Hybrid Runbook Worker. De alla finns i den `/var/opt/microsoft/omsagent/state/automationworker/` directory.


* **OMS.conf** – det här värdet är manager arbetsprocessen. Den startas direkt från DSC.

* **Worker.conf** – den här processen är automatiskt registrerad Hybrid arbetsprocessen, den startas av worker-hanteraren. Den här processen används av hantering av uppdateringar och är transparent för användaren. Den här processen är inte tillgänglig om lösningen för uppdateringshantering inte är aktiverad på datorn.

* **diy/Worker.conf** – den här processen är gör det själv hybrid worker-processen. Arbetsprocessen gör det själv hybrid används för att köra runbooks för användaren på den Hybrid Runbook Worker. Det endast skiljer sig från automatiskt registrerad Hybrid worker-processen i de viktiga detaljer som använder en annan konfiguration. Den här processen är inte tillgänglig om Azure Automation-lösningen är inaktiverad och gör det själv Linux Hybrid Worker är inte registrerad.

Om OMS-agenten för Linux inte körs, kör följande kommando för att starta tjänsten: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scenario: Den angivna klassen finns inte

Om du ser felet: **Den angivna klassen finns inte...** i den `/var/opt/microsoft/omsconfig/omsconfig.log` och OMS-agenten för Linux behöver uppdateras. Kör följande kommando för att installera om OMS-agenten:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker är beroende av Microsoft Monitoring Agent att kommunicera med ditt Automation-konto för att registrera worker, ta emot runbook-jobb och rapportera status. Om registreringen av arbetaren misslyckas följer här några tänkbara orsaker till felet:

### <a name="mma-not-running"></a>Scenario: Microsoft Monitoring Agent körs inte

#### <a name="issue"></a>Problem

Den `healthservice` tjänst inte körs på den Hybrid Runbook Worker-datorn.

#### <a name="cause"></a>Orsak

Om Microsoft Monitoring Agent-Windows-tjänst inte körs, hindrar det här tillståndet Hybrid Runbook Worker för att kommunicera med Azure Automation.

#### <a name="resolution"></a>Lösning

Verifiera agenten körs genom att ange följande kommando i PowerShell: `Get-Service healthservice`. Om tjänsten stoppas, anger du följande kommando i PowerShell för att starta tjänsten: `Start-Service healthservice`.

### <a name="event-4502"></a> Händelsen 4502 i Operations Manager-loggen

#### <a name="issue"></a>Problem

I den **program- och tjänstloggar\operations Manager** händelseloggen, som du ser händelse 4502 och EventMessage som innehåller **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**med följande beskrivning: *Certifikatet som presenterades av tjänsten \<wsid\>. oms.opinsights.azure.com kunde inte utfärdas av en certifikatutfärdare som används för Microsoft-tjänster. Kontakta nätverksadministratören för att se om de kör en proxy som hindrar TLS/SSL-kommunikation. Artikeln KB3126513 innehåller ytterligare felsökningsinformation för anslutningsproblem.*

#### <a name="cause"></a>Orsak

Det här problemet kan orsakas av dina proxy- eller brandvägg som blockerar kommunikation med Microsoft Azure. Kontrollera att datorn har utgående åtkomst till *.azure automation.net på port 443.

#### <a name="resolution"></a>Lösning

Loggar lagras lokalt på varje hybrid worker på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Du kan kontrollera om det finns några varnings- eller händelser i den **program och tjänster Logs\Microsoft-SMA\Operations** och **program- och tjänstloggar\operations Manager** händelseloggen som skulle Ange en anslutning eller andra problem som påverkar onboarding av rollen i Azure Automation eller problemet medan under normal drift.

[Runbook-utdata och meddelanden](../automation-runbook-output-and-messages.md) skickas till Azure Automation från hybrid Worker-arbeten precis som runbook-jobb som körs i molnet. Du kan också aktivera utförlig och förlopp strömmar på samma sätt som för andra runbooks.

### <a name="corrupt-cache"></a> Hybrid Runbook Worker som inte rapporterar

#### <a name="issue"></a>Problem

Hybrid Runbook Worker-datorn körs men du ser inte pulsslag data för datorn i arbetsytan.

Följande exempelfråga visar datorerna i en arbetsyta och deras senaste pulsslag:

```loganalytics
// Last heartbeat of each computer
Heartbeat 
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Orsak

Det här problemet kan orsakas av en skadad cache på den Hybrid Runbook Worker.

#### <a name="resolution"></a>Lösning

Logga in på den Hybrid Runbook Worker och kör följande skript för att lösa problemet. Det här skriptet stoppas Microsoft Monitoring Agent, tar bort sin cache och startar om tjänsten. Den här åtgärden tvingar Hybrid Runbook Worker ladda ned konfigurationen från Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.

