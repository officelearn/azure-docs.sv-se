---
title: Felsökning – Azure Automation Hybrid Runbook-arbetare
description: Den här artikeln innehåller information om felsökning av Azure Automation Hybrid Runbook Workers.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2587af0ada18b5c4271e7411783fe60211a3479
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637867"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Felsöka hybridkörningsarbetare

Den här artikeln innehåller information om felsökningsproblem med Hybrid Runbook Workers.

## <a name="general"></a>Allmänt

Hybrid Runbook Worker är beroende av att en agent kommunicerar med ditt Automation-konto för att registrera arbetaren, ta emot runbook-jobb och rapportstatus. För Windows är den här agenten Log Analytics-agenten för Windows, även kallad Microsoft Monitoring Agent (MMA). För Linux är det Log Analytics-agenten för Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: Körningen av runbook misslyckas

#### <a name="issue"></a>Problem

Körningen av Runbook misslyckas och följande felmeddelande visas.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbooken avbryts strax efter att den har försökt köras tre gånger. Det finns villkor som kan avbryta runbook från att slutföra. Det relaterade felmeddelandet kanske inte innehåller någon ytterligare information.

#### <a name="cause"></a>Orsak

Följande är möjliga orsaker:

* Runbooks kan inte autentisera med lokala resurser.

* Hybridarbetaren ligger bakom en proxy eller brandvägg.

* Datorn som konfigurerats för att köra hybridkörningsarbetsfunktionen uppfyller inte de lägsta maskinvarukraven.

#### <a name="resolution"></a>Lösning

Kontrollera att datorn har utgående åtkomst till ***.azure-automation.net** på port 443.

Datorer som kör Hybrid Runbook Worker bör uppfylla de lägsta maskinvarukraven innan arbetaren är konfigurerad för att vara värd för den här funktionen. Runbooks och bakgrundsprocessen som de använder kan leda till att systemet används över och orsaka fördröjningar eller tidsfördröjningar av runbook-jobb.

Kontrollera att datorn som ska köra funktionen Hybrid Runbook Worker uppfyller de lägsta maskinvarukraven. Om den gör det övervakar du CPU- och minnesanvändning för att fastställa eventuella samband mellan prestandan för Hybrid Runbook Worker-processer och Windows. Alla minnes- eller CPU-tryck kan tyda på behovet av att uppgradera resurser. Du kan också välja en annan beräkningsresurs som stöder minimikraven och skala när arbetsbelastningskrav indikerar att en ökning är nödvändig.

