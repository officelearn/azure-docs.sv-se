---
title: Felsöka fel med Azure Uppdateringshantering
description: Lär dig hur du felsöker och löser problem med Uppdateringshantering-lösningen i Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5ee1a20d4a3c46cab484b03b5fcc212a79d19047
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513277"
---
# <a name="troubleshooting-issues-with-update-management"></a>Fel sökning av problem med Uppdateringshantering

I den här artikeln beskrivs lösningar på problem som kan uppstå när du använder Uppdateringshantering.

Det finns en agent fel sökare för Hybrid Worker agenten för att fastställa det underliggande problemet. Mer information om fel sökaren finns i [Felsöka problem med uppdaterings agenten](update-agent-issues.md). Använd följande fel söknings vägledning för alla andra problem.

Om du stöter på problem när du försöker publicera lösningen på en virtuell dator (VM) kontrollerar du **Operations Manager** loggen under **program-och tjänst loggar** på den lokala datorn efter händelser med händelse-ID 4502 och händelse information som innehåller **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

I följande avsnitt beskrivs de olika fel meddelandena och möjliga lösningar för var och en. Information om andra onboarding-problem finns i [Felsöka lösning onboarding](onboarding.md).

## <a name="nologs"></a>Scenario: datorer visas inte i portalen under Uppdateringshantering

### <a name="issue"></a>Problem

Följande symptom uppstår:

* Datorn visas **inte** i uppdateringshantering visning av en virtuell dator.

* Datorerna saknas i Uppdateringshanterings visning av ditt Azure Automation-konto.

* Du har datorer som visas som **ej utvärderade** under **efterlevnad**. Men du ser pulsslags data i Azure Monitor loggar för Hybrid Runbook Worker men inte för Uppdateringshantering.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av lokala konfigurations problem eller av en felaktigt konfigurerad omfattnings konfiguration.

Du kanske måste registrera om och installera om Hybrid Runbook Worker.

Du kan ha definierat en kvot på din arbets yta som har nåtts och som förhindrar ytterligare data lagring.

### <a name="resolution"></a>Upplösning

