---
title: Felsöka fel med Azure Update Management
description: Lär dig hur du felsöker och löser problem med lösningshanteringslösningen för uppdatering i Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c9ff05591c98fda8be39e32f26da484f56e0831b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984631"
---
# <a name="troubleshooting-issues-with-update-management"></a>Felsöka problem med uppdateringshantering

I den här artikeln beskrivs lösningar på problem som kan uppstå när du använder Uppdateringshantering.

Det finns en agentfelsökare för Hybrid Worker-agenten för att fastställa det underliggande problemet. Mer information om felsökaren finns i [Felsöka problem med uppdateringsagenten](update-agent-issues.md). Om du vill ha alla andra problem kan du använda följande felsökningsvägledning.

Om du hittar problem när du kontrollerar lösningen på en virtuell dator (VM) kontrollerar du **Operations Manager-loggen** under **Program- och tjänstloggar** på den lokala datorn. Leta efter händelser med händelse-ID 4502 och händelseinformation som innehåller `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`.

I följande avsnitt beskrivs specifika felmeddelanden och möjliga lösningar för varje. Andra introduktionsproblem finns i [Felsöka lösningsantagning.](onboarding.md)

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Scenario: Felet "Det gick inte att aktivera uppdateringslösningen"

### <a name="issue"></a>Problem

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att aktivera update management-lösningen i ditt Automation-konto visas fÃ¶10fel:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Orsak

Det här felet kan uppstå av följande skäl:

* Kraven på nätverksbrandvägg för Log Analytics-agenten kanske inte är korrekt konfigurerade. Den här situationen kan orsaka att agenten misslyckas när DNS-url:erna löss.

* Lösningsinriktning är felkonfigurerad och datorn får inte uppdateringar som förväntat.

* Du kanske också märker att maskinen `Non-compliant` visar status under **Efterlevnad**. Samtidigt rapporterar **Agent Desktop Analytics** agenten som `Disconnected`.

### <a name="resolution"></a>Lösning

