---
title: Felsökning – Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information som felsökning av Azure Automation Hybrid Runbook Worker
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 38e2589365c2f1c88145fbf068d3ed267d4a4621
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284582"
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

Din runbook pausas strax efter att du försöker köra det tre gånger. Det finns villkor som kan avbryta runbooken slutförs och relaterade felmeddelandet innehåller inte ytterligare information som anger varför.

#### <a name="cause"></a>Orsak

Här följer möjliga orsaker:

* Runbooks inte kan autentiseras med lokala resurser

* Hybrid worker finns bakom en proxyserver eller brandvägg

* Runbooks inte kan autentiseras med lokala resurser

* Den dator som har utformats för att köra funktionen Hybrid Runbook Worker uppfyller minimikraven på maskinvara.

#### <a name="resolution"></a>Lösning

Kontrollera att datorn har utgående åtkomst till *.azure automation.net på port 443.

Datorer som kör Hybrid Runbook Worker ska uppfylla minimikraven för maskinvara innan du utser den som värd för den här funktionen. Annars blir överbelastad datorn beroende på resursanvändningen av andra bakgrundsprocesser och konkurrensen på grund av runbooks under körning och orsaka förseningar för runbook-jobb eller tidsgränser.

Bekräfta att den dator som har utformats för att köra funktionen Hybrid Runbook Worker uppfyller minimikraven på maskinvara. I annat fall kan du övervaka användning av processor och minne för att fastställa alla samband mellan prestanda för Hybrid Runbook Worker-processer och Windows. Om det finns minne eller hög CPU-belastning, kan detta tyda på behovet av att uppgradera eller lägga till ytterligare processorer eller öka minne för att åtgärda resource flaskhals och lösa problemet. Även välja en annan beräkningsresurs som stöder de minimikrav och skala när arbetsbelastning visar en ökning krävs.

Kontrollera den **Microsoft SMA** händelseloggen för motsvarande händelse med beskrivningen *Win32 processen avslutades med koden [4294967295]*. Orsaken till felet är du inte har konfigurerat autentisering i dina runbooks eller angivna kör som-autentiseringsuppgifterna för Hybrid worker-gruppen. Granska [Runbook-behörigheter](../automation-hrw-run-runbooks.md#runbook-permissions) att bekräfta att du har korrekt konfigurerat autentisering för dina runbooks.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker beror på OMS-agenten för Linux för att kommunicera med ditt Automation-konto att registrera worker, ta emot runbook-jobb och rapportera status. Om registreringen av arbetaren misslyckas följer här några tänkbara orsaker till felet:

### <a name="oms-agent-not-running"></a>Scenario: OMS-agenten för Linux körs inte

Om OMS-agenten för Linux inte körs, hindrar detta Linux Hybrid Runbook Worker för att kommunicera med Azure Automation. Verifiera agenten körs genom att ange följande kommando: `ps -ef | grep python`. Du bör se utdata som liknar följande, python-processer med **nxautomation** användarkonto. Om du inte har aktiverats för uppdateringshantering eller Azure Automation-lösningar, körs inget av följande processer.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

I följande lista visas de processer som startas för en Linux Hybrid Runbook Worker. De befinner sig i den `/var/opt/microsoft/omsagent/state/automationworker/` directory.

* **OMS.conf** – det här är manager arbetsprocessen, detta är igång direkt från DSC.

* **Worker.conf** – den här processen är automatiskt registrerad Hybrid arbetsprocessen, den startas av worker-hanteraren. Den här processen används av hantering av uppdateringar och är transparent för användaren. Den här processen finns inte om lösningen för uppdateringshantering inte är aktiverad på datorn.

* **diy/Worker.conf** – den här processen är gör det själv hybrid worker-processen. Arbetsprocessen gör det själv hybrid används för att köra runbooks för användaren på den Hybrid Runbook Worker. Det endast skiljer sig från automatiskt registrerad Hybrid worker-processen i de viktiga detaljer som använder en annan konfiguration. Den här processen finns inte om Azure Automation-lösningen inte har aktiverats och gör det själv Linux Hybrid Worker har inte registrerats.

Om OMS-agenten för Linux inte körs, kör följande kommando för att starta tjänsten: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scenario: Den angivna klassen finns inte

Om du ser felet: **finns inte den angivna klassen...** i den `/var/opt/microsoft/omsconfig/omsconfig.log` och OMS-agenten för Linux behöver uppdateras. Kör följande kommando för att installera om OMS-agenten:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker är beroende av Microsoft Monitoring Agent att kommunicera med ditt Automation-konto för att registrera worker, ta emot runbook-jobb och rapportera status. Om registreringen av arbetaren misslyckas följer här några tänkbara orsaker till felet:

### <a name="mma-not-running"></a>Scenario: Microsoft Monitoring Agent körs inte

#### <a name="issue"></a>Problem

Den `healthservice` tjänsten körs inte på den Hybrid Runbook Worker-datorn.

#### <a name="cause"></a>Orsak

Om Microsoft Monitoring Agent-Windows-tjänsten inte körs, hindrar detta Hybrid Runbook Worker för att kommunicera med Azure Automation.

#### <a name="resolution"></a>Lösning

Verifiera agenten körs genom att ange följande kommando i PowerShell: `Get-Service healthservice`. Om tjänsten stoppas, anger du följande kommando i PowerShell för att starta tjänsten: `Start-Service healthservice`.

### <a name="event-4502"></a> Händelsen 4502 i Operations Manager-loggen

#### <a name="issue"></a>Problem

I den **program- och tjänstloggar\operations Manager** händelseloggen du ser händelse 4502 och EventMessage som innehåller **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**med följande beskrivning: *certifikatet som presenterades av tjänsten \<wsid\>. oms.opinsights.azure.com kunde inte utfärdas av en certifikatutfärdare som används för Microsoft-tjänster. Kontakta nätverksadministratören för att se om de kör en proxy som hindrar TLS/SSL-kommunikation. Artikeln KB3126513 innehåller ytterligare felsökningsinformation för anslutningsproblem.*

#### <a name="cause"></a>Orsak

Detta kan bero på att din proxy- eller brandvägg som blockerar kommunikation med Microsoft Azure. Kontrollera att datorn har utgående åtkomst till *.azure automation.net på port 443.

#### <a name="resolution"></a>Lösning

Loggar lagras lokalt på varje hybrid worker på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Du kan kontrollera om det finns varningar eller felhändelser som sparas i den **program och tjänster Logs\Microsoft-SMA\Operations** och **program- och tjänstloggar\operations Manager** i händelseloggen som Visar att det finns en anslutning eller något annat problem som påverkar onboarding av rollen för att Azure Automation eller problem när du utför vanliga åtgärder.

[Runbook-utdata och meddelanden](../automation-runbook-output-and-messages.md) skickas till Azure Automation från hybrid Worker-arbeten precis som runbook-jobb som körs i molnet. Du kan också aktivera utförlig och förlopp strömmar på samma sätt som för andra runbooks.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
