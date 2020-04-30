---
title: Felsöka Azure Automation hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du felsöker Azure Automation hybrid Runbook Worker.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679340"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Felsöka hybrid Runbook Worker

Den här artikeln innehåller information om fel sökning av problem med hybrid Runbook Worker.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="general"></a>Allmänt

Hybrid Runbook Worker är beroende av en agent för att kommunicera med ditt Automation-konto för att registrera arbetaren, ta emot Runbook-jobb och rapportera status. För Windows är den här agenten Log Analytics agent för Windows. För Linux är det Log Analytics-agenten för Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: Runbook-körningen Miss lyckas

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
* Datorn som kon figurer ATS för att köra Hybrid Runbook Worker uppfyller inte minimi kraven för maskin vara.

#### <a name="resolution"></a>Lösning

Kontrol lera att datorn har utgående åtkomst till ***. Azure-Automation.net** på port 443.

Datorer som kör Hybrid Runbook Worker bör uppfylla minimi kraven för maskin vara innan arbets tagaren har kon figurer ATS för att vara värd för den här funktionen. Runbooks och bakgrunds processen som de använder kan orsaka att systemet överanvänds och orsakar fördröjningar eller tids gränser för Runbook-jobbet.

Bekräfta att datorn som ska köra Hybrid Runbook Worker funktionen uppfyller minimi kraven för maskin vara. Om det gör det kan du övervaka processor-och minnes användning för att fastställa eventuella korrelationer mellan prestandan för Hybrid Runbook Worker processer och Windows. Eventuell minnes-eller processor belastning kan indikera att du behöver uppgradera resurser. Du kan också välja en annan beräknings resurs som uppfyller minimi kraven och skala när arbets belastningen kräver att en ökning är nödvändig.

