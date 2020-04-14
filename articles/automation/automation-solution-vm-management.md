---
title: Start/stoppa virtuella datorer under lösning utanför arbetstid
description: Den här vm-hanteringslösningen startar och stoppar dina virtuella Azure-datorer enligt ett schema och övervakar proaktivt från Azure Monitor-loggar.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: fbcd4ea174d4b6a2a45495c32f178ed1bd01bbe0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261371"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Starta/stoppa virtuella datorer under off-hours-lösning i Azure Automation

**Start/stop virtuella datorer under ledig tid startar** eller stoppar dina virtuella Azure-datorer. Den startar eller stoppar datorer på användardefinierade scheman, ger insikter via Azure Monitor-loggar och skickar valfria e-postmeddelanden med hjälp av [åtgärdsgrupper](../azure-monitor/platform/action-groups.md). Lösningen stöder både Azure Resource Manager och klassiska virtuella datorer för de flesta scenarier. 

Den här lösningen ger ett decentraliserat lågprisautomationsalternativ för användare som vill optimera sina vm-kostnader. Med den här lösningen kan du:

- [Schemalägg virtuella datorer så att de startar och stoppar](automation-solution-vm-management-config.md#schedule).
- Schemalägg virtuella datorer så att de startar och slutar i stigande ordning med hjälp av [Azure-taggar](automation-solution-vm-management-config.md#tags) (stöds inte för klassiska virtuella datorer).
- Automatiska virtuella datorer baserat på [låg CPU-användning](automation-solution-vm-management-config.md#cpuutil).

> [!NOTE]
> **Start/stop virtuella datorer under ledig tid-lösningen** har uppdaterats för att stödja de senaste versionerna av Azure-modulerna som är tillgängliga.

Följande är begränsningar med den aktuella lösningen:

- Den hanterar virtuella datorer i valfri region, men kan bara användas i samma prenumeration som ditt Azure Automation-konto.
- Den är tillgänglig i Azure och Azure Government för alla regioner som stöder en Log Analytics-arbetsyta, ett Azure Automation-konto och aviseringar. Azure Government-regioner stöder för närvarande inte e-postfunktioner.

## <a name="solution-prerequisites"></a>Förutsättningar för lösning

Runbooks för den här lösningen fungerar med ett [Azure Run As-konto](automation-create-runas-account.md). Kontot Kör som är den önskade autentiseringsmetoden eftersom den använder certifikatautentisering i stället för ett lösenord som kan upphöra att gälla eller ändras ofta.

Vi rekommenderar att du använder ett separat **Automation-konto för start-/stopp-virtuella datorer under ledig tid.** Azure-modulversioner uppgraderas ofta och deras parametrar kan ändras. Lösningen uppgraderas inte på samma kadens och det kanske inte fungerar med nyare versioner av cmdlets som används. Du rekommenderas att testa moduluppdateringar i ett testautomationskonto innan du importerar dem i ditt produktionsautomatiseringskonto.You're recommended to test module updates in a test Automation account before importing them in your production Automation account(s).

## <a name="solution-permissions"></a>Lösningsbehörigheter

Du måste ha vissa behörigheter för att distribuera **start-/stopp-virtuella datorer under ledig tid-lösning.** Behörigheterna är olika om lösningen använder ett förskapad Automation-konto och Log Analytics-arbetsyta från de behörigheter som behövs om lösningen skapar ett nytt konto och en ny arbetsyta under distributionen. 

Du behöver inte konfigurera behörigheter om du är deltagare i prenumerationen och en global administratör i din Azure Active Directory-klient. Om du inte har dessa rättigheter eller behöver konfigurera en anpassad roll kontrollerar du att du har behörigheterna som beskrivs nedan.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Behörigheter för befintligt Automation-konto och Log Analytics-arbetsyta

För att distribuera **start-/stopp-virtuella datorer under ledig tid** till ett befintligt Automation-konto och Log Analytics-arbetsyta kräver användaren som distribuerar lösningen följande behörigheter för resursgruppens scope. Mer information om roller finns i [Anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md).

| Behörighet | Omfång|
| --- | --- |
| Microsoft.Automation/automationKonto/läsa | Resursgrupp |
| Microsoft.Automation/automationKonton/variabler/skrivning | Resursgrupp |
| Microsoft.Automation/automationKonton/scheman/skrivning | Resursgrupp |
| Microsoft.Automation/automationKonto/runbooks/write | Resursgrupp |
| Microsoft.Automation/automationKonto/anslutningar/skrivning | Resursgrupp |
| Microsoft.Automation/automationKonton/certifikat/skrivning | Resursgrupp |
| Microsoft.Automation/automationKonton/moduler/skrivning | Resursgrupp |
| Microsoft.Automation/automationKonton/moduler/läsa | Resursgrupp |
| Microsoft.automation/automationKonton/jobbSchedules/write | Resursgrupp |
| Microsoft.Automation/automationKonton/jobb/skrivning | Resursgrupp |
| Microsoft.Automation/automationKonto/jobb/läsa | Resursgrupp |
| Microsoft.OperationsManagement/lösningar/skriva | Resursgrupp |
| Microsoft.OperationalInsights/arbetsytor/* | Resursgrupp |
| Microsoft.Insights/diagnosticSettings/write | Resursgrupp |
| Microsoft.Insights/ActionGroups/Write | Resursgrupp |
| Microsoft.Insights/ActionGroups/read Microsoft.Insights/ActionGroups/read Microsoft.Insights/ActionGroups/read Microsoft. | Resursgrupp |
| Microsoft.Resurser/prenumerationer/resourceGroups/read | Resursgrupp |
| Microsoft.Resources/deployments/* | Resursgrupp |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Behörigheter för nytt Automation-konto och ny Log Analytics-arbetsyta

Du kan distribuera **start-/stopp-virtuella datorer under ledig tid** till ett nytt Automation-konto och Log Analytics-arbetsyta. I det här fallet behöver användaren som distribuerar lösningen de behörigheter som definierats i föregående avsnitt samt de behörigheter som definieras i det här avsnittet. 

Användaren som distribuerar lösningen behöver följande roller:

- Coadministrator på abonnemang. Den här rollen krävs för att skapa det klassiska körningskontot om du ska hantera klassiska virtuella datorer. [Klassiska Kör som-konton](automation-create-standalone-account.md#create-a-classic-run-as-account) skapas inte längre som standard.
- Medlemskap i rollen Utvecklare av [Azure Active Directory-program.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Mer information om hur du konfigurerar Kör som konton finns i [Behörigheter för att konfigurera Kör som-konton](manage-runas-account.md#permissions).
- Deltagare i prenumerationen eller följande behörigheter.

| Behörighet |Omfång|
| --- | --- |
| Microsoft.Auktorisering/åtgärder/läsning | Prenumeration|
| Microsoft.Auktorisering/behörigheter/läsa |Prenumeration|
| Microsoft.Authorization/roleAssignments/read | Prenumeration |
| Microsoft.Authorization/roleAssignments/write | Prenumeration |
| Microsoft.Authorization/roleAssignments/delete Microsoft.Authorization/roleAssignments/delete Microsoft.Authorization/roleAssignments/delete Microsoft. | Prenumeration |
| Microsoft.Automation/automationKonto/anslutningar/läsa | Resursgrupp |
| Microsoft.Automation/automationKonto/certifikat/läsa | Resursgrupp |
| Microsoft.Automation/automationKonto/skrivning | Resursgrupp |
| Microsoft.OperationalInsights/workspaces/write | Resursgrupp |

## <a name="solution-components"></a>Lösningskomponenter

**Start/stop-virtuella datorer under ledig tid-lösningen** innehåller förkonfigurerade runbooks, scheman och integrering med Azure Monitor-loggar. Du kan använda dessa element för att skräddarsy start och avstängning av dina virtuella datorer så att de passar dina affärsbehov.

### <a name="runbooks"></a>Runbooks

I följande tabell visas de runbooks som lösningen distribuerar till ditt Automation-konto. Gör INTE ändringar i runbookkoden. Skriv i stället en egen runbook för nya funktioner.

> [!IMPORTANT]
> Kör inte direkt någon runbook med **underordnad** bifogad till namnet.

Alla överordnade runbooks `WhatIf` innehåller parametern. När den är inställd på True stöder parametern detaljer om det exakta beteende som runbooken tar när den körs utan parametern och verifierar att rätt virtuella datorer är riktade. En runbook utför bara sina `WhatIf` definierade åtgärder när parametern är inställd på Falskt.

|Runbook | Parametrar | Beskrivning|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject (VMObject) <br> Varningsåtgärd <br> WebHookURI (olikartade) | Anropad från den överordnade runbooken. Den här runbooken skapar aviseringar per resurs för scenariot Auto-Stop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Sant eller falskt  | Skapar eller uppdaterar Azure-aviseringsregler för virtuella datorer i den riktade prenumerationen eller resursgrupperna. <br> `VMList`är en kommaavgränsad lista över virtuella datorer. Till exempel `vm1, vm2, vm3`.<br> `WhatIf`aktiverar validering av runbook-logik utan att köra.|
|AutoStop_Disable | Inget | Inaktiverar automatisk stoppvarningar och standardschema.|
|AutoStop_VM_Child | WebHookData (Olikartade) | Anropad från den överordnade runbooken. Varningsregler anropar den här runbooken för att stoppa en klassisk virtuell dator.|
|AutoStop_VM_Child_ARM | WebHookData (Olikartade) |Anropad från den överordnade runbooken. Varningsregler anropar den här runbooken för att stoppa en virtuell dator.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Åtgärd: Start eller stopp<br> VMList  | Utför åtgärdsstart eller stopp i den klassiska VM-gruppen av Molntjänster. |
|ScheduledStartStop_Child | VMName <br> Åtgärd: Start eller stopp <br> ResourceGroupName | Anropad från den överordnade runbooken. Utför en start- eller stoppåtgärd för det schemalagda stoppet.|
|ScheduledStartStop_Child_Classic | VMName<br> Åtgärd: Start eller stopp<br> ResourceGroupName | Anropad från den överordnade runbooken. Utför en start- eller stoppåtgärd för det schemalagda stoppet för klassiska virtuella datorer. |
|ScheduledStartStop_Parent | Åtgärd: Start eller stopp <br>VMList <br> WhatIf: Sant eller falskt | Startar eller stoppar alla virtuella datorer i prenumerationen. Redigera variablerna `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` och kör endast på dessa riktade resursgrupper. Du kan också utesluta specifika `External_ExcludeVMNames` virtuella datorer genom att uppdatera variabeln.|
|SequencedStartStop_Parent | Åtgärd: Start eller stopp <br> WhatIf: Sant eller falskt<br>VMList| Skapar taggar med namnet **sequencestart** och **sequencestop** på varje virtuell dator som du vill sekvensera start/stopp-aktivitet för. Dessa taggnamn är skiftlägeskänsliga. Taggens värde ska vara ett positivt heltal (1, 2, 3) som motsvarar den ordning i vilken du vill starta eller stoppa. <br>**Obs:** Virtuella datorer måste vara `External_Start_ResourceGroupNames`inom `External_Stop_ResourceGroupNames`resursgrupper som definierats i , och `External_ExcludeVMNames` variabler. De måste ha lämpliga taggar för att åtgärder ska börja gälla.|

### <a name="variables"></a>Variabler

I följande tabell visas de variabler som skapats i ditt Automation-konto. Ändra endast variabler som `External`föregås av . Ändra variabler föregås `Internal` med orsakar oönskade effekter.

> [!NOTE]
> Begränsningar för VM-namn och resursgrupp är till stor del ett resultat av variabel storlek.

|Variabel | Beskrivning|
|---------|------------|
|External_AutoStop_Condition | Den villkorliga operatorn som krävs för att konfigurera villkoret innan en avisering utlöses. Godtagbara `GreaterThan`värden `GreaterThanOrEqual` `LessThan`är `LessThanOrEqual`, , och .|
|External_AutoStop_Description | Aviseringen för att stoppa den virtuella datorn om processorprocenten överskrider tröskelvärdet.|
|External_AutoStop_Frequency | Utvärderingsfrekvensen för regel. Den här parametern accepterar indata i tidsspannformat. Möjliga värden är från 5 minuter till 6 timmar. |
|External_AutoStop_MetricName | Namnet på det prestandamått som Azure Alert-regeln ska konfigureras för.|
|External_AutoStop_Severity | Allvarlighetsgrad för måttaviseringen, som kan variera från 0 till 4. |
|External_AutoStop_Threshold | Tröskelvärdet för Azure Alert-regeln som `External_AutoStop_MetricName`anges i variabeln . Procentvärdena varierar mellan 1 och 100.|
|External_AutoStop_TimeAggregationOperator | Den tidsaggregeringsoperator som används på den valda fönsterstorleken för att utvärdera villkoret. Godtagbara `Average`värden `Minimum` `Maximum`är `Total`, `Last`, , och .|
|External_AutoStop_TimeWindow | Storleken på fönstret under vilket Azure analyserar valda mått för att utlösa en avisering. Den här parametern accepterar indata i tidsspannformat. Möjliga värden är från 5 minuter till 6 timmar.|
|External_EnableClassicVMs| Värde som anger om klassiska virtuella datorer är inriktade på lösningen. Standardvärdet är Sant. Ange den här variabeln till CSP-prenumerationer (False for Azure Cloud Solution Provider). Klassiska virtuella datorer kräver ett [klassiskt kör som konto](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Kommaavgränsad lista över VM-namn som ska uteslutas, begränsad till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i listan kan virtuella datorer som är inställda på att uteslutas oavsiktligt startas eller stoppas.|
|External_Start_ResourceGroupNames | Kommaavgränsad lista över en eller flera resursgrupper som är avsedda för startåtgärder.|
|External_Stop_ResourceGroupNames | Kommaavgränsad lista över en eller flera resursgrupper som är avsedda för stoppåtgärder.|
|External_WaitTimeForVMRetrySeconds |Väntetiden i sekunder för de åtgärder som ska utföras på de virtuella datorerna för **SequencedStartStop_Parent** runbook. Med den här variabeln kan runbooken vänta på underordnade åtgärder i ett visst antal sekunder innan du fortsätter med nästa åtgärd. Den maximala väntetiden är 10800 eller tre timmar. Standardvärdet är 2100 sekunder.|
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|
|Internal_AutoSnooze_ARM_WebhookURI | Webhook URI efterlyste AutoStop-scenariot för virtuella datorer.|
|Internal_AutoSnooze_WebhookUri | Webhook URI efterlyste AutoStop-scenariot för klassiska virtuella datorer.|
|Internal_AzureSubscriptionId | Azure-prenumerations-ID.|
|Internal_ResourceGroupName | Resursgruppsnamnet för Automation-kontot.|

>[!NOTE]
>För variabeln `External_WaitTimeForVMRetryInSeconds`har standardvärdet uppdaterats från 600 till 2100. 

I alla scenarier är `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`variablerna `External_ExcludeVMNames` , och nödvändiga för att rikta virtuella datorer, med undantag för de kommaavgränsade vm-listorna för **AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent**och **ScheduledStartStop_Parent** runbooks. Det vill säga, dina virtuella datorer måste tillhöra målgrupper för att starta och stoppa åtgärder som ska inträffa. Logiken fungerar som Azure-principen, eftersom du kan rikta prenumerationen eller resursgruppen och ha åtgärder som ärvts av nyskapade virtuella datorer. Den här metoden undviker att behöva behålla ett separat schema för varje virtuell dator och hantera starter och stopp i skala.

### <a name="schedules"></a>Scheman

I följande tabell visas var och en av standardscheman som skapats i ditt Automation-konto.Du kan ändra dem eller skapa egna anpassade scheman.Som standard är alla scheman inaktiverade förutom **Scheduled_StartVM** och **Scheduled_StopVM** scheman.

Aktivera inte alla scheman, eftersom detta kan skapa överlappande schemaåtgärder. Det är bäst att avgöra vilka optimeringar du vill göra och ändra dem därefter. Mer förklaring finns i exempelscenarierna i översiktsavsnittet.

|Schemalägg namn | Frequency | Beskrivning|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Var 8:e timme | Kör **AutoStop_CreateAlert_Parent** runbook var 8:e timme, vilket i sin `External_Start_ResourceGroupNames`tur `External_Stop_ResourceGroupNames`stoppar `External_ExcludeVMNames` de VM-baserade värdena i , och variablerna. Du kan också ange en kommaavgränsad lista med `VMList` virtuella datorer med hjälp av parametern.|
|Scheduled_StopVM | Användardefinierad, daglig | Kör **ScheduledStopStart_Parent** runbook med en `Stop` parameter för varje dag vid den angivna tiden.Stoppar automatiskt alla virtuella datorer som uppfyller de regler som definieras av variabla tillgångar.Aktivera det relaterade schemat **Scheduled-StartVM**.|
|Scheduled_StartVM | Användardefinierad, daglig | Kör **ScheduledStopStart_Parent** runbook med ett parametervärde `Start` för varje dag vid den angivna tiden. Startar automatiskt alla virtuella datorer som uppfyller de regler som definieras av variabla tillgångar.Aktivera det relaterade schemat **Scheduled-StopVM**.|
|Sekvenserade-StopVM | 01:00 (UTC), varje fredag | Kör Sequenced_Parent runbook med ett parametervärde `Stop` för varje fredag vid den angivna tidpunkten.Sekventiellt (stigande) stoppar alla virtuella datorer med en tagg **sequenceStop** som definieras av lämpliga variabler. Mer information om taggvärden och tillgångsvariabler finns i avsnittet Runbooks.Aktivera det relaterade **schemat, Sequenced-StartVM**.|
|Sekvenserade StartVM | 13:00 (UTC), varje måndag | Kör **SequencedStopStart_Parent** runbook med ett parametervärde `Start` för varje måndag vid den angivna tidpunkten. Sekventiellt (fallande) startar alla virtuella datorer med en tagg **i SequenceStart** som definieras av lämpliga variabler. Mer information om taggvärden och variabla tillgångar finns i [Runbooks](#runbooks). Aktivera det relaterade **schemat, Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Användning av lösningen med klassiska virtuella datorer

Om du använder **start-/stopp-virtuella datorer under off-hours-lösningen** för klassiska virtuella datorer bearbetar Automation alla dina virtuella datorer sekventiellt per molntjänst. Virtuella datorer bearbetas fortfarande parallellt mellan olika molntjänster. 

För användning av lösningen med klassiska virtuella datorer behöver du ett Klassiskt Kör som-konto, som inte skapas som standard. Instruktioner om hur du skapar ett klassiskt kör som-konto finns i [Skapa ett klassiskt kör som-konto](automation-create-standalone-account.md#create-a-classic-run-as-account).

Om du har fler än 20 virtuella datorer per molntjänst finns här några rekommendationer:

* Skapa flera scheman med den överordnade runbook **ScheduledStartStop_Parent** och ange 20 virtuella datorer per schema. 
* I schemaegenskaperna använder `VMList` du parametern för att ange VM-namn som en kommaavgränsad lista. 

Annars, om automation-jobbet för den här lösningen körs mer än tre timmar, är det tillfälligt lossas eller stoppas per [gränsen för skälig resurs.](automation-runbook-execution.md#fair-share)

Azure CSP-prenumerationer stöder endast Azure Resource Manager-modellen. Icke-Azure Resource Manager-tjänster är inte tillgängliga i programmet. När **start-/stopp-virtuella datorer under ledig tid-lösningen** körs kan det uppstå fel eftersom den har cmdlets för att hantera klassiska resurser. Mer information om CSP finns [i Tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Om du använder en CSP-prenumeration bör du ange [External_EnableClassicVMs](#variables) variabeln till False efter distributionen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Aktivera lösningen

Börja använda lösningen genom att följa stegen i [Aktivera start-/stopp-start-/start-videolösning](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Visa lösningen

Använd någon av följande mekanismer för att komma åt lösningen när du har aktiverat den:

* Välj **Start/Stop VM** under **Relaterade resurser**i ditt Automation-konto. På sidan Start/Stop VM väljer du **Hantera lösningen** från höger sida av sidan under **Hantera start-/stopp-VM-lösningar**.

* Navigera till arbetsytan Logganalys som är länkad till ditt Automation-konto. När du har valt arbetsytan väljer du **Lösningar** i den vänstra rutan. På sidan Lösningar väljer du lösningen **start-stop-VM[workspace]** i listan.  

Om du väljer lösningen visas lösningssidan **start-stop-vm[workspace].** Här kan du granska viktiga detaljer, till exempel informationen i **StartStopVM-panelen.** Precis som på arbetsytan Log Analytics visar den här panelen ett antal och en grafisk representation av runbook-jobben för den lösning som har startat och avslutats.

![Lösningssida för hantering av automatiseringsuppdatering](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Du kan utföra ytterligare analyser av jobbposterna genom att klicka på donutpanelen. Instrumentpanelen för lösning visar jobbhistorik och fördefinierade loggsökningsfrågor. Växla till den avancerade portalen för logganalys för att söka baserat på dina sökfrågor.

## <a name="update-the-solution"></a>Uppdatera lösningen

Om du har distribuerat en tidigare version av den här lösningen tar du bort den från ditt konto innan du distribuerar en uppdaterad version. Följ stegen för att [ta bort lösningen](#remove-the-solution) och följ sedan stegen för att distribuera [lösningen](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Ta bort lösningen

Om du inte längre behöver använda lösningen kan du ta bort den från Automation-kontot. Om du tar bort lösningen tas runbooks bort. Scheman eller variabler som skapades när lösningen lades till tas inte bort. Ta bort dessa resurser manuellt om du inte använder dem med andra runbooks.

Så här tar du bort lösningen:

1. Välj **Länkad arbetsyta** under **Relaterade resurser**i ditt Automation-konto.

2. Välj **Gå till arbetsyta .**

3. Klicka på **Lösningar** under **Allmänt**. 

4. På sidan Lösningar väljer du lösningen **Start-Stop-VM[Workspace]**. 

5. På sidan **VMManagementSolution[Workspace]** väljer du **Ta bort** på menyn.<br><br> ![Ta bort VM Mgmt-lösning](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Bekräfta att du vill ta bort lösningen i fönstret **Ta bort lösning.**

7. Medan informationen verifieras och lösningen tas bort kan du spåra förloppet under **Meddelanden**, som valts från menyn. Du kommer tillbaka till sidan Lösningar när processen har startats för att ta bort lösningen.

Arbetsytan Automation-konto och Logganalys tas inte bort som en del av den här processen. Om du inte vill behålla log analytics-arbetsytan måste du manuellt ta bort den från Azure-portalen:

1. Sök efter och välj **Log Analytics-arbetsytor**.

2. Välj arbetsytan på sidan **Logganalysarbetsytor.**

3. Välj **Ta bort** på menyn på sidan inställningar för arbetsytan.

4. Om du inte vill behålla Azure Automation-kontokomponenterna kan du ta bort var och en manuellt. Se [Lösningskomponenter](#solution-components).

## <a name="next-steps"></a>Nästa steg

[Aktivera](automation-solution-vm-management-enable.md) **start-/stopp-virtuella datorer under ledig tid för** dina virtuella Azure-datorer.