* Kör fel sökaren för [Windows](update-agent-issues.md#troubleshoot-offline) eller [Linux](update-agent-issues-linux.md#troubleshoot-offline), beroende på vilket operativ system du har.

* Kontrol lera att datorn rapporterar till rätt arbets yta. Anvisningar för hur du verifierar den här aspekten finns i [Verifiera agent anslutning till Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Se också till att den här arbets ytan är länkad till ditt Azure Automation-konto. Bekräfta genom att gå till ditt Automation-konto och välja **länkad arbets yta** under **relaterade resurser**.

* Kontrol lera att datorerna visas i din Log Analytics-arbetsyta. Kör följande fråga på arbets ytan Log Analytics som är länkad till ditt Automation-konto:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Om du inte ser din dator i frågeresultatet har den inte nyligen checkats in, vilket innebär att det finns ett lokalt konfigurations problem och du bör [installera om agenten](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Om din dator visas i frågeresultaten måste du kontrol lera den omfattnings konfiguration som anges i nästa punkt objekt i den här listan.

* Sök efter problem med omfattnings konfigurationen. [Omfattnings konfigurationen](../automation-onboard-solutions-from-automation-account.md#scope-configuration) avgör vilka datorer som får konfigureras för lösningen. Om din dator visas i arbets ytan men inte i **uppdateringshantering** -portalen, måste du konfigurera omfattnings konfigurationen så att den är riktad mot datorerna. Information om hur du gör detta finns i [onboard Machines på arbets ytan](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Kör följande fråga i arbets ytan:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Om du får ett `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` resultat finns en kvot definierad på din arbets yta som har nåtts och som har stoppat data från att sparas. I arbets ytan går du till **användning och beräknade kostnader** > **data volym hantering** och kontrollerar kvoten eller tar bort den.

* Om de här stegen inte löser problemet följer du stegen i [distribuera en Windows-hybrid Runbook Worker](../automation-windows-hrw-install.md) för att installera om hybrid Worker för Windows. Eller, för Linux, [distribuerar du en Linux-hybrid Runbook Worker](../automation-linux-hrw-install.md).

## <a name="rp-register"></a>Scenario: det går inte att registrera Automation Resource Provider för prenumerationer

### <a name="issue"></a>Problem

När du arbetar med lösningar i ditt Automation-konto uppstår följande fel:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Orsak

Den automatiska resurs leverantören är inte registrerad i prenumerationen.

### <a name="resolution"></a>Upplösning

Registrera automatiserings resurs leverantören genom att följa de här stegen i Azure Portal:

1. I listan Azure-tjänster längst ned i portalen väljer du **alla tjänster**och väljer sedan **prenumerationer** i gruppen allmän tjänst.
2. Välj din prenumeration.
3. Under **Inställningar**väljer du **resurs leverantörer**.
4. Kontrol lera att **Microsoft. Automation** Resource Provider är registrerad i listan över resurs leverantörer.
5. Om den inte visas registrerar du **Microsoft. Automation** -providern genom att följa stegen i [lösa fel för registrering av resurs leverantör](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="components-enabled-not-working"></a>Scenario: komponenterna för Uppdateringshantering-lösningen har Aktiver ATS och nu konfigureras den här virtuella datorn

### <a name="issue"></a>Problem

Du kan fortsätta att se följande meddelande på en virtuell dator 15 minuter efter onboarding:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Orsak

Det här felet kan inträffa av följande orsaker:

- Kommunikation med Automation-kontot blockeras.
- Den virtuella dator som har publicerats kan ha kommit från en klonad dator som inte är Sysprep med Microsoft Monitoring Agent (MMA) installerad.

### <a name="resolution"></a>Upplösning

1. Gå till [nätverks planering](../automation-hybrid-runbook-worker.md#network-planning) och lär dig om vilka adresser och portar som måste tillåtas för att uppdateringshantering ska fungera.
2. Om du använder en klonad avbildning:
   1. I arbets ytan Log Analytics tar du bort den virtuella datorn från den sparade sökningen efter `MicrosoftDefaultScopeConfig-Updates` omfattnings konfigurationen om den visas. Sparade sökningar hittar du under **Allmänt** på arbets ytan.
   2. Kör `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`.
   3. Kör `Restart-Service HealthService` för att starta om `HealthService`. Detta återskapar nyckeln och genererar en ny UUID.
   4. Om den här metoden inte fungerar kör du Sysprep på avbildningen först och installerar sedan MMA.

## <a name="multi-tenant"></a>Scenario: du får ett länkat prenumerations fel när du skapar en uppdaterings distribution för datorer i en annan Azure-klient

### <a name="issue"></a>Problem

Följande fel uppstår när du försöker skapa en uppdaterings distribution för datorer i en annan Azure-klient:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Orsak

Det här felet uppstår när du skapar en uppdaterings distribution med virtuella Azure-datorer i en annan klient som ingår i en uppdaterings distribution.

### <a name="resolution"></a>Upplösning

Använd följande lösning för att få de här objekten schemalagda. Du kan använda cmdleten [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) med växeln `-ForUpdate` för att skapa ett schema. Använd sedan cmdleten [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) och skicka datorerna i den andra klienten till `-NonAzureComputer`-parametern. I följande exempel visas hur du gör detta:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Scenario: avklarande omstarter

### <a name="issue"></a>Problem

Även om du har ställt in alternativet **Starta** om för att **aldrig starta om**, startar datorerna fortfarande om när uppdateringarna har installerats.

### <a name="cause"></a>Orsak

Windows Update kan ändras av flera register nycklar, vilken som helst kan ändra omstarts beteendet.

### <a name="resolution"></a>Upplösning

Granska register nycklarna under [Konfigurera automatiska uppdateringar genom att redigera register](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) -och [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) för att kontrol lera att datorerna är korrekt konfigurerade.

## <a name="failed-to-start"></a>Scenario: datorn visar att det inte gick att starta i en uppdaterings distribution

### <a name="issue"></a>Problem

En dator visar statusen **kunde inte startas** . När du visar detaljerad information för datorn visas följande fel:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Orsak

Felet kan uppstå på grund av någon av följande orsaker:

* Datorn finns inte längre.
* Datorn är inaktive rad och kan inte kontaktas.
* Datorn har ett problem med nätverks anslutningen och det går därför inte att komma åt hybrid Worker på datorn.
* En uppdatering har gjorts till MMA som ändrade SourceComputerId.
* Uppdaterings körningen begränsades om du träffar gränsen på 2 000 samtidiga jobb i ett Automation-konto. Varje distribution betraktas som ett jobb, och varje dator i en uppdaterings distribution räknas som ett jobb. Alla andra automatiserings jobb eller uppdaterings distributioner som körs i ditt Automation-konto räknas mot gränsen för samtidiga jobb.

### <a name="resolution"></a>Upplösning

Använd [dynamiska grupper](../automation-update-management-groups.md) för dina uppdaterings distributioner när det är tillämpligt. Dessutom:

* Kontrol lera att datorn fortfarande finns och att den kan kontaktas. Om den inte finns redigerar du distributionen och tar bort datorn.
* Se avsnittet [nätverks planering](../automation-update-management.md#ports) för en lista över portar och adresser som krävs för uppdateringshantering och kontrol lera att datorn uppfyller dessa krav.
* Kör följande fråga i Log Analytics för att hitta datorer i din miljö vars `SourceComputerId` har ändrats. Sök efter datorer som har samma `Computer` värde, men ett annat `SourceComputerId` värde. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   När du hittar berörda datorer redigerar du de uppdaterings distributioner som är riktade mot de datorerna och tar sedan bort och lägger till dem på nytt så att `SourceComputerId` visar rätt värde.

## <a name="updates-nodeployment"></a>Scenario: uppdateringar installeras utan distribution

### <a name="issue"></a>Problem

När du registrerar en Windows-dator i Uppdateringshantering visas uppdateringar som installerats utan en distribution.

### <a name="cause"></a>Orsak

I Windows installeras uppdateringar automatiskt så snart de är tillgängliga. Det här beteendet kan orsaka förvirring om du inte schemalägger en uppdatering så att den distribueras till datorn.

### <a name="resolution"></a>Upplösning

Register nyckeln `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` har standardinställningen 4: **Hämta och installera automatiskt**.

För Uppdateringshantering-klienter rekommenderar vi att du anger den här nyckeln till 3: **Ladda ned automatiskt men installera inte automatiskt**.

Mer information finns i [Konfigurera automatiska uppdateringar](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Scenario: datorn har redan registrerats till ett annat konto

### <a name="issue"></a>Problem

Du får följande fel meddelande:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Orsak

Datorn har redan publicerats till en annan arbets yta för Uppdateringshantering.

### <a name="resolution"></a>Upplösning

1. Följ stegen under [datorer som inte visas i portalen under uppdateringshantering](#nologs) för att kontrol lera att datorn rapporterar till rätt arbets yta.
2. Rensa gamla artefakter på datorn genom [att ta bort hybrid Runbook-gruppen](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)och försök sedan igen.

## <a name="machine-unable-to-communicate"></a>Scenario: datorn kan inte kommunicera med tjänsten

### <a name="issue"></a>Problem

Du får ett av följande fel meddelanden:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Orsak

En proxy, gateway eller brand vägg kan blockera nätverkskommunikation. 

### <a name="resolution"></a>Upplösning

Granska nätverket och kontrol lera att rätt portar och adresser är tillåtna. Se [nätverks krav](../automation-hybrid-runbook-worker.md#network-planning) för en lista över portar och adresser som krävs av uppdateringshantering och hybrid Runbook Worker.

## <a name="unable-to-create-selfsigned-cert"></a>Scenario: det går inte att skapa ett självsignerat certifikat

### <a name="issue"></a>Problem

Du får ett av följande fel meddelanden:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Orsak

Det gick inte att skapa ett självsignerat certifikat i Hybrid Runbook Worker.

### <a name="resolution"></a>Upplösning

Kontrol lera att system-kontot har Läs behörighet till mappen **C:\ProgramData\Microsoft\Crypto\RSA** och försök igen.

## <a name="mw-exceeded"></a>Scenario: den schemalagda uppdateringen misslyckades med ett MaintenanceWindowExceeded-fel

### <a name="issue"></a>Problem

Standard underhålls perioden för uppdateringar är 120 minuter. Du kan öka underhålls perioden till högst 6 timmar eller 360 minuter.

### <a name="resolution"></a>Upplösning

Redigera eventuella misslyckade schemalagda uppdaterings distributioner och öka underhålls perioden.

Mer information om underhålls perioder finns i [Installera uppdateringar](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Scenario: datorn visas som "inte utvärderad" och visar ett HResult-undantag

### <a name="issue"></a>Problem

* Du har datorer som visas som **ej utvärderade** under **kompatibilitet**och du ser ett undantags meddelande nedan.
* Du har datorer som visas som ej utvärderade.
* Du ser en HRESULT-felkod i portalen.

### <a name="cause"></a>Orsak

Uppdaterings agenten (Windows Update agenten i Windows, paket hanteraren för en Linux-distribution) är inte korrekt konfigurerad. Uppdateringshantering är beroende av datorns uppdaterings agent för att tillhandahålla de uppdateringar som behövs, status för korrigeringen och resultaten av distribuerade uppdateringar. Utan den här informationen kan Uppdateringshantering inte korrekt rapportera om de korrigeringar som behövs eller är installerade.

### <a name="resolution"></a>Upplösning

Försök att utföra uppdateringar lokalt på datorn. Om detta Miss lyckas betyder det vanligt vis att det finns ett konfigurations fel med uppdaterings agenten.

Det här problemet orsakas ofta av nätverks konfiguration och brand Väggs problem. Prova följande:

* För Linux kontrollerar du lämplig dokumentation för att se till att du kan komma åt nätverks slut punkten för din paket lagrings plats.
* För Windows kontrollerar du att agent konfigurationen som anges i [uppdateringar inte laddas ned från intranät slut punkten (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Om datorerna har kon figurer ATS för Windows Update kontrollerar du att du kan komma åt slut punkterna som beskrivs i [problem som rör http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Om datorerna är konfigurerade för Windows Server Update Services (WSUS) kontrollerar du att du kan ansluta till WSUS-servern som kon figurer ATS av [register nyckeln WUServer principvärdet](/windows/deployment/update/waas-wu-settings).

Om du ser ett HRESULT dubbelklickar du på undantaget som visas i rött för att se hela undantags meddelandet. Granska följande tabell för möjliga lösningar eller rekommenderade åtgärder:

|Undantag  |Lösning eller åtgärd  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Sök i fel kod listan i [Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) om du vill ha mer information om orsaken till undantaget.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Detta indikerar problem med nätverks anslutningen. Kontrol lera att datorn har nätverks anslutning till Uppdateringshantering. Se avsnittet [nätverks planering](../automation-update-management.md#ports) för en lista över nödvändiga portar och adresser.        |
|`0x8024001E`| Uppdaterings åtgärden slutfördes inte på grund av att tjänsten eller systemet stängdes av.|
|`0x8024002E`| Windows Updates tjänsten är inaktive rad.|
|`0x8024402C`     | Om du använder en WSUS-server kontrollerar du att register värden för `WUServer` och `WUStatusServer` under register nyckeln `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` anger rätt WSUS-server.        |
|`0x80072EE2`|Det finns ett problem med nätverks anslutningen eller ett problem med att prata med en konfigurerad WSUS-server. Kontrol lera inställningarna för WSUS och se till att tjänsten är tillgänglig från klienten.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Kontrol lera att Windows Updates tjänsten (wuauserv) körs och inte inaktive rad.        |
|`0x80070005`| Ett fel vid nekad åtkomst kan orsakas av något av följande:<br> Infekterad dator<br> Windows Update inställningarna har inte kon figurer ATS korrekt<br> Fil behörighets fel med mappen%WinDir%\SoftwareDistribution<br> Det finns inte tillräckligt med disk utrymme på system enheten (C:).
|Alla andra generiska undantag     | Kör en sökning på Internet för möjliga lösningar och arbeta med din lokala IT-support.         |

Att granska%Windir%\Windowsupdate.log-filen kan också hjälpa dig att avgöra möjliga orsaker. Mer information om hur du läser loggen finns i [så här läser du filen windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Du kan också hämta och köra [Windows Update fel sökaren](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) för att söka efter problem med Windows Update på datorn.

> [!NOTE]
> [Windows Update fel söknings](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) dokumentationen visar att den används på Windows-klienter, men fungerar även på Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: uppdaterings körning returnerar status "misslyckades" (Linux)

### <a name="issue"></a>Problem

En uppdaterings körning startar men påträffar fel under körningen.

### <a name="cause"></a>Orsak

Möjliga orsaker:

* Paket hanteraren är inte felfri.
* Uppdaterings agent (WUA för Windows, distribution Package Manager för Linux) är felkonfigurerad.
* Vissa paket stör molnbaserad uppdatering.
* Datorn kan inte kontaktas.
* Uppdateringar hade beroenden som inte lösts.

### <a name="resolution"></a>Upplösning

Om fel inträffar när en uppdatering körs när den har startats, [kontrollerar du jobbets utdata](../manage-update-multi.md#view-results-of-an-update-deployment) från den berörda datorn i körnings processen. Du kan hitta vissa fel meddelanden från dina datorer som du kan söka efter och vidta åtgärder för. Uppdateringshantering kräver att paket hanteraren är felfri för lyckade uppdaterings distributioner.

Om vissa korrigeringar, paket eller uppdateringar visas omedelbart innan jobbet Miss lyckas kan du prova att [utesluta](../automation-tutorial-update-management.md#schedule-an-update-deployment) dem från nästa uppdaterings distribution. Information om hur du samlar in logg information från Windows Update finns i [Windows Update loggfiler](/windows/deployment/update/windows-update-logs).

Om du inte kan lösa ett uppdaterings problem gör du en kopia av följande loggfil och bevarar den i fel söknings syfte *innan* nästa uppdaterings distribution startar:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Korrigeringsfiler har inte installerats

### <a name="machines-dont-install-updates"></a>Datorer installerar inte uppdateringar

* Försök att köra uppdateringar direkt på datorn. Om datorn inte kan tillämpa uppdateringarna läser du [listan över eventuella fel i fel söknings guiden](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Om uppdateringarna körs lokalt kan du försöka ta bort och installera om agenten på datorn genom att följa anvisningarna i [ta bort en virtuell dator från uppdateringshantering](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Jag vet att uppdateringar är tillgängliga, men de visas inte som tillgängliga på mina datorer

* Detta händer ofta om datorerna har kon figurer ATS för att hämta uppdateringar från WSUS eller Microsoft Endpoint Configuration Manager, men WSUS och Configuration Manager inte har godkänt uppdateringarna.
* Du kan kontrol lera om datorerna har kon figurer ATS för WSUS och SCCM genom [att referera till register nyckeln UseWUServer till register nycklarna i avsnittet "Konfigurera automatiska uppdateringar genom att redigera registret" i den här artikeln](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).
* Om uppdateringar inte är godkända i WSUS installeras de inte. Du kan söka efter ej godkända uppdateringar i Log Analytics genom att köra följande fråga:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Uppdateringar visas som installerade, men jag kan inte hitta dem på datorn

* Uppdateringar ersätts ofta av andra uppdateringar. Mer information finns i [Uppdatera ersätts](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) i Windows Update fel söknings guide.

### <a name="installing-updates-by-classification-on-linux"></a>Installera uppdateringar efter klassificering i Linux

* Distributionen av uppdateringar till Linux efter klassificering (”kritiska uppdateringar och säkerhetsuppdateringar”) har viktiga förbehåll, särskilt för CentOS. Dessa begränsningar beskrivs på [sidan uppdateringshantering översikt](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 saknas konsekvent

* KB2267602 är [Windows Defender-definitionsuppdateringen](https://www.microsoft.com/wdsi/definitions). Den uppdateras dagligen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport), det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