Kontrollera i **Microsoft-SMA-händelseloggen** för `Win32 Process Exited with code [4294967295]`en motsvarande händelse med beskrivning . Orsaken till det här felet är att du inte har konfigurerat autentisering i runbooks eller angett run as-autentiseringsuppgifterna för hybridkörningsarbetsgruppen. Granska runbook-behörigheter i [Köra runbooks på en Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) för att bekräfta att du har konfigurerat autentiseringen korrekt för dina runbooks.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: Händelse 15011 i Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Hybrid Runbook Worker tar emot händelse 15011, vilket anger att ett frågeresultat inte är giltigt. Följande fel visas när arbetaren försöker öppna en anslutning till [SignalR-servern](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Orsak

Hybrid Runbook Worker har inte konfigurerats korrekt för den automatiska distributionslösningen. Den här lösningen innehåller en del som ansluter den virtuella datorn till log analytics-arbetsytan. PowerShell-skriptet söker efter arbetsytan i prenumerationen med det angivna namnet. I det här fallet finns log analytics-arbetsytan i en annan prenumeration. Skriptet kan inte hitta arbetsytan och försöker skapa en, men namnet är redan taget. Således misslyckas distributionen.

#### <a name="resolution"></a>Lösning

Du har två alternativ för att lösa problemet:

* Ändra PowerShell-skriptet för att leta efter Log Analytics-arbetsytan i en annan prenumeration. Detta är en bra lösning om du planerar att distribuera många Hybrid Runbook Worker-datorer i framtiden.

* Konfigurera arbetardatorn manuellt så att den körs i en Orchestrator-sandlåda. Kör sedan en runbook som skapats i Azure Automation-kontot på arbetaren för att testa funktionen.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: Virtuella windows Azure-datorer tas bort automatiskt från hybridarbetsgruppen

#### <a name="issue"></a>Problem

Du kan inte se Hybrid Runbook Worker eller virtuella datorer när arbetsdatorn har varit avstängd under en längre tid.

#### <a name="cause"></a>Orsak

Hybrid Runbook Worker-datorn har inte pingat Azure Automation på mer än 30 dagar. Därför har Automation rensat hybridkörningsarbetsgruppen eller systemarbetsgruppen. 

#### <a name="resolution"></a>Lösning

Starta arbetsmaskinen och registrera den sedan igen med Azure Automation. Se instruktioner för hur du installerar runbook-miljön och ansluter till Azure Automation i [Distribuera en Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: Inget certifikat hittades i certifikatarkivet på Hybrid Runbook Worker

#### <a name="issue"></a>Problem

En runbook som körs på en Hybrid Runbook Worker misslyckas med följande felmeddelande.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Orsak

Det här felet uppstår när du försöker använda ett [Run As-konto](../manage-runas-account.md) i en runbook som körs på en Hybrid Runbook Worker där kontocertifikatet Kör som inte finns. Hybrid Runbook Workers har inte certifikattillgången lokalt som standard, vilket krävs av kontot Kör som för att fungera korrekt.

#### <a name="resolution"></a>Lösning

Om din Hybrid Runbook Worker är en Virtuell Azure-dator kan du använda [hanterade identiteter för Azure-resurser](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) i stället. Det här scenariot förenklar autentiseringen genom att du kan autentisera till Azure-resurser med den hanterade identiteten för Azure VM i stället för kontot Kör som. När Hybrid Runbook Worker är en lokal dator måste du installera kontocertifikatet Kör som på datorn. Mer information om hur du installerar certifikatet finns i stegen för att köra PowerShell-runbook export-runascertificateToHybridWorker i [Körkörböcker på en Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: Fel 403 under registrering av Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Arbetarens inledande registreringsfas misslyckas och du får följande felmeddelande (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Orsak

Följande är möjliga orsaker:

* Det finns ett felaktigt itypat arbetsyte-ID eller arbetsytenyckel (primär) i agentens inställningar. 

* Hybrid Runbook Worker kan inte hämta konfigurationen, vilket orsakar ett kontolänkningsfel. När Azure aktiverar lösningar stöder det bara vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto. Det är också möjligt att ett felaktigt datum och/eller en felaktig tid är inställt på datorn. Om tiden är +/-15 minuter från den aktuella tiden misslyckas introduktionen.

#### <a name="resolution"></a>Lösning

##### <a name="mistyped-workspace-idkey"></a>Felaktigt itypat arbetsområdes-ID/nyckel
För att kontrollera om agentens arbetsyte-ID eller arbetsytenyckel har skrivit fel, se [Lägga till eller ta bort en arbetsyta – Windows-agent](../../azure-monitor/platform/agent-manage.md#windows-agent) för Windows-agenten eller Lägga till eller ta bort en arbetsyta – [Linux-agent](../../azure-monitor/platform/agent-manage.md#linux-agent) för Linux-agenten.  Se till att välja hela strängen från Azure-portalen och kopiera och klistra in den noggrant.

##### <a name="configuration-not-downloaded"></a>Konfigurationen har inte hämtats

Din Log Analytics-arbetsyta och Automation-konto måste finnas i en länkad region. En lista över regioner som stöds finns i [Azure Automation- och Log Analytics-mappningar](../how-to/region-mappings.md).

Du kan också behöva uppdatera datum och/eller tidszon för datorn. Om du väljer ett anpassat tidsintervall kontrollerar du att intervallet finns i UTC, vilket kan skilja sig från den lokala tidszonen.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker är beroende av [log analytics-agenten för Linux för](../../azure-monitor/platform/log-analytics-agent.md) att kommunicera med ditt Automation-konto för att registrera arbetaren, ta emot runbook-jobb och rapportstatus. Om registreringen av arbetaren misslyckas, här är några möjliga orsaker till felet:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: Log Analytics-agenten för Linux körs inte

#### <a name="issue"></a>Problem

Log Analytics-agenten för Linux körs inte

#### <a name="cause"></a>Orsak

Om agenten inte körs förhindrar det att Linux Hybrid Runbook Worker kommunicerar med Azure Automation. Agenten kanske inte körs av olika skäl.

#### <a name="resolution"></a>Lösning

 Kontrollera att agenten körs genom `ps -ef | grep python`att ange kommandot . Du bör se utdata som liknar följande, Python-processer med **nxautomation** användarkonto. Om lösningen Update Management eller Azure Automation inte är aktiverad körs ingen av följande processer.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Följande lista visar de processer som har startats för en Linux Hybrid Runbook Worker. De finns alla i katalogen **/var/opt/microsoft/omsagent/state/automationworker/.**

* **oms.conf** - Arbetaren chef processen. Det har börjat direkt från DSC.

* **worker.conf** - Den automatiskt registrerade hybridarbetsprocessen. Det har startats av arbetarchefen. Den här processen används av Uppdateringshantering och är transparent för användaren. Den här processen finns inte om lösningen för uppdateringshantering inte är aktiverad på datorn.

* **diy/worker.conf** - DIY hybrid arbetarprocessen. Diy-hybridarbetsprocessen används för att köra användarkörningar på Hybrid Runbook Worker. Den skiljer sig bara från hybridarbetsprocessen för automatiskt registrerade i nyckeldetaljen som används i en annan konfiguration. Den här processen finns inte om Azure Automation-lösningen är inaktiverad och DIY Linux Hybrid Worker inte är registrerad.

Om agenten inte körs kör du följande kommando `sudo /opt/microsoft/omsagent/bin/service_control restart`för att starta tjänsten: .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: Den angivna klassen finns inte

Om felet visas `The specified class does not exist..` i **/var/opt/microsoft/omsconfig/omsconfig.log**måste Log Analytics-agenten för Linux uppdateras. Kör följande kommando för att installera om agenten:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker är beroende av [att Log Analytics-agenten för Windows](../../azure-monitor/platform/log-analytics-agent.md) kommunicerar med ditt Automation-konto för att registrera arbetaren, ta emot runbook-jobb och rapportstatus. Om registreringen av arbetaren misslyckas innehåller det här avsnittet några möjliga orsaker.

### <a name="scenario-the-microsoft-monitoring-agent-isnt-running"></a><a name="mma-not-running"></a>Scenario: Microsoft Monitoring Agent körs inte

#### <a name="issue"></a>Problem

Tjänsten `healthservice` körs inte på hybridkörningsarbetsmaskinen.

#### <a name="cause"></a>Orsak

Om Tjänsten Microsoft Monitoring Agent inte körs hindras Hybrid Runbook Worker från att kommunicera med Azure Automation.

#### <a name="resolution"></a>Lösning

Kontrollera att agenten körs genom att ange följande `Get-Service healthservice`kommando i PowerShell: . Om tjänsten stoppas anger du följande kommando i PowerShell för att starta tjänsten: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Scenario: Händelse 4502 i Operations Manager-loggen

#### <a name="issue"></a>Problem

I händelseloggen **Program och tjänster\Operations Manager** visas händelse 4502 och EventMessage som innehåller `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` följande beskrivning:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Orsak

Det här problemet kan orsakas av att proxy- eller nätverksbrandväggen blockerar kommunikationen till Microsoft Azure. Kontrollera att datorn har utgående åtkomst till ***.azure-automation.net** på port 443. 

#### <a name="resolution"></a>Lösning

Loggar lagras lokalt på varje hybridarbetare på **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. Du kan kontrollera om det finns några varnings- eller felhändelser i **program- och tjänstloggarna\Microsoft-SMA\Operations-** och **program- och tjänstloggar\Operations** Manager-händelseloggar. Dessa loggar anger en anslutning eller annan typ av problem som påverkar introduktion av rollen till Azure Automation, eller ett problem som påträffas under normala åtgärder. Mer hjälp med felsökning av problem med Log Analytics-agenten finns i [Felsöka problem med Log Analytics Windows-agenten](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Hybridarbetare skickar [Runbook-utdata och meddelanden](../automation-runbook-output-and-messages.md) till Azure Automation på samma sätt som runbook-jobb som körs i molnet skickar utdata och meddelanden. Du kan aktivera strömmarna Utför och Förlopp på samma sätt som för runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Scenario: Orchestrator.Sandbox.exe kan inte ansluta till Office 365 via proxy

#### <a name="issue"></a>Problem

Ett skript som körs på en Windows Hybrid Runbook Worker kan inte ansluta som förväntat till Office 365 i en Orchestrator-sandlåda. Skriptet använder [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) för anslutning. 

Om du justerar **Orchestrator.Sandbox.exe.config** för att ställa in proxy- och bypass-listan, ansluter sandlådan fortfarande inte korrekt. En **Powershell_ise.exe.config-fil** med samma proxy- och bypass-listinställningar verkar fungera som förväntat. SMA-loggar (Service Management Automation) och PowerShell-loggar ger ingen information om proxy.

#### <a name="cause"></a>Orsak

Anslutningen till Active Directory Federation Services (ADFS) på servern kan inte kringgå proxyn. Kom ihåg att en PowerShell-sandlåda körs som den inloggade användaren. En Orchestrator-sandlåda är dock kraftigt anpassad och kan ignorera filinställningarna **Orchestrator.Sandbox.exe.config.** Den har särskild kod för hantering maskin eller MMA proxyinställningar, men inte för hantering av andra anpassade proxyinställningar. 

#### <a name="resolution"></a>Lösning

Du kan lösa problemet för Orchestrator-sandlådan genom att migrera skriptet för att använda Azure AD-modulerna i stället för MSOnline-modulen för PowerShell-cmdlets. Se [Migrera från Orchestrator till Azure Automation (Beta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Om du vill fortsätta att använda CMDlets för MSOnline-modulen ändrar du skriptet så att det använder [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Ange värden `ComputerName` för `Credential` parametrarna och. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Den här kodändringen startar en helt ny PowerShell-session under kontexten för de angivna autentiseringsuppgifterna. Det bör göra det möjligt för trafiken att flöda genom en proxyserver som autentiserar den aktiva användaren.

>[!NOTE]
>Den här lösningen gör det onödigt att manipulera sandlådekonfigurationsfilen. Även om du lyckas få konfigurationsfilen att fungera med skriptet raderas filen varje gång Hybrid Runbook Worker-agenten uppdateras.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: Hybrid Runbook Worker rapporterar inte

#### <a name="issue"></a>Problem

Hybrid Runbook Worker-datorn körs men pulsslagsdata för datorn på arbetsytan visas inte.

Följande exempelfråga visar datorerna på en arbetsyta och deras senaste pulsslag:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Orsak

Det här problemet kan orsakas av en skadad cache på Hybrid Runbook Worker.

#### <a name="resolution"></a>Lösning

LÃ¶s problemet genom att logga in på Hybrid Runbook Worker och köra fÃ¶1 fÃ¶1 skriptet. Det här skriptet stoppar Microsoft Monitoring Agent, tar bort cacheminnet och startar om tjänsten. Den här åtgärden tvingar Hybrid Runbook Worker att hämta konfigurationen igen från Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: Du kan inte lägga till en hybridkörningsarbetare

#### <a name="issue"></a>Problem

Du får följande meddelande när du försöker lägga `Add-HybridRunbookWorker` till en Hybrid Runbook Worker med hjälp av cmdlet.

```error
Machine is already registered
```

#### <a name="cause"></a>Orsak

Det här problemet kan uppstå om datorn redan är registrerad med ett annat Automation-konto eller om du försöker läsa Hybrid Runbook Worker när du har tagit bort den från en dator.

#### <a name="resolution"></a>Lösning

LÃ¶s problemet genom att ta bort `HealthService`fÃ¶ljningsã¶kning av fÃ¶ljning av registernyckeln, starta om och försök med cmdlet igen. `Add-HybridRunbookWorker`

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.