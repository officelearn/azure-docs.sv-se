---
title: Start/stoppa virtuella datorer under lösning utanför arbetstid
description: Den här vm-hanteringslösningen startar och stoppar dina virtuella Azure-datorer enligt ett schema och övervakar proaktivt från Azure Monitor-loggar.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: cef3176c99cd57ae229b602feb3c825081fcfe3e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548373"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Starta/stoppa virtuella datorer när de inte används i Azure Automation

Start/Stop virtuella datorer under start av timmar och stoppar dina virtuella Azure-datorer på användardefinierade scheman, ger insikter via Azure Monitor-loggar och skickar valfria e-postmeddelanden med hjälp av [åtgärdsgrupper](../azure-monitor/platform/action-groups.md). Den stöder både Azure Resource Manager och klassiska virtuella datorer för de flesta scenarier. 

Om du vill använda den här lösningen med klassiska virtuella datorer behöver du ett klassiskt RunAs-konto, som inte skapas som standard. Instruktioner om hur du skapar ett klassiskt RunAs-konto finns i [Klassiska körningskonton](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Start/Stop virtuella datorer under ledig tid-lösningen har uppdaterats för att stödja de senaste versionerna av Azure-modulerna som är tillgängliga.

Den här lösningen ger ett decentraliserat lågprisautomationsalternativ för användare som vill optimera sina vm-kostnader. Med den här lösningen kan du:

- [Schemalägg virtuella datorer så att de startar och stoppar](automation-solution-vm-management-config.md#schedule).
- Schemalägg virtuella datorer så att de startar och slutar i stigande ordning med hjälp av [Azure-taggar](automation-solution-vm-management-config.md#tags) (stöds inte för klassiska virtuella datorer).
- Automatiska virtuella datorer baserat på [låg CPU-användning](automation-solution-vm-management-config.md#cpuutil).

Följande är begränsningar med den aktuella lösningen:

- Den här lösningen hanterar virtuella datorer i alla regioner, men kan bara användas i samma prenumeration som ditt Azure Automation-konto.
- Den här lösningen är tillgänglig i Azure och Azure Government för alla regioner som stöder en Log Analytics-arbetsyta, ett Azure Automation-konto och aviseringar. Azure Government-regioner stöder för närvarande inte e-postfunktioner.

> [!NOTE]
> Om du använder lösningen för klassiska virtuella datorer bearbetas alla dina virtuella datorer sekventiellt per molntjänst. Virtuella datorer bearbetas fortfarande parallellt mellan olika molntjänster. Om du har fler än 20 virtuella datorer per molntjänst rekommenderar vi att du skapar flera scheman med den överordnade **runbook-ScheduledStartStop_Parent** och anger 20 virtuella datorer per schema. Ange som en kommaavgränsad lista i schemaegenskaperna, VM-namn i parametern **VMList.** Annars, om automation-jobbet för den här lösningen körs mer än tre timmar, är det tillfälligt avlagrad eller stoppas per [gränsen för skälig resurs.](automation-runbook-execution.md#fair-share)
>
> Azure Cloud Solution Provider (Azure CSP)-prenumerationer stöder endast Azure Resource Manager-modellen, icke-Azure Resource Manager-tjänster är inte tillgängliga i programmet. När Start/Stop-lösningen körs kan det uppstå fel eftersom cmdlets har cmdlets för att hantera klassiska resurser. Mer information om CSP finns [i Tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Om du använder en CSP-prenumeration bör du ändra [**variabeln External_EnableClassicVMs**](#variables) till **False** efter distributionen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav

Runbooks för den här lösningen fungerar med ett [Azure Run As-konto](automation-create-runas-account.md). Kontot Kör som är den önskade autentiseringsmetoden, eftersom den använder certifikatautentisering i stället för ett lösenord som kan upphöra att gälla eller ändras ofta.

Vi rekommenderar att du använder ett separat Automation-konto för start/stop-VM-lösningen. Detta beror på att Azure-modulversioner ofta uppgraderas och deras parametrar kan ändras. Start/Stop VM-lösningen uppgraderas inte på samma kadens så det kanske inte fungerar med nyare versioner av cmdlets som används. Vi rekommenderar också att du testar moduluppdateringar i ett testautomationskonto innan du importerar dem i ditt eller dina automationskonto för produktion.

### <a name="permissions"></a>Behörigheter

Det finns vissa behörigheter som en användare måste ha för att distribuera start/stop-virtuella datorer under avstämningstimmarslösning. Dessa behörigheter är olika om du använder en förskapad Automation-konto- och Logganalysarbetsyta eller skapar nya under distributionen. Om du är deltagare i prenumerationen och en global administratör i din Azure Active Directory-klient behöver du inte konfigurera följande behörigheter. Om du inte har dessa rättigheter eller behöver konfigurera en anpassad roll läser du de behörigheter som krävs nedan.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Befintlig arbetsyta för automationskonto och logganalys

För att distribuera start/stop-virtuella datorer under lediga timmar till en befintlig Automation-konto- och Logganalysarbetsyta kräver användaren som distribuerar lösningen följande behörigheter för **resursgruppen**. Mer information om roller finns i [Anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md).

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

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nytt Automation-konto och en ny Log Analytics-arbetsyta

Om du vill distribuera start-/stopp-virtuella datorer under ledig tid till en ny Automation-konto- och Logganalysarbetsyta behöver användaren som distribuerar lösningen de behörigheter som definierats i föregående avsnitt samt följande behörigheter:

- Medadministratör på prenumeration - Det behövs bara för att skapa det klassiska körningskontot om du ska hantera klassiska virtuella datorer. [Klassiska RunAs-konton skapas](automation-create-standalone-account.md#classic-run-as-accounts) inte längre som standard.
- Medlem i rollen Utvecklare av Azure Active **Directory-program.** [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Mer information om hur du konfigurerar Kör som konton finns i [Behörigheter för att konfigurera Kör som-konton](manage-runas-account.md#permissions).
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

Den här lösningen innehåller förkonfigurerade runbooks, scheman och integrering med Azure Monitor-loggar så att du kan skräddarsy start och avstängning av dina virtuella datorer så att de passar dina affärsbehov.

### <a name="runbooks"></a>Runbooks

I följande tabell visas de runbooks som distribueras till ditt Automation-konto med den här lösningen. Gör inga ändringar i runbookkoden. Skriv i stället en egen runbook för nya funktioner.

> [!IMPORTANT]
> Kör inte direkt någon runbook med *underordnad* bifogad till sitt namn.

Alla överordnade runbooks inkluderar _parametern WhatIf._ När den är inställd på **True**stöder _WhatIf_ detaljer om det exakta beteende som runbooken tar när den körs utan _WhatIf-parametern_ och validerar rätt virtuella datorer som riktas. En runbook utför bara sina definierade åtgärder när parametern _WhatIf_ är inställd på **False**.

|Runbook | Parametrar | Beskrivning|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject (VMObject) <br> Varningsåtgärd <br> WebHookURI (olikartade) | Anropad från den överordnade runbooken. Den här runbooken skapar aviseringar per resurs för Scenariot För Automatisk topp.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Sant eller falskt  | Skapar eller uppdaterar Azure-aviseringsregler för virtuella datorer i den riktade prenumerationen eller resursgrupperna. <br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2, vm3_.<br> *WhatIf* validerar runbook-logiken utan att köra.|
|AutoStop_Disable | ingen | Inaktiverar AutoStop-aviseringar och standardschema.|
|AutoStop_VM_Child | WebHookData (Olikartade) | Anropad från den överordnade runbooken. Varningsregler anropar den här runbooken för att stoppa den klassiska virtuella datorn.|
|AutoStop_VM_Child_ARM | WebHookData (Olikartade) |Anropad från den överordnade runbooken. Varningsregler anropar den här runbooken för att stoppa den virtuella datorn.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Åtgärd: Start eller stopp<br> VMList  | Den här runbooken används för att utföra åtgärdsstart eller stopp i den klassiska VM-gruppen av Molntjänster.<br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2, vm3_. |
|ScheduledStartStop_Child | VMName <br> Åtgärd: Start eller stopp <br> ResourceGroupName | Anropad från den överordnade runbooken. Utför en start- eller stoppåtgärd för det schemalagda stoppet.|
|ScheduledStartStop_Child_Classic | VMName<br> Åtgärd: Start eller stopp<br> ResourceGroupName | Anropad från den överordnade runbooken. Utför en start- eller stoppåtgärd för det schemalagda stoppet för klassiska virtuella datorer. |
|ScheduledStartStop_Parent | Åtgärd: Start eller stopp <br>VMList <br> WhatIf: Sant eller falskt | Den här inställningen påverkar alla virtuella datorer i prenumerationen. Redigera **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** om du bara vill köra på dessa riktade resursgrupper. Du kan också utesluta specifika virtuella datorer genom att uppdatera **External_ExcludeVMNames** variabeln.<br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2, vm3_.<br> _WhatIf_ validerar runbook-logiken utan att köra.|
|SequencedStartStop_Parent | Åtgärd: Start eller stopp <br> WhatIf: Sant eller falskt<br>VMList| Skapa taggar med namnet **sequencestart** och **sequencestop** på varje virtuell dator som du vill sekvensera start/stopp-aktivitet för. Dessa taggnamn är skiftlägeskänsliga. Taggens värde ska vara ett positivt heltal (1, 2, 3) som motsvarar den ordning i vilken du vill starta eller stoppa. <br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2, vm3_. <br> _WhatIf_ validerar runbook-logiken utan att köra. <br> **Obs:** Virtuella datorer måste vara inom resursgrupper som definieras som External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames och External_ExcludeVMNames i Azure Automation-variabler. De måste ha lämpliga taggar för att åtgärder ska börja gälla.|

### <a name="variables"></a>Variabler

I följande tabell visas de variabler som skapats i ditt Automation-konto. Ändra endast variabler som är fördefinierade med **Extern**. Om du ändrar variabler som föregås av **interna** orsaker kan oönskade effekter inte ändras.

|Variabel | Beskrivning|
|---------|------------|
|External_AutoStop_Condition | Den villkorliga operatorn som krävs för att konfigurera villkoret innan en avisering utlöses. Godtagbara värden är **GreaterThan,** **GreaterThanOrEqual,** **LessThan**och **LessThanOrEqual**.|
|External_AutoStop_Description | Aviseringen för att stoppa den virtuella datorn om processorprocenten överskrider tröskelvärdet.|
|External_AutoStop_Frequency | Utvärderingsfrekvensen för regel. Den här parametern accepterar indata i tidsspannformat. Möjliga värden är från 5 minuter till 6 timmar. |
|External_AutoStop_MetricName | Namnet på det prestandamått som Azure Alert-regeln ska konfigureras för.|
|External_AutoStop_Severity | Allvarlighetsgrad för måttaviseringen, som kan variera från 0 till 4. |
|External_AutoStop_Threshold | Tröskelvärdet för Azure Alert-regeln som anges i variabeln _External_AutoStop_MetricName_. Procentvärden kan variera mellan 1 och 100.|
|External_AutoStop_TimeAggregationOperator | Tidsaggregeringsoperatorn, som används på den valda fönsterstorleken för att utvärdera villkoret. Godtagbara värden är **Medel,** **Minimum**, **Maximum**, **Total**och **Last**.|
|External_AutoStop_TimeWindow | Fönsterstorleken under vilken Azure analyserar valda mått för att utlösa en avisering. Den här parametern accepterar indata i tidsspannformat. Möjliga värden är från 5 minuter till 6 timmar.|
|External_EnableClassicVMs| Anger om klassiska virtuella datorer är inriktade på lösningen. Standardvärdet är Sant. Detta bör ställas in på False för CSP-prenumerationer. Klassiska virtuella datorer kräver ett [klassiskt run-as-konto](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Ange VM-namn som ska uteslutas, separera namn med hjälp av ett kommatecken utan blanksteg. Detta är begränsat till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i den här kommaavgränsade listan kan virtuella datorer som är inställda på att uteslutas startas eller stoppas av misstag.|
|External_Start_ResourceGroupNames | Anger en eller flera resursgrupper som avgränsar värden med hjälp av ett kommatecken som är avsett för startåtgärder.|
|External_Stop_ResourceGroupNames | Anger en eller flera resursgrupper som avgränsar värden med hjälp av ett kommatecken som är avsett för stoppåtgärder.|
|External_WaitTimeForVMRetrySeconds |Väntetiden i sekunder för de åtgärder som ska utföras på de virtuella datorerna för den sekvenserade start-/stopp-runbooken.<br> Standardvärdet är 2100 sekunder och stöder konfigurering till ett maximalt värde på 10800 eller tre timmar.|
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|
|Internal_AutoSnooze_ARM_WebhookURI | Anger Webhook URI som anropas för AutoStop-scenariot för klassiska virtuella datorer.|
|Internal_AutoSnooze_WebhookUri | Anger Webhook URI som anropas för Scenariot För Automatisk topp.|
|Internal_AzureSubscriptionId | Anger Azure-prenumerations-ID.|
|Internal_ResourceGroupName | Anger resursgruppsnamnet för automationskontot.|

>[!NOTE]
>För variabeln **External_WaitTimeForVMRetryInSeconds**har standardvärdet uppdaterats från 600 till 2100. Med den här variabeln kan runbooken **sekvenserade start/stop-scenario** vänta på de underordnade åtgärderna i angivet antal sekunder innan du fortsätter med nästa åtgärd.
>

I alla scenarier är **External_Start_ResourceGroupNames,** **External_Stop_ResourceGroupNames**och **External_ExcludeVMNames** variabler nödvändiga för att rikta virtuella datorer, med undantag för att tillhandahålla en kommaavgränsad lista över virtuella datorer för **AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent**och **ScheduledStartStop_Parent** runbooks. Det vill säga att dina virtuella datorer måste finnas i målgrupper för att starta och stoppa åtgärder som ska inträffa. Logiken fungerar som Azure-principen, eftersom du kan rikta prenumerationen eller resursgruppen och ha åtgärder som ärvts av nyskapade virtuella datorer. Den här metoden undviker att behöva behålla ett separat schema för varje virtuell dator och hantera starter och stopp i skala.

### <a name="schedules"></a>Scheman

I följande tabell visas var och en av standardscheman som skapats i ditt Automation-konto.Du kan ändra dem eller skapa egna anpassade scheman.Som standard är alla scheman inaktiverade förutom **Scheduled_StartVM** och **Scheduled_StopVM**.

Du bör inte aktivera alla scheman, eftersom detta kan skapa överlappande schemaåtgärder. Det är bäst att avgöra vilka optimeringar du vill utföra och ändra därefter. Mer förklaring finns i exempelscenarierna i översiktsavsnittet.

|Schemalägg namn | Frequency | Beskrivning|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Var 8:e timme | Kör AutoStop_CreateAlert_Parent runbook var 8:e timme, vilket i sin tur stoppar de VM-baserade värdena i External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames och External_ExcludeVMNames i Azure Automation-variabler. Du kan också ange en kommaavgränsad lista över virtuella datorer med parametern VMList.|
|Scheduled_StopVM | Användardefinierad, daglig | Kör Scheduled_Parent runbook med en parameter _stoppa_ varje dag vid den angivna tidpunkten.Stoppar automatiskt alla virtuella datorer som uppfyller de regler som definieras av tillgångsvariabler.Aktivera det relaterade schemat, **Schemalagd-StartVM**.|
|Scheduled_StartVM | Användardefinierad, daglig | Kör Scheduled_Parent runbook med en parameter _för Start_ varje dag vid den angivna tidpunkten. Startar automatiskt alla virtuella datorer som uppfyller de regler som definieras av lämpliga variabler.Aktivera det relaterade schemat, **Scheduled-StopVM**.|
|Sekvenserade-StopVM | 01:00 (UTC), varje fredag | Kör Sequenced_Parent runbook med en parameter _för Stopp_ varje fredag vid den angivna tidpunkten.Sekventiellt (stigande) stoppar alla virtuella datorer med en tagg **sequenceStop** som definieras av lämpliga variabler. Mer information om taggvärden och tillgångsvariabler finns i avsnittet Runbooks.Aktivera det relaterade **schemat, Sequenced-StartVM**.|
|Sekvenserade StartVM | 13:00 (UTC), varje måndag | Kör Sequenced_Parent runbook med en parameter _för Start_ varje måndag vid den angivna tidpunkten. Sekventiellt (fallande) startar alla virtuella datorer med en tagg **i SequenceStart** som definieras av lämpliga variabler. Mer information om taggvärden och tillgångsvariabler finns i avsnittet Runbooks. Aktivera det relaterade **schemat, Sequenced-StopVM**.|

## <a name="enable-the-solution"></a>Aktivera lösningen

Börja använda lösningen genom att utföra stegen i [lösningen Aktivera start/stopp- och start-/stopp-datorer](automation-solution-vm-management-enable.md).

## <a name="viewing-the-solution"></a>Visa lösningen

Du kan komma åt lösningen när du har aktiverat den på något av följande sätt:

* Välj **Start/Stop VM** under **Relaterade resurser**i ditt Automation-konto. På sidan **Start/Stop VM** väljer du **Hantera lösningen** från sidans högra sida under avsnittet Hantera **start-/stopp-VM-lösningar**.

* Navigera till arbetsytan Log Analytics som är länkad till ditt Automation-konto och när du har valt arbetsytan väljer du **Lösningar** i den vänstra rutan. På sidan **Lösningar** väljer du lösningen **start-stop-VM[workspace]** i listan.  

Om du väljer lösningen visas lösningssidan **start-stop-vm[workspace].** Här kan du granska viktiga detaljer som **StartStopVM-panelen.** Precis som på arbetsytan Log Analytics visar den här panelen ett antal och en grafisk representation av runbook-jobben för den lösning som har startat och avslutats.

![Lösningssida för hantering av automatiseringsuppdatering](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Härifrån kan du utföra ytterligare analys av jobbposterna genom att klicka på donutpanelen. Instrumentpanelen för lösning visar jobbhistorik och fördefinierade loggsökningsfrågor. Växla till den avancerade portalen för logganalys för att söka baserat på dina sökfrågor.

## <a name="update-the-solution"></a>Uppdatera lösningen

Om du har distribuerat en tidigare version av den här lösningen måste du först ta bort den från ditt konto innan du distribuerar en uppdaterad version. Följ stegen för att [ta bort lösningen](#remove-the-solution) och följ sedan stegen för att distribuera [lösningen](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Ta bort lösningen

Om du bestämmer dig för att du inte längre behöver använda lösningen kan du ta bort den från Automation-kontot. Om du tar bort lösningen tas runbooks bort. Scheman eller variabler som skapades när lösningen lades till tas inte bort. De tillgångar som du behöver ta bort manuellt om du inte använder dem med andra runbooks.

Så här tar du bort lösningen:

1. Välj **Länkad arbetsyta**under **Relaterade resurser**i ditt Automation-konto.

2. Välj **Gå till arbetsyta .**

3. Under **Allmänt**väljer du **Lösningar**. 

4. På sidan **Lösningar** väljer du lösningen **Start-Stop-VM[Workspace]**. Välj **Ta bort**på sidan **VMManagementSolution[Workspace]** på menyn .<br><br> ![Ta bort VM Mgmt-lösning](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. Bekräfta att du vill ta bort lösningen i fönstret **Ta bort lösning.**

6. Medan informationen verifieras och lösningen tas bort kan du spåra dess förlopp under **Meddelanden** från menyn. Du returneras till sidan **Lösningar** när processen för att ta bort lösningen startar.

Arbetsytan Automation-konto och Logganalys tas inte bort som en del av den här processen. Om du inte vill behålla log analytics-arbetsytan måste du ta bort den manuellt. Detta kan åstadkommas från Azure-portalen:

1. Sök efter och välj **Log Analytics-arbetsytor i Azure-portalen**.

2. Välj arbetsytan på sidan **Logganalysarbetsytor.**

3. Välj **Ta bort** på menyn på sidan inställningar för arbetsytan.

Om du inte vill behålla Azure Automation-kontokomponenterna kan du ta bort var och en manuellt. En lista över runbooks, variabler och scheman som skapats av lösningen finns i [lösningskomponenterna](#solution-components).

## <a name="next-steps"></a>Nästa steg

[Aktivera](automation-solution-vm-management-enable.md) Start/Stop under ledig tid-lösning för dina virtuella Azure-datorer.