* Kör felsökaren för [Windows](update-agent-issues.md#troubleshoot-offline) eller [Linux](update-agent-issues-linux.md#troubleshoot-offline), beroende på operativsystemet.

* Gå till [Nätverksplanering](../automation-hybrid-runbook-worker.md#network-planning) om du vill veta vilka adresser och portar som måste tillåtas för att uppdateringshantering ska fungera.  

* Gå till [Nätverksplanering](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) om du vill veta vilka adresser och portar som måste tillåtas för att Log Analytics-agenten ska fungera.

* Sök efter problem med scopekonfigurationen. [Scopekonfiguration avgör](../automation-onboard-solutions-from-automation-account.md#scope-configuration) vilka datorer som konfigureras för lösningen. Om datorn visas på arbetsytan men inte i **Update Management Portal måste du ställa in scopekonfigurationen så att den inriktas på datorerna. Mer information om scopekonfigurationen finns [i Inbyggda datorer på arbetsytan](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Ta bort arbetskonfigurationen genom att följa stegen i [Ta bort hybridkörningsarbetaren](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: Ersatt uppdatering som anges saknas i uppdateringshantering

### <a name="issue"></a>Problem

Gamla uppdateringar visas för ett Automation-konto som saknas trots att de har ersatts. En ersatt uppdatering är en som du inte behöver installera eftersom en senare uppdatering som korrigerar samma säkerhetsproblem är tillgängligt. Uppdateringshantering ignorerar den ersatta uppdateringen och gör den inte tillämplig till förmån för den ersatta uppdateringen. Information om ett relaterat problem finns i [Uppdatera ersätts](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Orsak

Ersatta uppdateringar anges inte korrekt som avvisade så att de inte kan anses vara tillämpliga.

### <a name="resolution"></a>Lösning

När en ersatt uppdatering blir 100 procent inte tillämplig bör du ändra `Declined`godkännandetillståndet för den uppdateringen till . Så här ändrar du godkännandetillståndet för alla uppdateringar:

1. I Automation-kontot väljer du **Uppdateringshantering** för att visa datorstatus. Se [Visa uppdateringsutvärderingar](../manage-update-multi.md#view-an-update-assessment).

2. Kontrollera den ersatta uppdateringen för att se till att den är 100 procent inte tillämplig. 

3. Markera uppdateringen som avvisad om du inte har en fråga om uppdateringen. 

4. Välj **Datorer** och tvinga fram en omsökning för efterlevnad i kolumnen **Efterlevnad.** Se [Hantera uppdateringar för flera datorer](../manage-update-multi.md).

5. Upprepa stegen ovan för andra ersatta uppdateringar.

6. Kör rensningsguiden om du vill ta bort filer från de avvisade uppdateringarna. 

7. För WSUS (Windows Server Update Services) rensar du alla ersatta uppdateringar manuellt för att uppdatera infrastrukturen.

8. Upprepa den här proceduren regelbundet för att korrigera visningsproblemet och minimera mängden diskutrymme som används för uppdateringshantering.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: Datorer visas inte i portalen under Uppdateringshantering

### <a name="issue"></a>Problem

Dina maskiner har följande symtom:

* Datorn visas `Not configured` från vyn Uppdateringshantering för en virtuell dator.

* Dina datorer saknas i vyn Uppdateringshantering för ditt Azure Automation-konto.

* Du har maskiner `Not assessed` som visas enligt **Efterlevnad**. Du ser dock pulsslagsdata i Azure Monitor-loggar för Hybrid Runbook Worker men inte för uppdateringshantering.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av lokala konfigurationsproblem eller av felaktigt konfigurerad scopekonfiguration. Möjliga specifika orsaker är:

* Du kanske måste registrera om och installera om Hybrid Runbook Worker.

* Du kanske har definierat en kvot på arbetsytan som har nåtts och som förhindrar ytterligare datalagring.

### <a name="resolution"></a>Lösning

1. Kör felsökaren för [Windows](update-agent-issues.md#troubleshoot-offline) eller [Linux](update-agent-issues-linux.md#troubleshoot-offline), beroende på operativsystemet.

2. Kontrollera att datorn rapporterar till rätt arbetsyta. Mer information om hur du verifierar den här aspekten finns i [Verifiera agentanslutning till Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Kontrollera också att den här arbetsytan är länkad till ditt Azure Automation-konto. Bekräfta genom att gå till ditt Automation-konto och välja **Länkad arbetsyta** under **Relaterade resurser**.

3. Kontrollera att datorerna visas på arbetsytan Log Analytics som är länkad till ditt Automation-konto. Kör följande fråga på arbetsytan Logganalys.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Om du inte ser datorn i frågeresultatet har den inte checkat in nyligen. Det finns förmodligen ett lokalt konfigurationsproblem och du bör [installera om agenten](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). 

5. Om datorn visas i frågeresultaten kontrollerar du om det finns problem med scopekonfigurationen. [Scopekonfigurationen](../automation-onboard-solutions-from-automation-account.md#scope-configuration) avgör vilka datorer som är konfigurerade för lösningen. 

6. Om datorn visas på arbetsytan men inte i Uppdateringshantering måste du konfigurera scopekonfigurationen så att den inriktas på datorn. Mer information om hur du gör detta finns [i Inbyggda datorer på arbetsytan](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

7. Kör den här frågan på arbetsytan.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Om du `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` får ett resultat har den kvot som definierats på arbetsytan uppnåtts, vilket har hindrat data från att sparas. På arbetsytan går du till **datavolymhantering** under **Användning och uppskattade kostnader**och ändrar eller tar bort kvoten.

9. Om problemet fortfarande är olöst följer du anvisningarna i [Distribuera en Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) för att installera om Hybrid Worker för Windows. För Linux följer du stegen i [Distribuera en Linux Hybrid Runbook Worker](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: Det går inte att registrera Automation-resursprovidern för prenumerationer

### <a name="issue"></a>Problem

NÃ¤r du arbetar med lösningar i ditt Automation-konto uppstÃ¥r följande fel:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Orsak

Resursleverantören Automation är inte registrerad i prenumerationen.

### <a name="resolution"></a>Lösning

Så här registrerar du resursleverantören Automation i Azure-portalen.

1. I Azure-tjänstlistan längst ned i portalen väljer du **Alla tjänster**och väljer sedan **Prenumerationer** i den allmänna tjänstgruppen.

2. Välj din prenumeration.

3. Under **Inställningar**väljer du **Resursleverantörer**.

4. Kontrollera att Microsoft.Automation-resursleverantören är registrerad i listan över resursleverantörer.

5. Om det inte finns med i listan registrerar du Microsoft.Automation-providern genom att följa stegen vid [Lös fel för registrering av resursleverantörer](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenario: Schemalagd uppdatering med ett dynamiskt schema missade vissa datorer

### <a name="issue"></a>Problem

Datorer som ingår i en förhandsgranskning av uppdateringar visas inte alla i listan över datorer som korrigerats under en schemalagd körning.

### <a name="cause"></a>Orsak

Det här problemet kan ha någon av följande orsaker:

* Prenumerationerna som definieras i omfånget i en dynamisk fråga är inte konfigurerade för den registrerade Automation-resursprovidern.

* Datorerna var inte tillgängliga eller hade inte lämpliga taggar när schemat kördes.

### <a name="resolution"></a>Lösning

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Prenumerationer som inte konfigurerats för registrerad Automation-resursprovider

Om din prenumeration inte är konfigurerad för Automation-resursleverantören kan du inte fråga eller hämta information om datorer i den prenumerationen. Följ följande steg för att verifiera registreringen för prenumerationen.

1. Öppna Azure-tjänstlistan i [Azure-portalen.](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)

2. Välj **Alla tjänster**och välj sedan **Prenumerationer** i den allmänna servicegruppen. 

3. Hitta prenumerationen som definierats i omfattningen för distributionen.

4. Under **Inställningar**väljer du **Resursleverantörer**.

5. Kontrollera att Microsoft.Automation-resursprovidern är registrerad.

6. Om det inte finns med i listan registrerar du Microsoft.Automation-providern genom att följa stegen vid [Lös fel för registrering av resursleverantörer](/azure/azure-resource-manager/resource-manager-register-provider-errors).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Maskiner som inte är tillgängliga eller inte taggade korrekt när schemat körs

Använd följande procedur om din prenumeration är konfigurerad för Automation-resursprovidern, men om du kör uppdateringsschemat med de angivna [dynamiska grupperna](../automation-update-management-groups.md) missade vissa datorer.

1. Öppna Automation-kontot i Azure-portalen och välj **Uppdateringshantering**.

2. Kontrollera [uppdateringshanteringshistoriken](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) för att fastställa den exakta tiden när uppdateringsdistributionen kördes. 

3. För datorer som du misstänker har missats av Uppdateringshantering använder du Azure Resource Graph (ARG) för att [hitta datorändringar](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details). 

4. Sök efter ändringar under en längre tid, till exempel en dag, innan uppdateringsdistributionen kördes.

5. Kontrollera om det finns några systemändringar, till exempel ta bort eller uppdatera ändringar, till datorerna under den här perioden. Dessa ändringar kan ändra maskinens status eller taggar så att datorer inte väljs i datorlistan när uppdateringar distribueras.

6. Justera maskinerna och resursinställningarna efter behov för att korrigera för maskinstatus eller taggproblem.

7. Kör uppdateringsschemat igen för att säkerställa att distributionen med de angivna dynamiska grupperna innehåller alla datorer.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Scenario: Förväntade datorer visas inte i förhandsversion för dynamisk grupp

### <a name="issue"></a>Problem

Virtuella datorer för valda scope för en dynamisk grupp visas inte i förhandslistan för Azure Portal. Den här listan består av alla datorer som hämtas av en ARG-fråga för de valda scopea. Scopen filtreras för datorer som har Hybrid Runbook Workers installerade och som du har åtkomstbehörighet för. 

### <a name="cause"></a>Orsak
 
Här är möjliga orsaker till det här problemet:

* Du har inte rätt åtkomst på de valda scopea.
* ARG-frågan hämtar inte de förväntade datorerna.
* Hybrid Runbook Worker är inte installerat på datorerna.

### <a name="resolution"></a>Lösning 

#### <a name="incorrect-access-on-selected-scopes"></a>Felaktig åtkomst för valda scope

Azure-portalen visar bara datorer som du har skrivbehörighet för i ett visst omfång. Om du inte har rätt åtkomst för ett scope läser du [Självstudiekurs: Bevilja en användare åtkomst till Azure-resurser med RBAC och Azure-portalen](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

#### <a name="arg-query-doesnt-return-expected-machines"></a>ARG-frågan returnerar inte förväntade datorer

Följ stegen nedan för att ta reda på om dina frågor fungerar som de ska.

1. Kör en ARG-fråga formaterad enligt nedan i bladet Resource Graph explorer i Azure-portalen. Den här frågan härmar de filter som du valde när du skapade den dynamiska gruppen i Uppdateringshantering. Se [Använda dynamiska grupper med Uppdateringshantering](https://docs.microsoft.com/azure/automation/automation-update-management-groups). 

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Här är ett exempel:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```
 
2. Kontrollera om de datorer du letar efter visas i frågeresultatet. 

3. Om datorerna inte visas finns det förmodligen ett problem med filtret som valts i den dynamiska gruppen. Justera gruppkonfigurationen efter behov.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker inte installerat på datorer

Datorer visas i ARG-frågeresultat men visas fortfarande inte i den dynamiska gruppförhandsgranskningen. I det här fallet kanske datorerna inte har angetts som hybridarbetare och kan därför inte köra Azure Automation- och Update Management-jobb. Så här ser du till att datorerna du förväntar dig att se är konfigurerade som Hybrid Runbook Workers:

1. Gå till Automation-kontot för en dator som inte visas korrekt i Azure-portalen.

2. Välj **Hybridarbetsgrupper** under **Processautomation**.

3. Välj fliken **Systemhybridarbetsgrupper.**

4. Verifiera att hybridarbetaren finns för den datorn.

5. Om datorn inte är konfigurerad som hybridarbetare gör du justeringar med hjälp av instruktioner på [Automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker).

6. Gå med i datorn till hybridkörningsarbetsgruppen.

7. Upprepa stegen ovan för alla datorer som inte har visats i förhandsgranskningen.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: Komponenter för uppdateringshanteringslösning aktiverad, medan den virtuella datorn fortsätter att visas som konfigurerad

### <a name="issue"></a>Problem

Du fortsätter att se följande meddelande på en virtuell dator 15 minuter efter introduktion:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Orsak

Det här felet kan uppstå av följande skäl:

* Kommunikationen med Automation-kontot blockeras.

* Det finns ett dubblettdatornamn med olika källdator-ID:n. Det här scenariot uppstår när en virtuell dator med ett visst datornamn skapas i olika resursgrupper och rapporterar till samma Logistics Agent-arbetsyta i prenumerationen.

* Den vm-avbildning som är inbyggd kan komma från en klonad dator som inte har förberetts med System preparation (sysprep) med Microsoft Monitoring Agent (MMA) installerat.

### <a name="resolution"></a>Lösning

Om du vill ha det exakta problemet med den virtuella datorn kör du följande fråga på arbetsytan Log Analytics som är länkad till ditt Automation-konto.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Kommunikation med Automation-konto blockerad

Gå till [Nätverksplanering](../automation-update-management.md#ports) om du vill veta vilka adresser och portar som måste tillåtas för att uppdateringshantering ska fungera.

#### <a name="duplicate-computer-name"></a>Duplicera datornamn

Byt namn på dina virtuella datorer för att säkerställa unika namn i sin miljö.

#### <a name="onboarded-image-from-cloned-machine"></a>Inbyggd bild från klonad maskin

Om du använder en klonad avbildning har olika datornamn samma källdator-ID. Om så är fallet:

1. I logganalysarbetsytan tar du bort den virtuella `MicrosoftDefaultScopeConfig-Updates` datorn från den sparade sökningen efter scopekonfigurationen om den visas. Sparade sökningar hittar du under **Allmänt** på arbetsytan.

2. Kör följande cmdlet.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Kör `Restart-Service HealthService` för att starta om hälsotjänsten. Den här åtgärden återskapar nyckeln och genererar en ny UUID.

4. Om den här metoden inte fungerar kör du Sysprep på avbildningen först och installerar sedan MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: Du får ett länkat prenumerationsfel när du skapar en uppdateringsdistribution för datorer i en annan Azure-klient

### <a name="issue"></a>Problem

Du stöter på följande fel när du försöker skapa en uppdateringsdistribution för datorer i en annan Azure-klient:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Orsak

Det här felet uppstår när du skapar en uppdateringsdistribution som har virtuella Azure-datorer i en annan klient som ingår i en uppdateringsdistribution.

### <a name="resolution"></a>Lösning

Använd följande lösning för att få dessa objekt schemalagda. Du kan använda cmdleten [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) med parametern `ForUpdate` för att skapa ett schema. Använd sedan cmdleten [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) och skicka datorerna `NonAzureComputer` i den andra klienten till parametern. I följande exempel visas hur du gör detta:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: Oförklarliga omstarter

### <a name="issue"></a>Problem

Även om du har ställt in alternativet **Starta om kontrollen** för att aldrig starta **om**startar datorer fortfarande om när uppdateringar har installerats.

### <a name="cause"></a>Orsak

Windows Update kan ändras med flera registernycklar, som alla kan ändra omstartsbeteende.

### <a name="resolution"></a>Lösning

Granska registernycklarna som anges under [Konfigurera automatiska uppdateringar genom](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) att redigera register- och [registernycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) för att se till att datorerna är korrekt konfigurerade.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: Datorn visar "Det gick inte att starta" i en uppdateringsdistribution

### <a name="issue"></a>Problem

En dator `Failed to start` visar status. När du visar den specifika informationen för datorn visas följande fel:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Orsak

Felet kan uppstå på grund av någon av följande orsaker:

* Maskinen finns inte längre.
* Maskinen är avstängd och kan inte nås.
* Datorn har ett problem med nätverksanslutningen och därför kan hybridarbetaren på datorn inte nås.
* Det fanns en uppdatering av MMA som ändrade källdator-ID.
* Uppdateringskörningen begränsades om du nådde gränsen på 2 000 samtidiga jobb i ett Automation-konto. Varje distribution betraktas som ett jobb och varje dator i en uppdateringsdistribution räknas som ett jobb. Alla andra automatiseringsjobb eller uppdateringsdistribution som körs i ditt Automation-konto räknas in i den samtidiga jobbgränsen.

### <a name="resolution"></a>Lösning

I förekommande fall använder du [dynamiska grupper](../automation-update-management-groups.md) för dina uppdateringsdistributioner. Dessutom kan du vidta följande åtgärder.

1. Kontrollera att maskinen fortfarande finns kvar och kan nås. 
2. Om datorn inte finns redigerar du distributionen och tar bort datorn.
3. Se [avsnittet nätverksplanering](../automation-update-management.md#ports) för en lista över portar och adresser som krävs för uppdateringshantering och kontrollera sedan att datorn uppfyller dessa krav.
4. Verifiera anslutningen till Hybrid Runbook Worker med hjälp av felsökaren för Hybrid Runbook Worker-agenten. Mer information om felsökaren finns i [Felsöka problem med uppdateringsagenten](update-agent-issues.md).
5. Kör följande fråga i Log Analytics för att hitta datorer i din miljö som källdator-ID har ändrats för. Leta efter datorer som `Computer` har samma `SourceComputerId` värde men ett annat värde.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. När du har hittat berörda datorer redigerar du uppdateringsdistributionerna som riktar `SourceComputerId` sig till dessa datorer och tar sedan bort och läste dem så att det återspeglar rätt värde.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: Uppdateringar installeras utan distribution

### <a name="issue"></a>Problem

När du registrerar en Windows-dator i Uppdateringshantering visas uppdateringar installerade utan distribution.

### <a name="cause"></a>Orsak

I Windows installeras uppdateringar automatiskt så snart de är tillgängliga. Detta kan orsaka förvirring om du inte schemalägger en uppdatering som ska distribueras till datorn.

### <a name="resolution"></a>Lösning

Registernyckeln `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` är som standard en inställning `auto download and install`på 4: .

För Update Management-klienter rekommenderar vi att `auto download but do not auto install`du ställer in den här nyckeln på 3: .

Mer information finns i [Konfigurera automatiska uppdateringar](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: Datorn är redan registrerad på ett annat konto

### <a name="issue"></a>Problem

Följande felmeddelande visas:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Orsak

Datorn har redan varit inbyggt på en annan arbetsyta för uppdateringshantering.

### <a name="resolution"></a>Lösning

1. Följ stegen under [Datorer visas inte i portalen under Uppdateringshantering](#nologs) för att se till att datorn rapporterar till rätt arbetsyta.
2. Rensa artefakter på datorn genom [att ta bort hybridkörningsgruppen](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)och försök sedan igen.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: Datorn kan inte kommunicera med tjänsten

### <a name="issue"></a>Problem

Du får något av följande felmeddelanden:

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

En proxy,gateway eller brandvägg kan blockera nätverkskommunikation. 

### <a name="resolution"></a>Lösning

Granska nätverket och se till att lämpliga portar och adresser är tillåtna. Se [nätverkskrav](../automation-hybrid-runbook-worker.md#network-planning) för en lista över portar och adresser som krävs av Update Management och Hybrid Runbook Workers.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: Det går inte att skapa självsignerat certifikat

### <a name="issue"></a>Problem

Du får något av följande felmeddelanden:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Orsak

Hybrid Runbook Worker kunde inte generera ett självsignerat certifikat.

### <a name="resolution"></a>Lösning

Kontrollera att systemkontot har läsbehörighet till mappen **C:\ProgramData\Microsoft\Crypto\RSA** och försök igen.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: Den schemalagda uppdateringen misslyckades med ett underhållsfönsterExceed-fel

### <a name="issue"></a>Problem

Standardunderhållsfönstret för uppdateringar är 120 minuter. Du kan öka underhållsfönstret till högst 6 timmar eller 360 minuter.

### <a name="resolution"></a>Lösning

Redigera eventuella misslyckade schemalagda uppdateringsdistributioner och öka underhållsfönstret.

Mer information om underhållsfönster finns i [Installera uppdateringar](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: Maskinen visas som "Ej bedömd" och visar ett HRESULT-undantag

### <a name="issue"></a>Problem

* Du har datorer `Not assessed` som visas under **Efterlevnad**och ett undantagsmeddelande nedanför.
* Du ser en HRESULT-felkod i portalen.

### <a name="cause"></a>Orsak

Uppdateringsagenten (Windows Update Agent i Windows, pakethanteraren för en Linux-distribution) är inte korrekt konfigurerad. Uppdateringshantering förlitar sig på datorns uppdateringsagent för att tillhandahålla de uppdateringar som behövs, korrigeringsfilens status och resultatet av distribuerade korrigeringar. Utan den här informationen kan uppdateringshanteringen inte korrekt rapportera om de korrigeringsfiler som behövs eller installeras.

### <a name="resolution"></a>Lösning

Försök att utföra uppdateringar lokalt på datorn. Om den här åtgärden misslyckas innebär det vanligtvis att det finns ett konfigurationsfel för uppdateringsagenten.

Det här problemet orsakas ofta av nätverkskonfiguration och brandväggsproblem. Använd följande kontroller för att åtgärda problemet.

* För Linux kontrollerar du lämplig dokumentation för att se till att du kan nå nätverksslutpunkten för paketarkivet.

* För Windows kontrollerar du att agentkonfigurationen som anges i [Uppdateringar inte hämtas från intranätslutpunkten (WSUS/SCCM).](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)

  * Om datorerna är konfigurerade för Windows Update kontrollerar du att du kan nå de slutpunkter som beskrivs i [Problem relaterade till HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Om datorerna är konfigurerade för WSUS (Windows Server Update Services) kontrollerar du att du kan nå WSUS-servern som konfigurerats av [WUServer-registernyckeln](/windows/deployment/update/waas-wu-settings).

Om du ser ett HRESULT dubbelklickar du på undantaget som visas i rött för att se hela undantagsmeddelandet. Gå igenom följande tabell för potentiella lösningar eller rekommenderade åtgärder.

|Undantag  |Lösning eller åtgärd  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Sök i den relevanta felkoden i [felkodslistan](https://support.microsoft.com/help/938205/windows-update-error-code-list) för Windows Update för att hitta ytterligare information om orsaken till undantaget.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Dessa indikerar problem med nätverksanslutningen. Kontrollera att datorn har nätverksanslutning till Uppdateringshantering. Se [avsnittet nätverksplanering](../automation-update-management.md#ports) för en lista över nödvändiga portar och adresser.        |
|`0x8024001E`| Uppdateringsåtgärden slutfördes inte eftersom tjänsten eller systemet stängdes av.|
|`0x8024002E`| Windows Update-tjänsten är inaktiverad.|
|`0x8024402C`     | Om du använder en WSUS-server kontrollerar du `WUServer` `WUStatusServer` att `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` registervärdena för och under registernyckeln anger rätt WSUS-server.        |
|`0x80072EE2`|Det finns ett problem med nätverksanslutningen eller ett problem med att prata med en konfigurerad WSUS-server. Kontrollera WSUS-inställningarna och se till att tjänsten är tillgänglig från klienten.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Kontrollera att Tjänsten Windows Update (wuauserv) körs och inte är inaktiverad.        |
|`0x80070005`| Ett nekat fel kan orsakas av något av följande:<br> Infekterad dator<br> Windows Update-inställningar är inte korrekt konfigurerade<br> Fel vid filbehörighet med mappen %WinDir%\SoftwareDistribution<br> Otillräckligt diskutrymme på systemenheten (C:).
|Alla andra allmänna undantag     | Sök på internet efter möjliga lösningar och arbeta med din lokala IT-support.         |

Genom att granska filen **%Windir%\Windowsupdate.log** kan du också ta reda på möjliga orsaker. Mer information om hur du läser loggen finns i Så här läser du [filen Windowsupdate.log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Du kan också hämta och köra [felsökaren](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) för Windows Update för att söka efter eventuella problem med Windows Update på datorn.

> [!NOTE]
> Felsökaren för [Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) anger att den är avsedd för användning på Windows-klienter, men det fungerar även på Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: Uppdateringskörning returnerar status som inte kundes (Linux)

### <a name="issue"></a>Problem

En uppdateringskörning startar men stöter på fel under körningen.

### <a name="cause"></a>Orsak

Möjliga orsaker:

* Pakethanteraren är felfritt.
* Update Agent (WUA för Windows, distributionsspecifika pakethanterare för Linux) är felkonfigurerad.
* Specifika paket stör molnbaserad korrigering.
* Maskinen kan inte nås.
* Uppdateringarna hade beroenden som inte löstes.

### <a name="resolution"></a>Lösning

Om fel inträffar under en uppdateringskörning när den har startats [kontrollerar du jobbutdata](../manage-update-multi.md#view-results-of-an-update-deployment) från den berörda datorn i körningen. Du kan hitta specifika felmeddelanden från dina datorer som du kan undersöka och vidta åtgärder för. Uppdateringshantering kräver att pakethanteraren är felfri för lyckade uppdateringsdistributioner.

Om specifika korrigeringar, paket eller uppdateringar visas omedelbart innan jobbet misslyckas kan du prova [att utesluta](../automation-tutorial-update-management.md#schedule-an-update-deployment) dessa objekt från nästa uppdateringsdistribution. Information om hur du samlar in logginformation från Windows Update finns i [Windows Update-loggfiler](/windows/deployment/update/windows-update-logs).

Om du inte kan lösa ett korrigeringsproblem gör du en kopia av filen **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** och bevara den för felsökning innan nästa uppdateringsdistribution startar.

## <a name="patches-arent-installed"></a>Korrigeringar är inte installerade

### <a name="machines-dont-install-updates"></a>Datorerna installerar inte uppdateringar

Försök att köra uppdateringar direkt på datorn. Om datorn inte kan installera uppdateringarna läser du [listan över potentiella fel i felsökningsguiden](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Om uppdateringar körs lokalt kan du prova att ta bort och installera om agenten på datorn genom att följa anvisningarna vid [Ta bort en virtuell dator från uppdateringshantering](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Jag vet att det finns uppdateringar, men de visas inte som tillgängliga på mina maskiner

Detta händer ofta om datorer är konfigurerade för att hämta uppdateringar från WSUS eller Microsoft Endpoint Configuration Manager, men WSUS och Configuration Manager inte har godkänt uppdateringarna.

Du kan kontrollera om datorerna är konfigurerade för WSUS och SCCM `UseWUServer` genom att korsreferera registernyckeln till registernycklarna i [avsnittet Konfigurera automatiska uppdateringar genom att redigera avsnittet Register i den här artikeln](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Om uppdateringar inte godkänns i WSUS är de inte installerade. Du kan söka efter icke godkända uppdateringar i Log Analytics genom att köra följande fråga.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Uppdateringar visas som installerade, men jag hittar dem inte på datorn

Uppdateringar ersätts ofta av andra uppdateringar. Mer information finns i Uppdatera ersätts i [felsökningsguiden](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) för Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Installera uppdateringar efter klassificering i Linux

Distributionen av uppdateringar till Linux efter klassificering (”kritiska uppdateringar och säkerhetsuppdateringar”) har viktiga förbehåll, särskilt för CentOS. Dessa begränsningar dokumenteras på [översiktssidan för uppdateringshantering](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 saknas konsekvent

KB2267602 är [Windows Defender-definitionsuppdateringen](https://www.microsoft.com/wdsi/definitions). Den uppdateras dagligen.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support.

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport)med , det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen.
* Arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