Kontrol lera händelse loggen för **Microsoft-SMA** för en motsvarande händelse `Win32 Process Exited with code [4294967295]`med beskrivning. Orsaken till det här felet är att du inte har konfigurerat autentisering i dina runbooks eller angett kör som-autentiseringsuppgifterna för Hybrid Runbook Worker gruppen. Granska Runbook-behörigheter i [köra Runbooks på en hybrid Runbook Worker](../automation-hrw-run-runbooks.md) för att bekräfta att du har konfigurerat autentisering för dina runbooks korrekt.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: händelse 15011 i Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Hybrid Runbook Worker tar emot händelse 15011, vilket indikerar att ett frågeresultat är ogiltigt. Följande fel visas när arbets tagaren försöker öppna en anslutning med [signal servern](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Orsak

Hybrid Runbook Worker har inte kon figurer ATS korrekt för den automatiserade distributions lösningen. Den här lösningen innehåller en del som ansluter den virtuella datorn till Log Analytics-arbetsytan. PowerShell-skriptet söker efter arbets ytan i prenumerationen med det angivna namnet. I det här fallet är Log Analytics arbets ytan i en annan prenumeration. Skriptet kan inte hitta arbets ytan och försöker skapa en, men namnet är redan upptaget. Distributionen Miss lyckas därför.

#### <a name="resolution"></a>Lösning

Det finns två alternativ för att lösa problemet:

* Ändra PowerShell-skriptet för att söka efter Log Analytics arbets ytan i en annan prenumeration. Det här är en lämplig lösning om du planerar att distribuera många Hybrid Runbook Worker datorer i framtiden.

* Konfigurera arbets datorn manuellt så att den körs i ett Orchestrator-läge. Kör sedan en Runbook som skapats i Azure Automation kontot på arbets tagaren för att testa funktionen.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: virtuella Windows Azure-datorer som har tagits bort automatiskt från hybrid Worker-gruppen

#### <a name="issue"></a>Problem

Du kan inte se Hybrid Runbook Worker eller virtuella datorer när arbets datorn har inaktiverats under en längre tid.

#### <a name="cause"></a>Orsak

Den Hybrid Runbook Worker datorn har inte Pingat Azure Automation i mer än 30 dagar. Därför har Automation rensat Hybrid Runbook Workers gruppen eller systemets arbets grupp. 

#### <a name="resolution"></a>Lösning

Starta arbets datorn och rereregister den med Azure Automation. Se anvisningar för att installera Runbook-miljön och ansluta till Azure Automation i [distribuera en Windows-hybrid Runbook Worker](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: det gick inte att hitta något certifikat i certifikat arkivet på Hybrid Runbook Worker

#### <a name="issue"></a>Problem

En Runbook som körs på en Hybrid Runbook Worker Miss lyckas med följande fel meddelande.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Orsak

Det här felet uppstår när du försöker använda ett [Kör som-konto](../manage-runas-account.md) i en Runbook som körs på en hybrid Runbook Worker där kör som-konto-certifikatet inte finns. Hybrid Runbook Worker har inte certifikat till gången lokalt som standard. Kör som-kontot kräver att den här till gången fungerar korrekt.

#### <a name="resolution"></a>Lösning

Om din Hybrid Runbook Worker är en virtuell Azure-dator kan du i stället använda [hanterade identiteter för Azure-resurser](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Det här scenariot fören klar autentiseringen genom att du kan autentisera till Azure-resurser med hjälp av den hanterade identiteten för den virtuella Azure-datorn i stället för kör som-kontot När Hybrid Runbook Worker är en lokal dator måste du installera kör som-användarkontot på datorn. Information om hur du installerar certifikatet finns i stegen för att köra PowerShell Runbook **export-RunAsCertificateToHybridWorker** i [köra runbooks på en hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: fel 403 vid registrering av Hybrid Runbook Worker

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

Du kan också behöva uppdatera datum och/eller tidszon på datorn. Om du väljer ett anpassat tidsintervall kontrollerar du att intervallet är UTC, vilket kan skilja sig från den lokala tids zonen.

## <a name="linux"></a>Linux

Linux-Hybrid Runbook Worker är beroende av [Log Analytics-agenten för Linux](../../azure-monitor/platform/log-analytics-agent.md) för att kunna kommunicera med ditt Automation-konto för att registrera arbetaren, ta emot Runbook-jobb och rapportera status. Om registreringen av arbets processen Miss lyckas kan du göra det här för att göra något av följande:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: Log Analytics agent för Linux körs inte

#### <a name="issue"></a>Problem

Log Analytics agenten för Linux körs inte

#### <a name="cause"></a>Orsak

Om agenten inte körs förhindrar Linux-Hybrid Runbook Worker att kommunicera med Azure Automation. Agenten kanske inte körs av olika orsaker.

#### <a name="resolution"></a>Lösning

 Kontrol lera att agenten körs genom att ange `ps -ef | grep python`kommandot. Du bör se utdata som liknar följande, python-processerna med **nxautomation** -användarkonto. Om Uppdateringshantering-eller Azure Automation-lösningen inte är aktive rad körs ingen av följande processer.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

I följande lista visas de processer som startas för en Linux-Hybrid Runbook Worker. De finns i **/var/opt/Microsoft/omsagent/State/automationworker/** -katalogen.

* **OMS. conf** – Worker Manager-processen. Den startas direkt från DSC.

* **Worker. conf** – den automatiskt registrerade hybrid arbets processen. Den startas av Worker Manager. Den här processen används av Uppdateringshantering och är transparent för användaren. Den här processen är inte tillgänglig om Uppdateringshantering-lösningen inte är aktive rad på datorn.

* **gör det själv/Worker. conf** – gör det själv hybrid Worker-processen. GÖR det själv hybrid Worker-processen används för att köra användar-Runbooks på Hybrid Runbook Worker. Det skiljer sig bara från den automatiskt registrerade hybrid Worker-processen i den nyckel information som den använder en annan konfiguration. Den här processen är inte tillgänglig om Azure Automation-lösningen är inaktive rad och gör det själv Linux Hybrid Worker inte är registrerad.

Om agenten inte körs kör du följande kommando för att starta tjänsten: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: den angivna klassen finns inte

Om du ser felet `The specified class does not exist..` i **/var/opt/Microsoft/omsconfig/omsconfig.log**måste Log Analytics-agenten för Linux uppdateras. Kör följande kommando för att installera om agenten:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker är beroende av [Log Analytics-agenten för att Windows](../../azure-monitor/platform/log-analytics-agent.md) ska kunna kommunicera med ditt Automation-konto för att registrera arbetaren, ta emot Runbook-jobb och rapportera status. Om det inte går att registrera arbets tagaren, innehåller det här avsnittet några möjliga orsaker.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenario: Log Analytics agenten för Windows körs inte

#### <a name="issue"></a>Problem

Körs `healthservice` inte på den hybrid Runbook Worker datorn.

#### <a name="cause"></a>Orsak

Om Log Analytics för Windows-tjänsten inte körs kan Hybrid Runbook Worker inte kommunicera med Azure Automation.

#### <a name="resolution"></a>Lösning

Kontrol lera att agenten körs genom att ange följande kommando i PowerShell: `Get-Service healthservice`. Om tjänsten har stoppats anger du följande kommando i PowerShell för att starta tjänsten: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Scenario: händelse 4502 i Operations Manager logg

#### <a name="issue"></a>Problem

I händelse loggen **program-och tjänst loggar \ Operations Manager** visas händelse 4502 och EventMessage som innehåller `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` följande beskrivning:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Orsak

Det här problemet kan bero på att proxyn eller nätverks brand väggen blockerar kommunikationen till Microsoft Azure. Kontrol lera att datorn har utgående åtkomst till ***. Azure-Automation.net** på port 443. 

#### <a name="resolution"></a>Lösning

Loggar lagras lokalt på varje hybrid Worker på **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. Du kan kontrol lera om det finns några varnings-eller fel händelser i händelse loggarna **program och tjänster Logs\Microsoft-SMA\Operations** och **program-och tjänst loggar \ Operations Manager** . Dessa loggar indikerar en anslutning eller någon annan typ av problem som påverkar onboarding av rollen för att Azure Automation, eller ett problem som uppstått under normal drift. Mer hjälp fel söknings problem med Log Analytics agent finns i [Felsöka problem med Log Analytics Windows-agenten](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Hybrid Worker skickar [Runbook-utdata och meddelanden](../automation-runbook-output-and-messages.md) till Azure Automation på samma sätt som Runbook-jobb som körs i molnet skickar utdata och meddelanden. Du kan aktivera utförlig och förlopps strömmar precis som du gör för Runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Scenario: Orchestrator. sandbox. exe kan inte ansluta till Office 365 via proxy

#### <a name="issue"></a>Problem

Ett skript som körs på en Windows-Hybrid Runbook Worker kan inte ansluta som förväntat till Office 365 i ett Orchestrator-läge. Skriptet använder [Connect-MSOLService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) för anslutning. 

Om du ändrar **Orchestrator. sandbox. exe. config** för att ange proxyservern och bypass-listan, ansluts inte sand lådan ändå korrekt. En **Powershell_ise. exe. config** -fil med samma inställningar för proxy och kringgå lista verkar fungera som förväntat. Service Management Automation (SMA) loggar och PowerShell-loggar tillhandahåller inte någon information om proxyn.

#### <a name="cause"></a>Orsak

Anslutningen till Active Directory Federation Services (AD FS) (ADFS) på servern kan inte kringgå proxyn. Kom ihåg att en PowerShell-sandbox körs som den loggade användaren. En Orchestrator-Sandbox är dock mycket anpassad och kan ignorera fil inställningarna för **Orchestrator. sandbox. exe. config** . Den har särskild kod för hantering av inställningar för dator-eller MMA, men inte för hantering av andra anpassade proxyinställningar. 

#### <a name="resolution"></a>Lösning

Du kan lösa problemet för Orchestrator sandbox genom att migrera skriptet till att använda Azure AD-modulerna i stället för MSOnline-modulen för PowerShell-cmdletar. Se [Migrera från Orchestrator till Azure Automation (beta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Om du vill fortsätta att använda MSOnline-modulens cmdlets ändrar du skriptet för att använda [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Ange värden för parametrarna `ComputerName` och `Credential` . 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Den här kod ändringen startar en helt ny PowerShell-session under kontexten för de angivna autentiseringsuppgifterna. Det bör göra det möjligt för trafiken att flöda genom en proxyserver som autentiserar den aktiva användaren.

>[!NOTE]
>Den här lösningen gör det onödigt att ändra sand boxens konfigurations fil. Även om du lyckas att göra konfigurations filen till ditt skript rensas filen varje gång som Hybrid Runbook Worker agenten uppdateras.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: Hybrid Runbook Worker inte rapportering

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

Lös problemet genom att logga in på Hybrid Runbook Worker och köra följande skript. Det här skriptet stoppar Log Analytics agent för Windows, tar bort dess cacheminne och startar om tjänsten. Den här åtgärden tvingar Hybrid Runbook Worker att ladda ned konfigurationen igen från Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: du kan inte lägga till en Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Följande meddelande visas när du försöker lägga till en Hybrid Runbook Worker med hjälp av `Add-HybridRunbookWorker` cmdleten.

```error
Machine is already registered
```

#### <a name="cause"></a>Orsak

Det här problemet kan uppstå om datorn redan har registrerats med ett annat Automation-konto eller om du försöker läsa Hybrid Runbook Worker efter att ha tagit bort den från en dator.

#### <a name="resolution"></a>Lösning

Lös problemet genom att ta bort följande register nyckel, starta om `HealthService`och prova `Add-HybridRunbookWorker` cmdleten igen.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport), det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.