---
title: Start/stoppa virtuella datorer under lösning utanför arbetstid
description: Den här vm-hanteringslösningen startar och stoppar dina virtuella Azure Resource Manager-datorer enligt ett schema och övervakar proaktivt från Azure Monitor-loggar.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: cbf181b9a6d3860854c7b61cca0e6c50810cced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278550"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Starta/stoppa virtuella datorer när de inte används i Azure Automation

Start/Stop virtuella datorer under start av timmar och stoppar dina virtuella Azure-datorer på användardefinierade scheman, ger insikter via Azure Monitor-loggar och skickar valfria e-postmeddelanden med hjälp av [åtgärdsgrupper](../azure-monitor/platform/action-groups.md). Den stöder både Azure Resource Manager och klassiska virtuella datorer för de flesta scenarier. Om du vill använda den här lösningen med klassiska virtuella datorer behöver du ett klassiskt RunAs-konto, som inte skapas som standard. Instruktioner om hur du skapar ett klassiskt RunAs-konto finns i [Klassiska körningskonton](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Start/Stop-virtuella datorer under ledig tid-lösningen har testats med Azure-moduler som importeras till ditt Automation-konto när du distribuerar lösningen. Lösningen fungerar för närvarande inte med nyare versioner av Azure-modulen. Detta påverkar bara det Automation-konto som du använder för att köra start-/stopp-virtuella datorer under ledig tid-lösning. Du kan fortfarande använda nyare versioner av Azure-modulen i dina andra Automation-konton, enligt beskrivningen i [Så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md)

Den här lösningen ger ett decentraliserat lågprisautomationsalternativ för användare som vill optimera sina vm-kostnader. Med den här lösningen kan du:

- Schemalägg virtuella datorer så att de startar och stoppar.
- Schemalägg virtuella datorer så att de startar och slutar i stigande ordning med hjälp av Azure-taggar (stöds inte för klassiska virtuella datorer).
- Automatiska virtuella datorer baserat på låg CPU-användning.

Följande är begränsningar med den aktuella lösningen:

- Den här lösningen hanterar virtuella datorer i alla regioner, men kan bara användas i samma prenumeration som ditt Azure Automation-konto.
- Den här lösningen är tillgänglig i Azure och AzureGov för alla regioner som stöder en Log Analytics-arbetsyta, ett Azure Automation-konto och aviseringar. AzureGov-regioner stöder för närvarande inte e-postfunktioner.

> [!NOTE]
> Om du använder lösningen för klassiska virtuella datorer bearbetas alla dina virtuella datorer sekventiellt per molntjänst. Virtuella datorer bearbetas fortfarande parallellt mellan olika molntjänster. Om du har fler än 20 virtuella datorer per molntjänst rekommenderar vi att du skapar flera scheman med den överordnade **runbook-ScheduledStartStop_Parent** och anger 20 virtuella datorer per schema. Ange som en kommaavgränsad lista i schemaegenskaperna, VM-namn i parametern **VMList.** Annars, om automation-jobbet för den här lösningen körs mer än tre timmar, är det tillfälligt avlagrad eller stoppas per [gränsen för skälig resurs.](automation-runbook-execution.md#fair-share)
>
> Azure Cloud Solution Provider (Azure CSP)-prenumerationer stöder endast Azure Resource Manager-modellen, icke-Azure Resource Manager-tjänster är inte tillgängliga i programmet. När Start/Stop-lösningen körs kan det uppstå fel eftersom cmdlets har cmdlets för att hantera klassiska resurser. Mer information om CSP finns [i Tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Om du använder en CSP-prenumeration bör du ändra [**variabeln External_EnableClassicVMs**](#variables) till **False** efter distributionen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav

Runbooks för den här lösningen fungerar med ett [Azure Run As-konto](automation-create-runas-account.md). Kontot Kör som är den önskade autentiseringsmetoden, eftersom den använder certifikatautentisering i stället för ett lösenord som kan upphöra att gälla eller ändras ofta.

Vi rekommenderar att du använder ett separat Automation-konto för start/stop-VM-lösningen. Detta beror på att Azure-modulversioner ofta uppgraderas och deras parametrar kan ändras. Start/Stop VM-lösningen uppgraderas inte på samma kadens så det kanske inte fungerar med nyare versioner av cmdlets som används. Vi rekommenderar också att du testar moduluppdateringar i ett testautomationskonto innan du importerar dem i ditt eller dina automationskonto för produktion.

### <a name="permissions-needed-to-deploy"></a>Behörigheter som behövs för att distribuera

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

## <a name="deploy-the-solution"></a>Distribuera lösningen

Utför följande steg för att lägga till start-/stopp-virtuella datorer under ledig tid i ditt Automation-konto och konfigurera sedan variablerna för att anpassa lösningen.

1. Välj **Start/Stop VM** under **Relaterade resurser**från ett automationskonto. Härifrån kan du klicka på **Läs mer om och aktivera lösningen**. Om du redan har distribuerat en Start/Stop VM-lösning kan du välja den genom att klicka på **Hantera lösningen** och hitta den i listan.

   ![Aktivera från automationskonto](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Du kan också skapa den var som helst i Azure-portalen genom att klicka på **Skapa en resurs**. Skriv ett nyckelord som **Start** eller **Start/Stopp**på Marketplace-sidan. När du börjar skriva filtreras listan baserat på det du skriver. Du kan också skriva in ett eller flera nyckelord från lösningens fullständiga namn och sedan trycka på Retur. Välj **Start/Stoppa virtuella datorer under lediga timmar** från sökresultaten.

2. På sidan **Start/Stop-datorer under ledig tid** för den valda lösningen granskar du sammanfattningsinformationen och klickar sedan på **Skapa**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. Sidan **Lägg till lösning** visas. Du uppmanas att konfigurera lösningen innan du kan importera den till din Automation-prenumeration.

   ![Sidan Lägg till lösning för VM-hantering](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Välj **Arbetsyta**på sidan **Lägg till lösning** . Välj en Log Analytics-arbetsyta som är länkad till samma Azure-prenumeration som Automation-kontot finns i. Om du inte har någon arbetsyta väljer du **Skapa ny arbetsyta**. Gör följande på **sidan Logganalysarbetsyta:**
   - Ange ett namn för den nya **log analytics-arbetsytan**, till exempel "ContosoLAWorkspace".
   - Välj en **prenumeration** som du vill länka till genom att välja från listrutan, om den valda standardinställningen inte är lämplig.
   - För **Resursgrupp**kan du skapa en ny resursgrupp eller välja en befintlig.
   - Välj en **plats**.
   - Välj en **prisnivå**. Välj alternativet **Per GB (fristående).** Azure Monitor-loggar har uppdaterade [priser](https://azure.microsoft.com/pricing/details/log-analytics/) och per GB-nivå är det enda alternativet.

   > [!NOTE]
   > När du aktiverar lösningar går det endast att länka en Log Analytics-arbetsyta och ett Automation-konto i vissa regioner.
   >
   > En lista över mappningspar som stöds finns i [Regionmappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

5. När du har lämnat den information som krävs på **arbetsytan Log Analytics** klickar du på **Skapa**. Du kan spåra dess förlopp under **Meddelanden** från menyn, som returnerar dig till sidan **Lägg till lösning** när du är klar.
6. Välj **Automation-konto**på sidan **Lägg till lösning** . Om du skapar en ny Log Analytics-arbetsyta kan du skapa ett nytt Automation-konto som ska kopplas till det, eller välja ett befintligt Automation-konto som inte redan är länkat till en Log Analytics-arbetsyta. Välj ett befintligt Automation-konto eller klicka på **Skapa ett Automation-konto**och ange följande information på sidan **Lägg till automationskonto:**
   - I fältet **namn** anger du namnet på Automation-kontot.

     Alla andra alternativ fylls i automatiskt baserat på den valda logganalysarbetsytan. Det går inte att ändra dessa alternativ. Ett Azure kör som-konto är standardmetoden för autentisering för runbooks som ingår i den här lösningen. När du har klickat på **OK**valideras konfigurationsalternativen och Automation-kontot skapas. Du kan spåra förloppet under **Meddelanden** på menyn.

7. Slutligen väljer du **Konfiguration**på sidan **Lägg till lösning** . Sidan **Parametrar** visas.

   ![Sidan Parametrar för lösning](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Här uppmanas du att:
   - Ange **namn på målresursgrupper**. Dessa värden är resursgruppsnamn som innehåller virtuella datorer som ska hanteras av den här lösningen. Du kan ange mer än ett namn och separera varje med hjälp av ett kommatecken (värden är inte skiftlägeskänsliga). Användning av jokertecken stöds om du vill inkludera virtuella datorer i alla resursgrupper i prenumerationen. Det här värdet lagras i **variablerna External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames.**
   - Ange **den vm-undantagslista (strängen)**. Det här värdet är namnet på en eller flera virtuella datorer från målresursgruppen. Du kan ange mer än ett namn och separera varje med hjälp av ett kommatecken (värden är inte skiftlägeskänsliga). Det går att använda ett jokertecken. Det här värdet lagras i **variabeln External_ExcludeVMNames.**
   - Välj ett **schema**. Välj ett datum och en tid för schemat. Ett återkommande dagligt schema skapas från och med den tid du valde. Det går inte att välja en annan region. Information om hur du konfigurerar schemat till den specifika tidszonen efter att lösningen har konfigurerats finns i [Ändra start- och avstängningsschemat](#modify-the-startup-and-shutdown-schedules).
   - Om du vill ta emot **e-postaviseringar** från en åtgärdsgrupp godkänner du standardvärdet **Ja** och anger en giltig e-postadress. Om du väljer **Nej** men bestämmer dig vid ett senare tillfälle att du vill få e-postmeddelanden kan du uppdatera [åtgärdsgruppen](../azure-monitor/platform/action-groups.md) som skapas med giltiga e-postadresser avgränsade med ett kommatecken. Du måste också aktivera följande varningsregler:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Standardvärdet för **Mål ResourceGroup-namn** är en **&ast;**. Detta är inriktat på alla virtuella datorer i en prenumeration. Om du inte vill att lösningen ska inriktas på alla virtuella datorer i din prenumeration måste det här värdet uppdateras till en lista över resursgruppsnamn innan schemana aktiveras.

8. När du har konfigurerat de första inställningarna som krävs för lösningen klickar du på **OK** för att stänga sidan **Parametrar** och välj **Skapa**. När alla inställningar har validerats distribueras lösningen till din prenumeration. Den här processen kan ta flera sekunder att slutföra och du kan spåra dess förlopp under **Meddelanden** från menyn.

> [!NOTE]
> Om du har en Azure Cloud Solution Provider-prenumeration (Azure CSP) går du i ditt Automation-konto i ditt Automation-konto till **Variabler** under **Delade resurser** och anger [**External_EnableClassicVMs**](#variables) variabeln till **False**. Detta hindrar lösningen från att leta efter klassiska vm-resurser.

## <a name="scenarios"></a>Scenarier

Lösningen innehåller tre olika scenarier. Dessa scenarier är:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenario 1: Start-/stopp-virtuella datorer enligt ett schema

Det här scenariot är standardkonfigurationen när du först distribuerar lösningen. Du kan till exempel konfigurera den så att den stoppa alla virtuella datorer i en prenumeration när du lämnar arbetet på kvällen och starta dem på morgonen när du är tillbaka på kontoret. När du konfigurerar scheman **Schemalagd-StartVM** och **Schemalagd-StopVM** under distributionen startar och stoppar de riktade virtuella datorer. Konfigurera den här lösningen för att bara stoppa virtuella datorer stöds, se [Ändra start- och avstängningsscheman](#modify-the-startup-and-shutdown-schedules) för att lära dig hur du konfigurerar ett anpassat schema.

> [!NOTE]
> Tidszonen är din aktuella tidszon när du konfigurerar parametern för schematid. Den lagras dock i UTC-format i Azure Automation. Du behöver inte göra någon tidszonskonvertering eftersom detta hanteras under distributionen.

Du styr vilka virtuella datorer som omfattas genom att konfigurera följande variabler: **External_Start_ResourceGroupNames,** **External_Stop_ResourceGroupNames**och **External_ExcludeVMNames**.

Du kan aktivera antingen inriktning åtgärden mot en prenumeration och resursgrupp, eller inriktning på en viss lista över virtuella datorer, men inte båda.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta start- och stoppåtgärder mot en prenumerations- och resursgrupp

1. Konfigurera **variablerna External_Stop_ResourceGroupNames** och **External_ExcludeVMNames** för att ange mål-virtuella datorer.
2. Aktivera och uppdatera schemana **För schemalagd startVM** och **Schemalagd-StopVM.**
3. Kör **ScheduledStartStop_Parent** runbook med parametern ACTION inställd på **start** och parametern WHATIF inställd på **True** för att förhandsgranska ändringarna.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta in start- och stoppåtgärden efter VM-lista

1. Kör **ScheduledStartStop_Parent** runbook med parametern ACTION inställd **på**start, lägg till en kommaavgränsad lista över virtuella datorer i parametern *VMList* och ställ sedan in parametern WHATIF på **True**. Förhandsgranska ändringarna.
1. Konfigurera **parametern External_ExcludeVMNames** med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3).
1. Det här scenariot respekterar inte **variablerna External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames.** I det här scenariot måste du skapa ett eget automationsschema. Mer information finns [i Schemalägga en runbook i Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Värdet för **Mål ResourceGroup-namn** lagras som värde för både **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames**. För ytterligare granularitet kan du ändra var och en av dessa variabler för att rikta olika resursgrupper. Använd **External_Start_ResourceGroupNames External_Stop_ResourceGroupNames**för startåtgärd och för stoppåtgärd **.** Virtuella datorer läggs automatiskt till i start- och stoppscheman.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenario 2: Starta/stoppa VMS i följd med hjälp av taggar

I en miljö som innehåller två eller flera komponenter på flera virtuella datorer som stöder en distribuerad arbetsbelastning är det viktigt att stödja den sekvens i vilken komponenter startas och stoppas i ordning. Du kan utföra det här scenariot genom att utföra följande steg:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta start- och stoppåtgärder mot en prenumerations- och resursgrupp

1. Lägg till en **sequencestart** och en **sequencestop-tagg** med ett positivt heltalsvärde i virtuella datorer som är inriktade **på External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** variabler. Start- och stoppåtgärder utförs i stigande ordning. Mer information om hur du taggar en virtuell dator finns [i Tagga en virtuell Windows-dator i Azure](../virtual-machines/windows/tag.md) och [tagga en virtuell Linux-dator i Azure](../virtual-machines/linux/tag.md).
1. Ändra scheman **Sequenced-StartVM** och **Sequenced-StopVM** till det datum och den tid som uppfyller dina krav och aktivera schemat.
1. Kör **SequencedStartStop_Parent** runbook med parametern ACTION inställd på **start** och parametern WHATIF inställd på **True** för att förhandsgranska ändringarna.
1. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar mot virtuella produktions-datorer. När du är klar kör du runbooken manuellt med parametern inställd på **Falskt**eller låt automationsschemat **Sequenced-StartVM** och **Sequenced-StopVM** köras automatiskt enligt det föreskrivna schemat.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta in start- och stoppåtgärden efter VM-lista

1. Lägg till en **sequencestart** och en **sequencestop-tagg** med ett positivt heltalsvärde i virtuella datorer som du planerar att lägga till i **parametern VMList.**
1. Kör **SequencedStartStop_Parent** runbook med parametern ACTION inställd **på**start, lägg till en kommaavgränsad lista över virtuella datorer i parametern *VMList* och ställ sedan in parametern WHATIF på **True**. Förhandsgranska ändringarna.
1. Konfigurera **parametern External_ExcludeVMNames** med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3).
1. Det här scenariot respekterar inte **variablerna External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames.** I det här scenariot måste du skapa ett eget automationsschema. Mer information finns [i Schemalägga en runbook i Azure Automation](../automation/automation-schedules.md).
1. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar mot virtuella produktions-datorer. När du är klar kör du automatiskt övervaknings-och diagnostik-/övervakning-action-groupsrunbook med parametern inställd på **Falskt**eller låt automationsschemat **Sequenced-StartVM** och **Sequenced-StopVM** köras automatiskt enligt ditt föreskrivna schema.

## <a name="solution-components"></a>Lösningskomponenter

Den här lösningen innehåller förkonfigurerade runbooks, scheman och integrering med Azure Monitor-loggar så att du kan skräddarsy start och avstängning av dina virtuella datorer så att de passar dina affärsbehov.

### <a name="runbooks"></a>Runbooks

I följande tabell visas de runbooks som distribueras till ditt Automation-konto med den här lösningen. Gör inga ändringar i runbookkoden. Skriv i stället en egen runbook för nya funktioner.

> [!IMPORTANT]
> Kör inte direkt någon runbook med "underordnad" bifogad till sitt namn.

Alla överordnade runbooks inkluderar _parametern WhatIf._ När den är inställd på **True**stöder _WhatIf_ detaljer om det exakta beteende som runbooken tar när den körs utan _WhatIf-parametern_ och validerar rätt virtuella datorer som riktas. En runbook utför bara sina definierade åtgärder när parametern _WhatIf_ är inställd på **False**.

|Runbook | Parametrar | Beskrivning|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject (VMObject) <br> Varningsåtgärd <br> WebHookURI (olikartade) | Anropad från den överordnade runbooken. Den här runbooken skapar aviseringar per resurs för Scenariot För Automatisk topp.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Sant eller falskt  | Skapar eller uppdaterar Azure-aviseringsregler för virtuella datorer i den riktade prenumerationen eller resursgrupperna. <br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2, vm3_.<br> *WhatIf* validerar runbook-logiken utan att köra.|
|AutoStop_Disable | ingen | Inaktiverar AutoStop-aviseringar och standardschema.|
|AutoStop_StopVM_Child | WebHookData (Olikartade) | Anropad från den överordnade runbooken. Varningsregler anropar den här runbooken för att stoppa den virtuella datorn.|
|Bootstrap_Main | ingen | Används en gång för att ställa in bootstrap-konfigurationer som webhookURI, som vanligtvis inte är tillgängliga från Azure Resource Manager. Den här runbooken tas bort automatiskt vid lyckad distribution.|
|ScheduledStartStop_Child | VMName <br> Åtgärd: Start eller stopp <br> ResourceGroupName | Anropad från den överordnade runbooken. Utför en start- eller stoppåtgärd för det schemalagda stoppet.|
|ScheduledStartStop_Parent | Åtgärd: Start eller stopp <br>VMList <br> WhatIf: Sant eller falskt | Den här inställningen påverkar alla virtuella datorer i prenumerationen. Redigera **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** om du bara vill köra på dessa riktade resursgrupper. Du kan också utesluta specifika virtuella datorer genom att uppdatera **External_ExcludeVMNames** variabeln.<br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2, vm3_.<br> _WhatIf_ validerar runbook-logiken utan att köra.|
|SequencedStartStop_Parent | Åtgärd: Start eller stopp <br> WhatIf: Sant eller falskt<br>VMList| Skapa taggar med namnet **sequencestart** och **sequencestop** på varje virtuell dator som du vill sekvensera start/stopp-aktivitet för. Dessa taggnamn är skiftlägeskänsliga. Taggens värde ska vara ett positivt heltal (1, 2, 3) som motsvarar den ordning i vilken du vill starta eller stoppa. <br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2, vm3_. <br> _WhatIf_ validerar runbook-logiken utan att köra. <br> **Obs:** Virtuella datorer måste vara inom resursgrupper som definieras som External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames och External_ExcludeVMNames i Azure Automation-variabler. De måste ha lämpliga taggar för att åtgärder ska börja gälla.|

### <a name="variables"></a>Variabler

I följande tabell visas de variabler som skapats i ditt Automation-konto. Ändra endast variabler som är fördefinierade med **Extern**. Om du ändrar variabler som föregås av **interna** orsaker kan oönskade effekter inte ändras.

|Variabel | Beskrivning|
|---------|------------|
|External_AutoStop_Condition | Den villkorliga operatorn som krävs för att konfigurera villkoret innan en avisering utlöses. Godtagbara värden är **GreaterThan,** **GreaterThanOrEqual,** **LessThan**och **LessThanOrEqual**.|
|External_AutoStop_Description | Aviseringen för att stoppa den virtuella datorn om processorprocenten överskrider tröskelvärdet.|
|External_AutoStop_MetricName | Namnet på det prestandamått som Azure Alert-regeln ska konfigureras för.|
|External_AutoStop_Threshold | Tröskelvärdet för Azure Alert-regeln som anges i variabeln _External_AutoStop_MetricName_. Procentvärden kan variera mellan 1 och 100.|
|External_AutoStop_TimeAggregationOperator | Tidsaggregeringsoperatorn, som används på den valda fönsterstorleken för att utvärdera villkoret. Godtagbara värden är **Medel,** **Minimum**, **Maximum**, **Total**och **Last**.|
|External_AutoStop_TimeWindow | Fönsterstorleken under vilken Azure analyserar valda mått för att utlösa en avisering. Den här parametern accepterar indata i tidsspannformat. Möjliga värden är från 5 minuter till 6 timmar.|
|External_EnableClassicVMs| Anger om klassiska virtuella datorer är inriktade på lösningen. Standardvärdet är Sant. Detta bör ställas in på False för CSP-prenumerationer. Klassiska virtuella datorer kräver ett [klassiskt run-as-konto](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Ange VM-namn som ska uteslutas, separera namn med hjälp av ett kommatecken utan blanksteg. Detta är begränsat till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i den här kommaavgränsade listan kan virtuella datorer som är inställda på att uteslutas startas eller stoppas av misstag.|
|External_Start_ResourceGroupNames | Anger en eller flera resursgrupper som avgränsar värden med hjälp av ett kommatecken som är avsett för startåtgärder.|
|External_Stop_ResourceGroupNames | Anger en eller flera resursgrupper som avgränsar värden med hjälp av ett kommatecken som är avsett för stoppåtgärder.|
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|
|Internal_AutoSnooze_WebhookUri | Anger Webhook URI som anropas för Scenariot För Automatisk topp.|
|Internal_AzureSubscriptionId | Anger Azure-prenumerations-ID.|
|Internal_ResourceGroupName | Anger resursgruppsnamnet för automationskontot.|

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

## <a name="azure-monitor-logs-records"></a>Azure Monitor loggar poster

Automatisering skapar två typer av poster på log analytics-arbetsytan: jobbloggar och jobbströmmar.

### <a name="job-logs"></a>Jobbloggar

|Egenskap | Beskrivning|
|----------|----------|
|Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
|Kategori | Klassificering av typ av data. För Automation är värdet JobLogs.|
|CorrelationId | GUID som är korrelations-ID för runbook-jobbet.|
|JobId | GUID som är ID för runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet Jobb.|
|resourceId | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
|ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
|ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|resultType | Status för runbookjobbet. Möjliga värden:<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>- Slutförd|
|resultDescription | Beskriver jobbstatusen för runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes|
|RunbookName | Anger namnet på runbooken.|
|SourceSystem | Anger källsystemet för data som skickats. För Automation är värdet OpsManager|
|StreamType | Anger händelsetypen. Möjliga värden:<br>- Verbose<br>- Utdata<br>- Fel<br>- Varning|
|SubscriptionId | Anger prenumerations-ID för jobbet.
|Tid | Datum och tid då runbook-jobbet körs.|

### <a name="job-streams"></a>Arbetsflöden

|Egenskap | Beskrivning|
|----------|----------|
|Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
|Kategori | Klassificering av typ av data. För Automation är värdet JobStreams.|
|JobId | GUID som är ID för runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet Jobb.|
|ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
|resourceId | Anger resurs-ID i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
|ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|resultType | Resultatet av runbook-jobbet då händelsen skapades. Ett möjligt värde är:<br>- Ärendeposter|
|resultDescription | Innehåller utdataströmmen från runbook.|
|RunbookName | Anger namnet på runbooken.|
|SourceSystem | Anger källsystemet för data som skickats. För Automation är värdet OpsManager.|
|StreamType | Typ av jobbström. Möjliga värden:<br>- Framsteg<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose|
|Tid | Datum och tid då runbook-jobbet körs.|

När du utför en loggsökning som returnerar kategoriposter för **JobLogs** eller **JobStreams**kan du välja vyn **JobLogs** eller **JobStreams,** som visar en uppsättning paneler som sammanfattar uppdateringarna som returneras av sökningen.

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på sökningar i loggen för jobbposter som har samlats in av den här lösningen.

|Söka i data | Beskrivning|
|----------|----------|
|Hitta jobb för runbook ScheduledStartStop_Parent som har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Hitta jobb för runbook SequencedStartStop_Parent som har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Visa lösningen

Om du vill komma åt lösningen navigerar du till ditt Automation-konto och väljer **Arbetsyta** under **RELATERADE RESURSER**. På loganalyssidan väljer du **Lösningar** under **ALLMÄNT**. På sidan **Lösningar** väljer du lösningen **Start-Stop-VM[workspace]** i listan.

Om du väljer lösningen visas lösningssidan **start-stop-vm[workspace].** Här kan du granska viktiga detaljer som **StartStopVM-panelen.** Precis som på arbetsytan Log Analytics visar den här panelen ett antal och en grafisk representation av runbook-jobben för den lösning som har startat och avslutats.

![Lösningssida för hantering av automatiseringsuppdatering](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Härifrån kan du utföra ytterligare analys av jobbposterna genom att klicka på donutpanelen. Instrumentpanelen för lösning visar jobbhistorik och fördefinierade loggsökningsfrågor. Växla till den avancerade portalen för logganalys för att söka baserat på dina sökfrågor.

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Om du vill ändra e-postmeddelanden när lösningen har distribuerats ändrar du åtgärdsgruppen som skapades under distributionen.  

> [!NOTE]
> Prenumerationer i Azure Government Cloud stöder inte e-postfunktionerna i den här lösningen.

I Azure-portalen navigerar du till Övervaka -> åtgärdsgrupper. Markera den åtgärdsgrupp med titeln **StartStop_VM_Notication**.

![Lösningssida för hantering av automatiseringsuppdatering](media/automation-solution-vm-management/azure-monitor.png)

Klicka på **Redigera information** under **Information**på sidan **StartStop_VM_Notification.** Då öppnas sidan **E-post/SMS/Push/Voice.** Uppdatera e-postadressen och klicka på **OK** för att spara ändringarna.

![Lösningssida för hantering av automatiseringsuppdatering](media/automation-solution-vm-management/change-email.png)

Alternativt kan du lägga till ytterligare åtgärder i åtgärdsgruppen, lära dig mer om åtgärdsgrupper, se [åtgärdsgrupper](../azure-monitor/platform/action-groups.md)

Följande är ett exempel på e-post som skickas när lösningen stänger av virtuella datorer.

![Lösningssida för hantering av automatiseringsuppdatering](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Lägga till/exkluderar virtuella datorer

Lösningen ger möjlighet att lägga till virtuella datorer som ska riktas av lösningen eller specifikt utesluta maskiner från lösningen.

### <a name="add-a-vm"></a>Lägga till en virtuell dator

Det finns ett par alternativ som du kan använda för att se till att en virtuell dator ingår i Start/Stop-lösningen när den körs.

* Var och en av de överordnade [runbooks](#runbooks) av lösningen har en **VMList** parameter. Du kan skicka en kommaavgränsad lista över VM-namn till den här parametern när du schemalägger lämplig överordnad runbook för din situation och dessa virtuella datorer inkluderas när lösningen körs.

* Om du vill markera flera virtuella datorer anger du **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** med de resursgruppsnamn som innehåller de virtuella datorer som du vill starta eller stoppa. Du kan också ange `*`det här värdet till , så att lösningen körs mot alla resursgrupper i prenumerationen.

### <a name="exclude-a-vm"></a>Undanta en virtuell dator

Om du vill utesluta en virtuell dator från lösningen kan du lägga till den i **variabeln External_ExcludeVMNames.** Den här variabeln är en kommaavgränsad lista över specifika virtuella datorer som ska uteslutas från Start/Stop-lösningen. Den här listan är begränsad till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i den här kommaavgränsade listan kan virtuella datorer som är inställda på att uteslutas startas eller stoppas av misstag.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Ändra start- och avstängningsscheman

Hantera start- och avstängningsscheman i den här lösningen följer samma steg som beskrivs i [Schemalägga en runbook i Azure Automation](automation-schedules.md). Det måste finnas ett separat schema för att starta och stoppa virtuella datorer.

Det stöds att konfigurera lösningen för att bara stoppa virtuella datorer vid en viss tidpunkt. I det här fallet skapar du bara ett **stoppschema** och ingen motsvarande **Start** schemalagd. Om du vill göra det måste du:

1. Se till att du har lagt till resursgrupperna för de virtuella datorerna att stänga av i **variabeln External_Stop_ResourceGroupNames.**
2. Skapa ditt eget schema för den tid du vill stänga av de virtuella datorerna.
3. Navigera till **ScheduledStartStop_Parent** runbook och klicka på **Schema**. På så sätt kan du välja det schema som du skapade i föregående steg.
4. Välj **Parametrar och kör inställningar** och ange åtgärden parametern till "Stop".
5. Spara ändringarna genom att klicka på **OK**.

## <a name="update-the-solution"></a>Uppdatera lösningen

Om du har distribuerat en tidigare version av den här lösningen måste du först ta bort den från ditt konto innan du distribuerar en uppdaterad version. Följ stegen för att [ta bort lösningen](#remove-the-solution) och följ sedan stegen ovan för att distribuera [lösningen](#deploy-the-solution).

## <a name="remove-the-solution"></a>Ta bort lösningen

Om du bestämmer dig för att du inte längre behöver använda lösningen kan du ta bort den från Automation-kontot. Om du tar bort lösningen tas runbooks bort. Scheman eller variabler som skapades när lösningen lades till tas inte bort. De tillgångar som du behöver ta bort manuellt om du inte använder dem med andra runbooks.

Så här tar du bort lösningen:

1. Välj **Länkad arbetsyta**under **Relaterade resurser**i ditt Automation-konto.
1. Välj **Gå till arbetsyta .**
1. Under **Allmänt**väljer du **Lösningar**. 
1. På sidan **Lösningar** väljer du lösningen **Start-Stop-VM[Workspace]**. Välj **Ta bort**på sidan **VMManagementSolution[Workspace]** på menyn .<br><br> ![Ta bort VM Mgmt-lösning](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Bekräfta att du vill ta bort lösningen i fönstret **Ta bort lösning.**
1. Medan informationen verifieras och lösningen tas bort kan du spåra dess förlopp under **Meddelanden** från menyn. Du returneras till sidan **Lösningar** när processen för att ta bort lösningen startar.

Arbetsytan Automation-konto och Logganalys tas inte bort som en del av den här processen. Om du inte vill behålla log analytics-arbetsytan måste du ta bort den manuellt. Detta kan åstadkommas från Azure-portalen:

1. Sök efter och välj **Log Analytics-arbetsytor i Azure-portalen**.
1. Välj arbetsytan på sidan **Logganalysarbetsytor.**
1. Välj **Ta bort** på menyn på sidan inställningar för arbetsytan.

Om du inte vill behålla Azure Automation-kontokomponenterna kan du ta bort var och en manuellt. En lista över runbooks, variabler och scheman som skapats av lösningen finns i [lösningskomponenterna](#solution-components).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar olika sökfrågor och granskar automation-jobbloggarna med Azure Monitor-loggar finns [i Loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md).
- Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
- Mer information om Azure Monitor-loggar och datainsamlingskällor finns [i Samla in Azure-lagringsdata i Azure Monitor-loggloggar översikt](../azure-monitor/platform/collect-azure-metrics-logs.md).
