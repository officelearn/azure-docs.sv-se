---
title: Starta/stoppa virtuella datorer vid låg belastnings lösning
description: Den här lösningen för hantering av virtuella datorer startar och stoppar dina virtuella Azure-datorer enligt ett schema och proaktivt övervakar från Azure Monitor loggar.
services: automation
ms.subservice: process-automation
ms.date: 04/28/2020
ms.topic: conceptual
ms.openlocfilehash: 760c56ad6179a7bf94f19e004e2fbbece3908198
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683501"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Starta/stoppa virtuella datorer vid låg belastnings lösning i Azure Automation

Lösningen **Starta/stoppa virtuella datorer vid låg belastnings tider** startar eller stoppar dina virtuella Azure-datorer. Den startar eller stoppar datorer enligt användardefinierade scheman, ger insikter via Azure Monitor loggar och skickar valfria e-postmeddelanden med hjälp av [Åtgärds grupper](../azure-monitor/platform/action-groups.md). Lösningen stöder både Azure Resource Manager och klassiska virtuella datorer för de flesta scenarier. 

Den här lösningen använder cmdleten [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0) för att starta virtuella datorer. Den använder [Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Stop-AzureRmVM?view=azurermps-6.13.0) för att stoppa virtuella datorer.

> [!NOTE]
> Lösningen **Starta/stoppa virtuella datorer vid låg belastning** har uppdaterats till att stödja de senaste versionerna av de Azure-moduler som är tillgängliga. Den uppdaterade versionen av den här lösningen, som är tillgänglig i Marketplace, stöder inte AzureRM-moduler eftersom vi har migrerat från AzureRM till AZ-moduler.

Lösningen innehåller ett decentraliserat Automation-alternativ för användare som vill optimera sina VM-kostnader. Med den här lösningen kan du:

- [Schemalägg virtuella datorer för start och stopp](automation-solution-vm-management-config.md#schedule).
- Schemalägg virtuella datorer att starta och stoppa i stigande ordning med [hjälp av Azure-Taggar](automation-solution-vm-management-config.md#tags) (stöds inte för klassiska virtuella datorer).
- Stoppa virtuella datorer för virtuella datorer baserat på [låg CPU-användning](automation-solution-vm-management-config.md#cpuutil).

Följande är begränsningar med den aktuella lösningen:

- Den hanterar virtuella datorer i vilken region som helst, men kan bara användas i samma prenumeration som ditt Azure Automation-konto.
- Den är tillgänglig i Azure och Azure Government till en region som har stöd för en Log Analytics arbets yta, ett Azure Automation-konto och aviseringar. Azure Government regioner stöder för närvarande inte e-postfunktioner.

## <a name="solution-prerequisites"></a>Lösnings krav

Runbooks för den här lösningen fungerar med ett [Kör som-konto i Azure](automation-create-runas-account.md). Kör som-kontot är den föredragna autentiseringsmetoden eftersom den använder certifikatautentisering i stället för ett lösen ord som kan gå ut eller ändras ofta.

Vi rekommenderar att du använder ett separat Automation-konto för lösningen **Starta/stoppa virtuella datorer vid låg belastnings tid** . Azure-modulernas versioner uppgraderas ofta och deras parametrar kan ändras. Lösningen har inte uppgraderats på samma takt och fungerar kanske inte med nyare versioner av de cmdletar som används. Du rekommenderas att testa modul uppdateringar i ett test Automation-konto innan du importerar dem i dina produktions Automation-konton.

## <a name="solution-permissions"></a>Lösnings behörigheter

Du måste ha vissa behörigheter för att distribuera den **virtuella datorn för att starta/stoppa virtuella datorer vid låg belastning** . Behörigheterna skiljer sig om lösningen använder ett förgenererat Automation-konto och Log Analytics arbets ytan från de behörigheter som krävs om lösningen skapar ett nytt konto och en arbets yta under distributionen. 

Du behöver inte konfigurera behörigheter om du är deltagare i prenumerationen och en global administratör i din Azure Active Directory klient. Om du inte har dessa rättigheter eller behöver konfigurera en anpassad roll kontrollerar du att du har de behörigheter som beskrivs nedan.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Behörigheter för redan befintligt Automation-konto och Log Analytics arbets yta

Om du vill distribuera en lösning för att **Starta/stoppa virtuella datorer under låg tid** till ett befintligt Automation-konto och Log Analytics arbets ytan, kräver användaren som distribuerar lösningen följande behörigheter för resurs grupps omfånget. Mer information om roller finns i [anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md).

| Behörighet | Omfång|
| --- | --- |
| Microsoft. Automation/automationAccounts/Read | Resursgrupp |
| Microsoft. Automation/automationAccounts/variabler/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/-scheman/-skrivning | Resursgrupp |
| Microsoft. Automation/automationAccounts/Runbooks/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/Connections/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/certificates/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/modules/-skrivning | Resursgrupp |
| Microsoft. Automation/automationAccounts/modules/läsa | Resursgrupp |
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/Jobs/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/Jobs/Read | Resursgrupp |
| Microsoft. OperationsManagement/lösningar/Skriv | Resursgrupp |
| Microsoft. OperationalInsights/arbets ytor/* | Resursgrupp |
| Microsoft. Insights/diagnosticSettings/Write | Resursgrupp |
| Microsoft. Insights/ActionGroups/Write | Resursgrupp |
| Microsoft. Insights/ActionGroups/Read | Resursgrupp |
| Microsoft. Resources/Subscriptions/resourceGroups/Read | Resursgrupp |
| Microsoft. Resources/Deployments/* | Resursgrupp |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Behörigheter för det nya Automation-kontot och arbets ytan ny Log Analytics

Du kan distribuera lösningen **Starta/stoppa virtuella datorer under låg timme** till ett nytt Automation-konto och Log Analytics arbets ytan. I det här fallet behöver användaren som distribuerar lösningen de behörigheter som definierats i föregående avsnitt samt de behörigheter som definierats i det här avsnittet. 

Användaren som distribuerar lösningen behöver följande roller:

- Administratör för prenumerationen. Den här rollen krävs för att skapa det klassiska kör som-kontot om du ska hantera klassiska virtuella datorer. [Klassiska kör som-konton](automation-create-standalone-account.md#create-a-classic-run-as-account) skapas inte längre som standard.
- Medlemskap i rollen [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Application Developer. Mer information om hur du konfigurerar kör som-konton finns i [behörighet att konfigurera kör som-konton](manage-runas-account.md#permissions).
- Deltagare i prenumerationen eller följande behörigheter.

| Behörighet |Omfång|
| --- | --- |
| Microsoft. Authorization/Operations/Read | Prenumeration|
| Microsoft. Authorization/Permissions/Read |Prenumeration|
| Microsoft. Authorization/roleAssignments/Read | Prenumeration |
| Microsoft.Authorization/roleAssignments/write | Prenumeration |
| Microsoft. Authorization/roleAssignments/Delete | Prenumeration |
| Microsoft. Automation/automationAccounts/Connections/Read | Resursgrupp |
| Microsoft. Automation/automationAccounts/certifikat/läsa | Resursgrupp |
| Microsoft. Automation/automationAccounts/Write | Resursgrupp |
| Microsoft. OperationalInsights/arbets ytor/Skriv | Resursgrupp |

## <a name="solution-components"></a>Lösningskomponenter

**Lösningen starta/stoppa virtuella datorer vid låg belastning** inkluderar förkonfigurerade Runbooks, scheman och integrering med Azure Monitor loggar. Du kan använda dessa element för att skräddarsy Start och avstängning av dina virtuella datorer så att de passar dina affärs behov.

### <a name="runbooks"></a>Runbooks

I följande tabell visas de Runbooks som lösningen distribuerar till ditt Automation-konto. Gör inte ändringar i Runbook-koden. Skriv i stället din egen Runbook för nya funktioner.

> [!IMPORTANT]
> Kör inte någon Runbook med det **underordnade** tillägget till sitt namn direkt.

Alla överordnade Runbooks inkluderar `WhatIf` parametern. När värdet är true, stöder parametern information om det exakta beteende som Runbook tar när den körs utan parametern och kontrollerar att rätt virtuella datorer är riktade. En Runbook utför bara sina definierade åtgärder när `WhatIf` parametern har angetts till false.

|Runbook | Parametrar | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Anropas från den överordnade runbooken. Denna Runbook skapar aviseringar per resurs för det automatiska stopp scenariot.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true eller false  | Skapar eller uppdaterar Azures aviserings regler på virtuella datorer i mål prenumerationen eller resurs grupperna. <br> `VMList`är en kommaavgränsad lista över virtuella datorer. Till exempel `vm1, vm2, vm3`.<br> `WhatIf`aktiverar validering av Runbook-logik utan att köra.|
|AutoStop_Disable | Inga | Inaktiverar automatiska stopp-aviseringar och standard schema.|
|AutoStop_VM_Child | WebHookData | Anropas från den överordnade runbooken. Aviserings regler anropar denna Runbook för att stoppa en klassisk virtuell dator.|
|AutoStop_VM_Child_ARM | WebHookData |Anropas från den överordnade runbooken. Aviserings regler anropar denna Runbook för att stoppa en virtuell dator.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Åtgärd: starta eller stoppa<br> VMList  | Utför åtgärden starta eller stoppa i den klassiska VM-gruppen genom att Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Åtgärd: starta eller stoppa <br> ResourceGroupName | Anropas från den överordnade runbooken. Kör en start-eller stopp åtgärd för det schemalagda steget.|
|ScheduledStartStop_Child_Classic | VMName<br> Åtgärd: starta eller stoppa<br> ResourceGroupName | Anropas från den överordnade runbooken. Kör en start-eller stopp åtgärd för det schemalagda stoppet för klassiska virtuella datorer. |
|ScheduledStartStop_Parent | Åtgärd: starta eller stoppa <br>VMList <br> WhatIf: true eller false | Startar eller stoppar alla virtuella datorer i prenumerationen. Redigera variablerna `External_Start_ResourceGroupNames` och `External_Stop_ResourceGroupNames` för att endast köra på dessa mål resurs grupper. Du kan också undanta vissa virtuella datorer genom att uppdatera `External_ExcludeVMNames` variabeln.|
|SequencedStartStop_Parent | Åtgärd: starta eller stoppa <br> WhatIf: true eller false<br>VMList| Skapar taggar med namnet **sequencestart** och **sequencestop** på varje virtuell dator för vilken du vill starta/stoppa aktivitet. Dessa taggnamn är Skift läges känsliga. Taggens värde måste vara ett positivt heltal (1, 2, 3) som motsvarar den ordning som du vill starta eller stoppa. <br>**Obs!** virtuella datorer måste finnas i resurs grupper som har definierats i `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` variablerna, och. De måste ha lämpliga taggar för att åtgärder ska börja gälla.|

### <a name="variables"></a>Variabler

I följande tabell visas variablerna som skapas i ditt Automation-konto. Ändra endast variabler som föregås av `External` . Om du ändrar variabler med prefixet får du `Internal` oönskade effekter.

> [!NOTE]
> Begränsningar i VM-namn och resurs grupp är i stort sett resultatet av varierande storlek. Se [variabel till gångar i Azure Automation](https://docs.microsoft.com/azure/automation/shared-resources/variables).

|Variabel | Description|
|---------|------------|
|External_AutoStop_Condition | Den villkorliga operator som krävs för att konfigurera villkoret innan en avisering utlöses. Godkända värden är `GreaterThan` , `GreaterThanOrEqual` , `LessThan` och `LessThanOrEqual` .|
|External_AutoStop_Description | Aviseringen för att stoppa den virtuella datorn om PROCESSORns procents ATS överstiger tröskelvärdet.|
|External_AutoStop_Frequency | Utvärderings frekvensen för regeln. Den här parametern accepterar inmatade i TimeSpan-format. Möjliga värden är mellan 5 och 6 timmar. |
|External_AutoStop_MetricName | Namnet på det prestanda mått som Azure-aviserings regeln ska konfigureras för.|
|External_AutoStop_Severity | Allvarlighets grad för måttets avisering, som kan vara mellan 0 och 4. |
|External_AutoStop_Threshold | Tröskelvärdet för den Azure Alert-regel som anges i variabeln `External_AutoStop_MetricName` . Procent värden sträcker sig från 1 till 100.|
|External_AutoStop_TimeAggregationOperator | Tids mängds operatorn som används för den valda fönster storleken för att utvärdera villkoret. Godkända värden är `Average` ,,, `Minimum` `Maximum` `Total` och `Last` .|
|External_AutoStop_TimeWindow | Storleken på fönstret där Azure analyserar valda mått för att utlösa en avisering. Den här parametern accepterar inmatade i TimeSpan-format. Möjliga värden är mellan 5 och 6 timmar.|
|External_EnableClassicVMs| Värde som anger om klassiska virtuella datorer är riktade mot lösningen. Standardvärdet är true. Ställ in den här variabeln på falskt för Azure Cloud Solution Provider (CSP)-prenumerationer. Klassiska virtuella datorer kräver ett [klassiskt kör som-konto](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Kommaavgränsad lista över VM-namn som ska undantas, begränsade till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i listan kan virtuella datorer som är inställda på att undantas oavsiktligt startas eller stoppas.|
|External_Start_ResourceGroupNames | Kommaavgränsad lista över en eller flera resurs grupper som är avsedda för start åtgärder.|
|External_Stop_ResourceGroupNames | Kommaavgränsad lista över en eller flera resurs grupper som är avsedda för stopp åtgärder.|
|External_WaitTimeForVMRetrySeconds |Vänte tiden i sekunder för de åtgärder som ska utföras på de virtuella datorerna för **SequencedStartStop_Parent** Runbook. Med den här variabeln kan runbooken vänta på underordnade åtgärder under ett angivet antal sekunder innan nästa åtgärd fortsätter. Maximal vänte tid är 10800 eller tre timmar. Standardvärdet är 2100 sekunder.|
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|
|Internal_AutoSnooze_ARM_WebhookURI | Webhook-URI: n anropade för scenariot för autostopp för virtuella datorer.|
|Internal_AutoSnooze_WebhookUri | Webhook-URI: n anropade för scenariot för autostop för klassiska virtuella datorer.|
|Internal_AzureSubscriptionId | ID för Azure-prenumerationen.|
|Internal_ResourceGroupName | Namnet på resurs gruppen för Automation-kontot.|

>[!NOTE]
>`External_WaitTimeForVMRetryInSeconds`Standardvärdet har uppdaterats från 600 till 2100 för variabeln. 

I alla scenarier, variablerna `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` och `External_ExcludeVMNames` är nödvändiga för att rikta in virtuella datorer, förutom de kommaavgränsade VM-listorna för **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**och **ScheduledStartStop_Parent** Runbooks. Det innebär att de virtuella datorerna måste tillhöra mål resurs grupper för att start-och stopp åtgärder ska inträffa. Logiken fungerar ungefär som Azure Policy, i så att du kan rikta prenumerationen eller resurs gruppen och ha åtgärder som ärvts av nyskapade virtuella datorer. Den här metoden gör att du inte behöver ha ett separat schema för varje virtuell dator och hantera startar och stoppas i stor skala.

### <a name="schedules"></a>Scheman

I följande tabell visas alla standard scheman som skapats i ditt Automation-konto.Du kan ändra dem eller skapa egna anpassade scheman.Som standard inaktive ras alla scheman förutom **Scheduled_StartVM** och **Scheduled_StopVM** scheman.

Aktivera inte alla scheman, eftersom detta kan skapa överlappande schema åtgärder. Det är bäst att bestämma vilka optimeringar du vill göra och ändra dem på lämpligt sätt. I exempel scenarierna i översikts avsnittet finns ytterligare förklaring.

|Schema namn | Frekvens | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Var 8:e timme | Kör **AutoStop_CreateAlert_Parent** Runbook var 8: e timme, vilket i sin tur stoppar de VM-baserade värdena i `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` variablerna, och. Alternativt kan du ange en kommaavgränsad lista över virtuella datorer med hjälp av- `VMList` parametern.|
|Scheduled_StopVM | Användardefinierad, daglig | Kör **ScheduledStopStart_Parent** Runbook med en parameter på `Stop` varje dag vid den angivna tiden.Stoppar automatiskt alla virtuella datorer som uppfyller reglerna som definierats av variabel till gångar.Aktivera det **schemalagda schemat för schemalagda StartVM**.|
|Scheduled_StartVM | Användardefinierad, daglig | Kör **ScheduledStopStart_Parent** Runbook med ett parameter värde på `Start` varje dag vid den angivna tiden. Startar automatiskt alla virtuella datorer som uppfyller reglerna som definierats av variabel till gångar.Aktivera det **schemalagda schemat för schemalagda StopVM**.|
|Sekvenserad – StopVM | 1:00 AM (UTC), varje fredag | Kör **Sequenced_StopStop_Parent** Runbook med ett parameter värde på `Stop` varje fredag vid den angivna tiden.I tur och ordning stoppas alla virtuella datorer med en tagg **SequenceStop** som definieras av lämpliga variabler. Mer information om tagg värden och till gångs variabler finns i [Runbooks](#runbooks).Aktivera relaterat schema, **sekvenserat-StartVM**.|
|Sekvenserad – StartVM | 1:00 PM (UTC), varje måndag | Kör **SequencedStopStart_Parent** Runbook med ett parameter värde på `Start` varje måndag vid den angivna tiden. Sekventiellt (fallande) startar alla virtuella datorer med en tagg för **SequenceStart** som definieras av lämpliga variabler. Mer information om tagg värden och variabel till gångar finns i [Runbooks](#runbooks). Aktivera relaterat schema, **sekvenserat-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Användning av lösningen med klassiska virtuella datorer

Om du använder lösningen **Starta/stoppa virtuella datorer vid låg belastnings tid** för klassiska virtuella datorer, bearbetar Automation alla virtuella datorer sekventiellt per moln tjänst. Virtuella datorer bearbetas fortfarande parallellt över olika moln tjänster. 

För att du ska kunna använda lösningen med klassiska virtuella datorer behöver du ett klassiskt kör som-konto, som inte skapas som standard. Anvisningar om hur du skapar ett klassiskt kör som-konto finns i [skapa ett klassiskt kör som-konto](automation-create-standalone-account.md#create-a-classic-run-as-account).

Om du har fler än 20 virtuella datorer per moln tjänst är här några rekommendationer:

* Skapa flera scheman med den överordnade Runbook- **ScheduledStartStop_Parent** och ange 20 virtuella datorer per schema. 
* I schema egenskaperna använder du `VMList` parametern för att ange namn på virtuella datorer som en kommaavgränsad lista. 

Annars, om automatiserings jobbet för den här lösningen körs mer än tre timmar, tas det tillfälligt bort eller stoppas enligt den [verkliga delnings](automation-runbook-execution.md#fair-share) gränsen.

Azure CSP-prenumerationer stöder endast Azure Resource Managers modellen. Icke-Azure Resource Manager tjänster är inte tillgängliga i programmet. När **lösningen starta/stoppa virtuella datorer under låg tid** körs kan du få fel eftersom den har cmdlets för att hantera klassiska resurser. Läs mer om CSP i [tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Om du använder en CSP-prenumeration ska du ange [External_EnableClassicVMs](#variables) variabeln till falskt efter distributionen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Aktivera lösningen

Börja använda lösningen genom att följa stegen i [aktivera lösningen för att starta/stoppa virtuella datorer](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Visa lösningen

Använd någon av följande metoder för att få åtkomst till lösningen när du har aktiverat den:

* Från ditt Automation-konto väljer du **Starta/stoppa virtuell dator** under **relaterade resurser**. På sidan Starta/stoppa virtuell dator väljer du **Hantera lösningen** från höger sida av sidan under hantera lösningar för att **Starta/stoppa virtuella datorer**.

* Navigera till den Log Analytics arbets ytan som är länkad till ditt Automation-konto. När du har valt arbets ytan väljer du **lösningar** i det vänstra fönstret. På sidan lösningar väljer du lösningen **Start-Stop-VM [arbetsyte]** i listan.  

Om du väljer lösningen visas lösnings sidan **Starta-Stop-VM [Workspace]** . Här kan du granska viktig information, till exempel informationen på **StartStopVM** -panelen. Precis som i arbets ytan Log Analytics visar den här panelen ett antal och en grafisk representation av Runbook-jobben för lösningen som har startat och har slutförts.

![Sidan Automation Uppdateringshantering-lösning](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Du kan utföra ytterligare analyser av jobb posterna genom att klicka på Ring panelen. Instrument panelen för lösningen visar jobb historik och fördefinierade loggs öknings frågor. Växla till logganalys-portalen i Log Analytics om du vill söka utifrån dina Sök frågor.

## <a name="update-the-feature"></a>Uppdatera funktionen

Om du har distribuerat en tidigare version av den här lösningen tar du bort den från ditt konto innan du distribuerar en uppdaterad version. Följ stegen för att [ta bort lösningen](#remove-the-feature) och följ sedan stegen för att [distribuera lösningen](automation-solution-vm-management-enable.md).

## <a name="remove-the-feature"></a>Ta bort funktionen

Om du inte längre behöver använda lösningen kan du ta bort den från Automation-kontot. Att ta bort lösningen tar bara bort Runbooks. De scheman eller variabler som skapades när lösningen lades till tas inte bort. Ta bort dessa till gångar manuellt om du inte använder dem med andra Runbooks.

Ta bort lösningen:

1. Från ditt Automation-konto väljer du **länkad arbets yta** under **relaterade resurser**.

2. Välj **gå till arbets yta**.

3. Klicka på **lösningar** under **Allmänt**. 

4. På sidan lösningar väljer du lösningen **Start-Stop-VM [arbetsyte]**. 

5. På sidan **VMManagementSolution [Workspace]** väljer du **ta bort** på menyn.<br><br> ![Ta bort lösningen för VM-hantering](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. I fönstret **ta bort lösning** bekräftar du att du vill ta bort lösningen.

7. Medan informationen är verifierad och lösningen tas bort, kan du följa förloppet under **meddelanden**, valt i menyn. Du kommer tillbaka till lösnings sidan när du har tagit bort lösningen.

Automation-kontot och Log Analytics-arbetsytan tas inte bort som en del av den här processen. Om du inte vill behålla Log Analytics arbets ytan måste du manuellt ta bort den från Azure Portal:

1. Sök efter och välj **Log Analytics arbets ytor**.

2. På sidan Log Analytics arbets yta väljer du arbets ytan.

3. Välj **ta bort** på menyn på sidan Inställningar för arbets yta.

4. Om du inte vill behålla komponenter för Azure Automation-konto [lösning](#solution-components)kan du ta bort dem manuellt.

## <a name="next-steps"></a>Nästa steg

[Aktivera](automation-solution-vm-management-enable.md) lösningen **Starta/stoppa virtuella datorer vid låg belastnings tid** för dina virtuella Azure-datorer.
