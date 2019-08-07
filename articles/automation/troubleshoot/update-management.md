---
title: Felsöka fel med Uppdateringshantering
description: Lär dig hur du felsöker problem med Uppdateringshantering
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 884ded67c25aca78225baef2d7e4c5de1cc94fd0
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782290"
---
# <a name="troubleshooting-issues-with-update-management"></a>Fel sökning av problem med Uppdateringshantering

I den här artikeln beskrivs lösningar för att lösa problem som kan köras i när du använder Uppdateringshantering.

Det finns en agent fel sökare för Hybrid Worker agent för att fastställa det underliggande problemet. Mer information om fel sökaren finns i [Felsöka problem](update-agent-issues.md)med uppdaterings agenten. För alla andra problem, se detaljerad information nedan om möjliga problem.

## <a name="general"></a>Allmänt

### <a name="components-enabled-not-working"></a>Situationen Komponenterna för lösningen Uppdateringshantering har Aktiver ATS och nu konfigureras den här virtuella datorn

#### <a name="issue"></a>Problem

Du kan fortsätta att se följande meddelande på en virtuell dator 15 minuter efter onboarding:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av följande orsaker:

1. Kommunikation tillbaka till Automation-kontot blockeras.
2. Den virtuella dator som har publicerats kan ha kommit från en klonad dator som inte var Sysprep med Microsoft Monitoring Agent installerad.

#### <a name="resolution"></a>Lösning

