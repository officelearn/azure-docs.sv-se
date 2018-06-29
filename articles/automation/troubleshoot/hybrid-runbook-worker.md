---
title: Felsökning – Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information som felsökning Azure Automation Hybrid Runbook Worker
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cae7253a4bfcb4f83baf003a4d9d3c367d8f014
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064045"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Felsöka Runbook Worker-hybrider

Den här artikeln innehåller information om felsökning av problem med Hybrid Runbook Worker.

## <a name="general"></a>Allmänt

Hybrid Runbook Worker är beroende av en agent för att kommunicera med ditt Automation-konto för att registrera worker, ta emot runbook-jobb och rapportera status. Den här agenten är för Windows, Microsoft Monitoring Agent. Linux är det OMS-Agent för Linux.

###<a name="runbook-execution-fails"></a>Scenario: Runbook-körningen misslyckas

#### <a name="issue"></a>Problem

Runbook-körningen misslyckas och följande felmeddelande:

```
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Din runbook har pausats strax efter att försök att köra den tre gånger. Det finns villkor som kan avbryta runbook slutförs och relaterade felmeddelandet innehåller inte någon ytterligare information som anger varför.

#### <a name="cause"></a>Orsak

Följande är möjliga orsaker:

* Runbooks kan inte autentisera med lokala resurser

* Worker-hybriden finns bakom en proxyserver eller brandvägg

* Runbooks kan inte autentisera med lokala resurser

* Den dator som är tilldelad att köra funktionen Hybrid Runbook Worker uppfyller minimikraven på maskinvara.

#### <a name="resolution"></a>Lösning

Kontrollera att datorn är utgående åtkomst till *.azure automation.net på port 443.

Datorer som kör Runbook Worker-hybriden ska uppfylla minimikraven för maskinvara innan du utser den som värd för den här funktionen. Annars blir överbelastad datorn beroende på resursutnyttjande andra bakgrundsprocesser och konkurrens på grund av runbooks under körning och orsaka fördröjningar för runbook-jobb eller timeout.

Bekräfta att den dator som är tilldelad att köra funktionen Hybrid Runbook Worker uppfyller minimikraven på maskinvara. Om den finns, kan du övervaka användningen av CPU och minne för att fastställa alla korrelation mellan prestanda för Hybrid Runbook Worker-processer och Windows. Om det finns minne eller CPU-belastning, kan detta tyda på att behöva uppgradera eller lägga till fler processorer eller öka minne för att adressera resurs flaskhalsar och åtgärda felet. Du kan också markera en annan beräkningsresurser som stöder de minimikrav och skala när arbetsbelastning indikera att öka krävs.

Kontrollera den **Microsoft SMA** händelseloggen för motsvarande händelse med beskrivning *Win32 processen avslutades med koden [4294967295]*. Orsaken till felet är att du inte har konfigurerat autentisering i dina runbooks eller angivna kör som-autentiseringsuppgifter för Hybrid worker-gruppen. Granska [Runbook-behörigheter](../automation-hrw-run-runbooks.md#runbook-permissions) att bekräfta att du har konfigurerat autentisering korrekt för dina runbooks.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker beror på OMS-Agent för Linux för att kommunicera med ditt Automation-konto för registrering worker, ta emot runbook-jobb och rapportera status. Om registreringen av worker misslyckas, är här några möjliga orsaker till felet:

###<a name="oms-agent-not-running"></a>Scenario: OMS-Agent för Linux körs inte

Om OMS-Agent för Linux inte körs, förhindrar detta Linux Hybrid Runbook Worker från att kommunicera med Azure Automation. Kontrollera att agenten körs genom att ange följande kommando: `ps -ef | grep python`. Du bör se utdata som liknar följande, python-processer med **nxautomation** användarkonto. Inget av följande processer körs om lösningar för hantering av uppdateringar eller Azure Automation inte är aktiverade.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

I följande lista visas de processer som startas för en Linux Hybrid Runbook Worker. De befinner sig i den `/var/opt/microsoft/omsagent/state/automationworker/` directory.

* **OMS.conf** -detta är manager arbetsprocessen, detta är direkt från DSC.

* **Worker.conf** -den här processen är automatiskt registrerad Hybrid arbetsprocessen, den har startats av hanteraren för arbetaren. Den här processen används av hantering av uppdateringar och är transparent för användaren. Den här processen finns inte om uppdateringshantering lösningen inte är aktiverad på datorn.

* **diy/Worker.conf** -den här processen är själv hybrid arbetsprocessen. Arbetsprocessen själv hybrid används för att köra runbooks för användaren på Hybrid Runbook Worker. Den bara skiljer sig från automatisk registrerade Hybrid arbetsprocessen i de viktiga detaljer som använder en annan konfiguration. Den här processen finns inte om Azure Automation-lösningen inte är aktiverat och själv Linux Hybrid Worker har inte registrerats.

Om OMS-Agent för Linux inte körs, kör följande kommando för att starta tjänsten: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

###<a name="class-does-not-exist"></a>Scenario: Den angivna klassen finns inte

Om du ser felet: **finns inte den angivna klassen...** i den `/var/opt/microsoft/omsconfig/omsconfig.log` sedan OMS-Agent för Linux måste uppdateras. Kör följande kommando för att installera om OMS-agenten:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker är beroende av Microsoft Monitoring Agent kan kommunicera med ditt Automation-konto för att registrera worker, ta emot runbook-jobb och rapportera status. Om registreringen av worker misslyckas, är här några möjliga orsaker till felet:

###<a name="mma-not-running"></a>Scenario: Microsoft Monitoring Agent körs inte

#### <a name="issue"></a>Problem

Den `healthservice` tjänsten körs inte på Hybrid Runbook Worker-datorn.

#### <a name="cause"></a>Orsak

Om Microsoft Monitoring Agent Windows-tjänsten inte körs, förhindrar detta Hybrid Runbook Worker från att kommunicera med Azure Automation.

#### <a name="resolution"></a>Lösning

Kontrollera att agenten körs genom att ange följande kommando i PowerShell: `Get-Service healthservice`. Om tjänsten stoppas kan du ange följande kommando i PowerShell för att starta tjänsten: `Start-Service healthservice`.

###<a name="event-4502"></a> Händelsen 4502 i loggen för Operations Manager

#### <a name="issue"></a>Problem

I den **program och tjänster för Logs\Operations** händelseloggen händelse 4502 och EventMessage som innehåller **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**med följande beskrivning: *certifikatet som presenterades av tjänsten \<wsid\>. oms.opinsights.azure.com har inte utfärdats av en certifikatutfärdare som används för Microsoft-tjänster. Kontakta nätverksadministratören för att se om de kör en proxy som hindrar TLS/SSL-kommunikation. Artikeln KB3126513 innehåller ytterligare felsökningsinformation för problem med nätverksanslutningen.*

#### <a name="cause"></a>Orsak

Detta kan bero på att din proxy- eller brandvägg som blockerar kommunikation till Microsoft Azure. Kontrollera att datorn är utgående åtkomst till *.azure automation.net på port 443.

#### <a name="resolution"></a>Lösning

Loggfilerna lagras lokalt på varje worker-hybrid på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Du kan kontrollera om det finns några varning eller felhändelser som sparas i den **program och tjänster Logs\Microsoft-SMA\Operations** och **program och tjänster för Logs\Operations** händelseloggen som Visar att det finns en anslutning eller andra problem som påverkar onboarding av rollen för Azure Automation eller problem under normal drift.

[Runbook-utdata och meddelanden om](../automation-runbook-output-and-messages.md) skickas till Azure Automation från hybrid Worker-arbeten precis som runbook-jobb körs i molnet. Du kan också aktivera utförlig och förlopp strömmar på samma sätt som för andra runbooks.

## <a name="next-steps"></a>Nästa steg

Om du inte fick ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Du kan arkivera en incident i Azure-supporten om du behöver mer hjälp. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