1. Besök, [nätverks planering](../automation-hybrid-runbook-worker.md#network-planning) för att lära dig om vilka adresser och portar som måste tillåtas för att uppdateringshantering ska fungera.
2. Om du använder en klonad avbildning:
   1. I arbets ytan Log Analytics tar du bort den virtuella datorn från den sparade sökningen efter omfattnings konfigurationen `MicrosoftDefaultScopeConfig-Updates` om den visas. Sparade sökningar hittar du under **Allmänt** på arbets ytan.
   2. Kör `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Kör `Restart-Service HealthService` för att `HealthService`starta om. Detta skapar en ny nyckel och genererar en ny UUID.
   4. Om detta inte fungerar Sysprep-avbildningen först och installerar MMA-agenten efter faktumet.

### <a name="multi-tenant"></a>Situationen Du får ett länkat prenumerations fel när du skapar en uppdaterings distribution för datorer i en annan Azure-klient.

#### <a name="issue"></a>Problem

Du får följande fel meddelande när du försöker skapa en uppdaterings distribution för datorer i en annan Azure-klient:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår när du skapar en uppdaterings distribution som har virtuella Azure-datorer i en annan klient som ingår i en uppdaterings distribution.

#### <a name="resolution"></a>Lösning

Du måste använda följande lösning för att få dem schemalagda. Du kan använda cmdleten [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) med växeln `-ForUpdate` för att skapa ett schema och använda [cmdleten New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) och skicka datorerna i den andra klient till `-NonAzureComputer` parametern. I följande exempel visas ett exempel på hur du gör detta:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Situationen Datorer visas inte i portalen under Uppdateringshantering

#### <a name="issue"></a>Problem

Du kan köra i följande scenarier:

* Datorn **har inte kon figurer ATS** från uppdateringshantering visning av en virtuell dator

* Dina datorer saknas i Uppdateringshanterings visning av ditt Automation-konto

* Du har datorer som visas som **ej utvärderade** under **efterlevnad**, men du ser pulsslags data i Azure Monitor loggar för Hybrid Runbook Worker men inte uppdateringshantering.

#### <a name="cause"></a>Orsak

Detta kan orsakas av potentiella lokala konfigurations problem eller av en felaktigt konfigurerad omfattnings konfiguration.

Hybrid Runbook Worker kan behöva registreras igen och installeras om.

Du kan ha definierat en kvot på din arbets yta som har nåtts och stoppat data från att lagras.

#### <a name="resolution"></a>Lösning

* Se till att din dator rapporterar till rätt arbets yta. Kontrol lera vilken arbets yta som datorn rapporterar till. Instruktioner för hur du verifierar detta finns i [Verifiera agent anslutning till Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Se sedan till att det här är arbets ytan som är länkad till ditt Azure Automation-konto. Bekräfta detta genom att gå till ditt Automation-konto och klicka på **länkad arbets yta** under **relaterade resurser**.

* Kontrol lera att datorerna visas i din Log Analytics-arbetsyta. Kör följande fråga på din Log Analytics arbets yta som är länkad till ditt Automation-konto. Om du inte ser din dator i frågeresultatet, är datorn inte pulsslag, vilket innebär att det förmodligen finns ett lokalt konfigurations problem. Du kan köra fel sökaren för [Windows](update-agent-issues.md#troubleshoot-offline) eller [Linux](update-agent-issues-linux.md#troubleshoot-offline) beroende på vilket operativ system du har, eller så kan du [Installera agenten på nytt](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Om din dator visas i frågeresultatet måste du ange den omfattnings konfiguration som anges i följande punkt.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Sök efter problem med omfattnings konfigurationen. [Omfattnings konfigurationen](../automation-onboard-solutions-from-automation-account.md#scope-configuration) avgör vilka datorer som får konfigureras för lösningen. Om din dator visas i arbets ytan men inte visas måste du konfigurera omfångs konfigurationen så att den passar datorerna. Information om hur du gör detta finns i [onboard Machines på arbets ytan](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Om ovanstående steg inte löser problemet följer du stegen i [distribuera en Windows-hybrid Runbook Worker](../automation-windows-hrw-install.md) för att installera om hybrid Worker för Windows eller [distribuera en Linux-hybrid Runbook Worker](../automation-linux-hrw-install.md) för Linux.

* Kör följande fråga i arbets ytan. Om du ser resultatet `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` har du en kvot som definierats på din arbets yta och har stoppat data från att sparas. I arbets ytan navigerar du till **användning och uppskattade kostnader** > **data volym hantering** och kontrollerar kvoten eller tar bort den kvot som du har.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Om det uppstår problem vid försök att publicera lösningen på en virtuell dator kontrollerar du **Operations Manager** händelse loggen under program- **och tjänst loggar** på den lokala datorn för händelser med händelse-ID **4502** och händelse meddelande innehåller **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

I följande avsnitt beskrivs vissa fel meddelanden och en möjlig lösning för var och en. Mer information om andra onboarding-problem finns i [Felsöka lösning onboarding](onboarding.md).

### <a name="machine-already-registered"></a>Situationen Datorn är redan registrerad på ett annat konto

#### <a name="issue"></a>Problem

Du får följande fel meddelande:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Orsak

Datorn har redan publicerats till en annan arbets yta för Uppdateringshantering.

#### <a name="resolution"></a>Lösning

Utför rensning av gamla artefakter på datorn genom [att ta bort hybrid Runbook-gruppen](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) och försök igen.

### <a name="machine-unable-to-communicate"></a>Situationen Datorn kan inte kommunicera med tjänsten

#### <a name="issue"></a>Problem

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

#### <a name="cause"></a>Orsak

Det kan finnas en proxy, gateway eller brand vägg som blockerar nätverkskommunikation.

#### <a name="resolution"></a>Lösning

Granska nätverket och se till att lämpliga portar och adresser är tillåtna. Se [nätverks krav](../automation-hybrid-runbook-worker.md#network-planning)för en lista över portar och adresser som krävs av uppdateringshantering och hybrid Runbook Worker.

### <a name="unable-to-create-selfsigned-cert"></a>Situationen Det gick inte att skapa ett självsignerat certifikat

#### <a name="issue"></a>Problem

Du får ett av följande fel meddelanden:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Orsak

Det Hybrid Runbook Worker inte att skapa ett självsignerat certifikat

#### <a name="resolution"></a>Lösning

Kontrol lera att system kontot har Läs behörighet till mappen **C:\ProgramData\Microsoft\Crypto\RSA** och försök igen.

### <a name="failed-to-start"></a>Situationen En dator visar att det inte gick att starta en uppdaterings distribution

#### <a name="issue"></a>Problem

En dator har statusen **kunde inte starta** för en dator. När du visar detaljerad information för datorn visas följande fel:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Orsak

Felet kan bero på någon av följande orsaker:

* Datorn finns inte längre.
* Datorn är inaktive rad och kan inte kontaktas.
* Datorn har ett problem med nätverks anslutningen och hybrid Worker på datorn kan inte kontaktas.
* En uppdatering har gjorts till Microsoft Monitoring Agent som ändrade SourceComputerId
* Uppdaterings körningen kan ha begränsats om du träffar gränsen 2 000 för samtidiga jobb i ett Automation-konto. Varje distribution betraktas som ett jobb och varje dator i en uppdaterings distribution räknas som ett jobb. Alla andra automatiserings jobb eller uppdaterings distributioner som körs i ditt Automation-konto räknas till gränsen för samtidiga jobb.

#### <a name="resolution"></a>Lösning

Använd [dynamiska grupper](../automation-update-management.md#using-dynamic-groups) för dina uppdaterings distributioner när det är tillämpligt.

* Kontrol lera att datorn fortfarande finns och att den kan kontaktas. Om den inte finns redigerar du distributionen och tar bort datorn.
* Se avsnittet om [nätverks planering](../automation-update-management.md#ports) för en lista över portar och adresser som krävs för att uppdateringshantering och kontrol lera att datorn uppfyller dessa krav.
* Kör följande fråga i Log Analytics för att hitta datorer i din miljö vars `SourceComputerId` ändrade. Leta efter datorer som har samma `Computer` värde, men ett annat `SourceComputerId` värde. När du har hittat de berörda datorerna måste du redigera de uppdaterings distributioner som är riktade till dessa datorer och ta bort och lägga till datorerna på nytt så att det `SourceComputerId` motsvarar rätt värde.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Situationen Datorn visas som ej utvärderad och visar ett HResult-undantag

#### <a name="issue"></a>Problem

Du har datorer som visas som **ej utvärderade** under **kompatibilitet**och du ser ett undantags meddelande nedan.

#### <a name="cause"></a>Orsak

Windows Update eller WSUS har inte kon figurer ATS korrekt på datorn. Uppdateringshantering förlitar sig på Windows Update eller WSUS för att tillhandahålla de uppdateringar som behövs, status för korrigeringen och resultaten av de uppdateringar som har distribuerats. Utan den här informationen Uppdateringshantering kan inte rapportera om de korrigeringar som behövs eller har installerats på rätt sätt.

#### <a name="resolution"></a>Lösning

Dubbelklicka på det undantag som visas i rött för att se hela undantags meddelandet. Granska följande tabell för potentiella lösningar eller åtgärder som du kan vidta:

|Undantag  |Lösning eller åtgärd  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Sök efter mer information om orsaken till undantaget i listan med fel kod i [Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) -felkoden.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Felen är problem med nätverks anslutningen. Kontrol lera att datorn har rätt nätverks anslutning till Uppdateringshantering. Se avsnittet om [nätverks planering](../automation-update-management.md#ports) för en lista över portar och adresser som krävs.        |
|`0x8024001E`| Uppdaterings åtgärden slutfördes inte på grund av att tjänsten eller systemet stängdes av.|
|`0x8024002E`| Windows Updates tjänsten är inaktive rad.|
|`0x8024402C`     | Om du använder en WSUS-server kontrollerar du att register värden för `WUServer` och `WUStatusServer` under register nyckeln `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` har rätt WSUS-server.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Kontrol lera att Windows Updates tjänsten (wuauserv) körs och inte är inaktive rad.        |
|Alla andra generiska undantag     | Gör en sökning på Internet efter möjliga lösningar och arbeta med din lokala IT-support.         |

`windowsupdate.log` Genom att granska kan du försöka fastställa den möjliga orsaken också. Mer information om hur du läser loggen finns i [så här läser du filen windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Dessutom kan du hämta och köra [Windows Update fel sökare](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) för att kontrol lera om det finns några problem med Windows Update på datorn.

> [!NOTE]
> [Windows Update fel sökaren](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) anger att den är för Windows-klienter, men fungerar även på Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenario: Uppdaterings körningen kan inte startas

#### <a name="issue"></a>Problem

Det går inte att starta en uppdatering på en Linux-dator.

#### <a name="cause"></a>Orsak

Linux-Hybrid Workeren är inte felfri.

#### <a name="resolution"></a>Lösning

Gör en kopia av följande loggfil och behåll den i fel söknings syfte:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenario: Uppdaterings körningen startar, men påträffar fel

#### <a name="issue"></a>Problem

En uppdaterings körning startar, men påträffar fel under körningen.

#### <a name="cause"></a>Orsak

Möjliga orsaker kan vara:

* Paket hanteraren är inte felfri
* Vissa paket kan störa Cloud-baserad uppdatering
* Andra orsaker

#### <a name="resolution"></a>Lösning

Om fel inträffar när en uppdatering körs när den har startats på Linux, kontrollerar du jobbets utdata från den berörda datorn i körningen. Du kan hitta fel meddelanden från din dators paket hanterare som du kan utforska och vidta åtgärder för. Uppdateringshantering kräver att paket hanteraren är felfri för lyckade uppdaterings distributioner.

I vissa fall kan paket uppdateringar störa Uppdateringshantering som förhindrar att en uppdaterings distribution slutförs. Om du ser detta måste du antingen undanta dessa paket från framtida uppdaterings körningar eller installera dem manuellt.

Om du inte kan lösa ett uppdaterings problem gör du en kopia av följande loggfil och bevarar den **innan** nästa uppdaterings distribution startar i fel söknings syfte:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

### <a name="other"></a>Situationen Mitt problem visas inte ovan

### <a name="issue"></a>Problem

Du har ett problem som inte löses av de andra scenarierna i listan.

### <a name="cause"></a>Orsak

Felkonfigurerade eller saknade register nycklar kan orsaka problem med Uppdateringshantering.

### <a name="resolution"></a>Lösning

Ta bort register nyckeln `HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker` och starta om **HealthService**.

Du kan också använda följande PowerShell-kommandon.

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
