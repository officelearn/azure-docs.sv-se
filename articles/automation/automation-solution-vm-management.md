---
title: Starta/stoppa virtuella datorer när de inte används lösning
description: Den här lösningen för hantering av virtuella datorer startar och stoppar dina Azure Resource Manager virtuella datorer enligt ett schema och proaktivt övervakar från Azure Monitor loggar.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 37fee7f96a27942a1295cb8c2315fedffc5bdefe
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030169"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Starta/stoppa virtuella datorer när de inte används lösning i Azure Automation

Starta/stoppa virtuella datorer när de inte användss lösningen startar och stoppar dina virtuella Azure-datorer enligt användardefinierade scheman, ger insikter via Azure Monitor loggar och skickar valfria e-postmeddelanden med hjälp av [Åtgärds grupper](../azure-monitor/platform/action-groups.md). Det stöder både Azure Resource Manager och klassiska virtuella datorer för de flesta scenarier. Om du vill använda den här lösningen med klassiska virtuella datorer behöver du ett klassiskt RunAs-konto som inte skapas som standard. Anvisningar om hur du skapar ett klassiskt kör som-konto finns i [klassiska kör som-konton](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Den Starta/stoppa virtuella datorer när de inte används lösningen har testats med Azure-modulerna som importeras till ditt Automation-konto när du distribuerar lösningen. Lösningen fungerar för närvarande inte med nyare versioner av Azure-modulen. Detta påverkar bara det Automation-konto som du använder för att köra Starta/stoppa virtuella datorer när de inte används-lösningen. Du kan fortfarande använda nyare versioner av Azure-modulen i andra Automation-konton, enligt beskrivningen i [så här uppdaterar du Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md)

Den här lösningen innehåller ett decentraliserat alternativ för låg kostnads automatisering för användare som vill optimera sina VM-kostnader. Med den här lösningen kan du:

- Schemalägg virtuella datorer för start och stopp.
- Schemalägg virtuella datorer att starta och stoppa i stigande ordning med hjälp av Azure-Taggar (stöds inte för klassiska virtuella datorer).
- Stoppa virtuella datorer för virtuella datorer baserat på låg CPU-användning.

Följande är begränsningar med den aktuella lösningen:

- Den här lösningen hanterar virtuella datorer i valfri region, men kan endast användas i samma prenumeration som ditt Azure Automation-konto.
- Den här lösningen är tillgänglig i Azure och AzureGov till alla regioner som stöder en Log Analytics arbets yta, ett Azure Automation konto och aviseringar. AzureGov-regioner stöder för närvarande inte e-postfunktioner.

> [!NOTE]
> Om du använder lösningen för klassiska virtuella datorer kommer alla virtuella datorer att bearbetas sekventiellt per moln tjänst. Virtuella datorer bearbetas fortfarande parallellt över olika moln tjänster. Om du har fler än 20 virtuella datorer per moln tjänst rekommenderar vi att du skapar flera scheman med den överordnade Runbook- **ScheduledStartStop_Parent** och anger 20 virtuella datorer per schema. I schema egenskaperna anger du som en kommaavgränsad lista, VM-namn i parametern **VMList** . Annars, om automatiserings jobbet för den här lösningen körs mer än tre timmar, tas det tillfälligt bort eller stoppas enligt den [verkliga delnings](automation-runbook-execution.md#fair-share) gränsen.
>
> Azure-prenumerationer för moln lösningar (Azure CSP) stöder endast Azure Resource Manager-modellen, icke-Azure Resource Manager-tjänster är inte tillgängliga i programmet. När starta/stoppa-lösningen körs kan du få fel eftersom det finns cmdlets för att hantera klassiska resurser. Läs mer om CSP i [tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services). Om du använder en CSP-prenumeration bör du ändra [**External_EnableClassicVMs**](#variables) variabeln till **falskt** efter distributionen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav

Runbooks för den här lösningen fungerar med ett [Kör som-konto i Azure](automation-create-runas-account.md). Kör som-kontot är den föredragna autentiseringsmetoden eftersom den använder certifikatautentisering i stället för ett lösen ord som kan gå ut eller ändras ofta.

Vi rekommenderar att du använder ett separat Automation-konto för lösningen starta/stoppa virtuell dator. Detta beror på att Azure module-versioner ofta uppgraderas och att deras parametrar kan ändras. Lösningen för att starta/stoppa virtuell dator uppgraderas inte på samma takt, så den kanske inte fungerar med nyare versioner av de cmdlets som används. Vi rekommenderar också att du testar module-uppdateringar i ett test Automation-konto innan du importerar dem i dina produktions Automation-konton.

### <a name="permissions-needed-to-deploy"></a>Behörigheter som krävs för att distribuera

Det finns vissa behörigheter som en användare måste ha för att distribuera en lösning för att starta/stoppa virtuella datorer under låg tid. De här behörigheterna skiljer sig om du använder ett befintligt Automation-konto och Log Analytics arbets ytan eller skapar nya under distributionen. Om du är deltagare i prenumerationen och en global administratör i din Azure Active Directory klient behöver du inte konfigurera följande behörigheter. Om du inte har dessa rättigheter eller behöver konfigurera en anpassad roll, se de behörigheter som krävs nedan.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Redan befintligt Automation-konto och Log Analytics arbets yta

Om du vill distribuera en lösning för att starta/stoppa virtuella datorer under låg tid till ett befintligt Automation-konto och Log Analytics arbets ytan, kräver användaren som distribuerar lösningen följande behörigheter för **resurs gruppen**. Mer information om roller finns i [anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md).

| Behörighet | Omfång|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Resursgrupp |
| Microsoft. Automation/automationAccounts/variabler/Write | Resursgrupp |
| Microsoft.Automation/automationAccounts/schedules/write | Resursgrupp |
| Microsoft. Automation/automationAccounts/Runbooks/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/Connections/Write | Resursgrupp |
| Microsoft. Automation/automationAccounts/certificates/Write | Resursgrupp |
| Microsoft.Automation/automationAccounts/modules/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/modules/read | Resursgrupp |
| Microsoft. Automation/automationAccounts/jobSchedules/Write | Resursgrupp |
| Microsoft.Automation/automationAccounts/jobs/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/jobs/read | Resursgrupp |
| Microsoft. OperationsManagement/lösningar/Skriv | Resursgrupp |
| Microsoft. OperationalInsights/arbets ytor/* | Resursgrupp |
| Microsoft. Insights/diagnosticSettings/Write | Resursgrupp |
| Microsoft. Insights/ActionGroups/Write | Resursgrupp |
| Microsoft. Insights/ActionGroups/Read | Resursgrupp |
| Microsoft.Resources/subscriptions/resourceGroups/read | Resursgrupp |
| Microsoft. Resources/Deployments/* | Resursgrupp |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nytt Automation-konto och en ny Log Analytics arbets yta

Om du vill distribuera en lösning för att starta/stoppa virtuella datorer under låg tid till ett nytt Automation-konto och Log Analytics arbets ytan, behöver användaren som distribuerar lösningen de behörigheter som definierats i föregående avsnitt samt följande behörigheter:

- Medadministratör för prenumeration – du behöver bara skapa det klassiska kör som-kontot om du ska hantera klassiska virtuella datorer. [Klassiska runas-konton](automation-create-standalone-account.md#classic-run-as-accounts) skapas inte längre som standard.
- En medlem i rollen som [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **program utvecklare** . Mer information om hur du konfigurerar kör som-konton finns i [behörighet att konfigurera kör som-konton](manage-runas-account.md#permissions).
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

## <a name="deploy-the-solution"></a>Distribuera lösningen

Utför följande steg för att lägga till Starta/stoppa virtuella datorer när de inte används-lösningen till ditt Automation-konto och konfigurera sedan variablerna för att anpassa lösningen.

1. Från ett Automation-konto väljer du **Starta/stoppa virtuell dator** under **relaterade resurser**. Härifrån kan du klicka på **Läs mer om och aktivera lösningen**. Om du redan har distribuerat en lösning för att starta/stoppa virtuell dator kan du välja den genom att klicka på **Hantera lösningen** och söka efter den i listan.

   ![Aktivera från Automation-konto](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Du kan också skapa den från var som helst i Azure Portal genom att klicka på **skapa en resurs**. På sidan Marketplace skriver du ett nyckelord, till exempel **Starta** eller **Starta/stoppa**. När du börjar skriva filtreras listan baserat på det du skriver. Alternativt kan du skriva in ett eller flera nyckelord från det fullständiga namnet på lösningen och trycka på RETUR. Välj **Starta/stoppa virtuella datorer när de inte används** från Sök resultaten.

2. På sidan **Starta/stoppa virtuella datorer när de inte används** för den valda lösningen granskar du sammanfattnings informationen och klickar sedan på **skapa**.

   ![Azure portal](media/automation-solution-vm-management/azure-portal-01.png)

3. Sidan **Lägg till lösning** visas. Du uppmanas att konfigurera lösningen innan du kan importera den till din Automation-prenumeration.

   ![Sidan för att lägga till hantering av virtuella datorer](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. På sidan **Lägg till lösning** väljer du **arbets yta**. Välj en Log Analytics arbets yta som är länkad till samma Azure-prenumeration som Automation-kontot finns i. Om du inte har en arbets yta väljer du **Skapa ny arbets yta**. Utför följande steg på sidan **Log Analytics arbets yta** :
   - Ange ett namn för den nya **Log Analytics-arbetsytan**, till exempel "ContosoLAWorkspace".
   - Välj en **prenumeration** som du vill länka till genom att välja i den nedrullningsbara listan, om det valda standardvärdet inte är lämpligt.
   - För **resurs grupp**kan du skapa en ny resurs grupp eller välja en befintlig.
   - Välj en **Plats**.
   - Välj en **Prisnivå**. Välj alternativet **per GB (fristående)** . Azure Monitors loggar har uppdaterat [priser](https://azure.microsoft.com/pricing/details/log-analytics/) och nivån per GB är det enda alternativet.

   > [!NOTE]
   > När du aktiverar lösningar går det endast att länka en Log Analytics-arbetsyta och ett Automation-konto i vissa regioner.
   >
   > En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

5. När du har angett nödvändig information på sidan **Log Analytics arbets yta** klickar du på **skapa**. Du kan följa förloppet under **meddelanden** på menyn, som återgår till sidan **Lägg till lösning** när du är färdig.
6. På sidan **Lägg till lösning** väljer du **Automation-konto**. Om du skapar en ny Log Analytics arbets yta kan du skapa ett nytt Automation-konto som ska associeras med det, eller välja ett befintligt Automation-konto som inte redan är kopplat till en Log Analytics arbets yta. Välj ett befintligt Automation-konto eller klicka på **skapa ett Automation-konto**och ange följande information på sidan **Lägg till Automation-konto** :
   - I fältet **namn** anger du namnet på Automation-kontot.

     Alla andra alternativ fylls i automatiskt baserat på den Log Analytics arbets ytan som valts. Dessa alternativ kan inte ändras. Ett Azure kör som-konto är standardmetoden för autentisering för runbooks som ingår i den här lösningen. När du klickar på **OK**verifieras konfigurations alternativen och automation-kontot skapas. Du kan spåra förloppet under **Meddelanden** på menyn.

7. På sidan **Lägg till lösning** väljer du till sist **konfiguration**. Sidan **parametrar** visas.

   ![Parameter sidan för lösningen](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Här uppmanas du att:
   - Ange **mål ResourceGroup namn**. Dessa värden är resurs grupp namn som innehåller virtuella datorer som ska hanteras av den här lösningen. Du kan ange mer än ett namn och avgränsa dem med ett kommatecken (värden är inte Skift läges känsliga). Användning av jokertecken stöds om du vill inkludera virtuella datorer i alla resursgrupper i prenumerationen. Det här värdet lagras i **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** variabler.
   - Ange **undantags listan för den virtuella datorn (sträng)** . Det här värdet är namnet på en eller flera virtuella datorer från mål resurs gruppen. Du kan ange mer än ett namn och avgränsa dem med ett kommatecken (värden är inte Skift läges känsliga). Användning av jokertecken stöds. Det här värdet lagras i variabeln **External_ExcludeVMNames** .
   - Välj ett **schema**. Välj datum och tid för ditt schema. Ett dagligt schema som pågår kommer att skapas från och med den tid som du har valt. Det går inte att välja en annan region. Information om hur du konfigurerar schemat till din speciella tidszon efter att du har konfigurerat lösningen finns i [ändra schemat för start och avstängning](#modify-the-startup-and-shutdown-schedules).
   - Acceptera standardvärdet **Ja** och ange en giltig e-postadress för att ta emot **e-postaviseringar** från en åtgärds grupp. Om du väljer **Nej** men väljer ett senare datum som du vill ta emot e-postaviseringar kan du uppdatera den [Åtgärds grupp](../azure-monitor/platform/action-groups.md) som skapas med giltiga e-postadresser, avgränsade med kommatecken. Du måste också aktivera följande aviserings regler:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Standardvärdet för **mål ResourceGroup namn** är ett **&ast;** . Detta riktar sig till alla virtuella datorer i en prenumeration. Om du inte vill att lösningen ska rikta alla virtuella datorer i din prenumeration måste det här värdet uppdateras till en lista över resurs grupp namn innan du aktiverar scheman.

8. När du har konfigurerat de första inställningarna som krävs för lösningen klickar du på **OK** för att stänga sidan **parametrar** och väljer **skapa**. När alla inställningar har verifierats distribueras lösningen till din prenumeration. Den här processen kan ta flera sekunder att slutföra och du kan följa förloppet under **meddelanden** på menyn.

> [!NOTE]
> Om du har en prenumeration på Azure Cloud Solution Provider (Azure CSP) efter att distributionen har slutförts går du till **variabler** under **delade resurser** i ditt Automation-konto och anger [**External_EnableClassicVMs**](#variables) variabeln till **falskt**. Detta stoppar lösningen från att leta efter klassiska VM-resurser.

## <a name="scenarios"></a>Scenarier

Lösningen innehåller tre olika scenarier. De här scenarierna är:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenario 1: Starta/stoppa virtuella datorer enligt ett schema

Det här scenariot är standard konfigurationen när du först distribuerar lösningen. Du kan till exempel konfigurera det för att stoppa alla virtuella datorer i en prenumeration när du lämnar jobbet på kvällen och starta dem på morgon när du är tillbaka på kontoret. När du konfigurerar scheman för **schemalagda-StartVM** och **schemalagda StopVM** under distributionen, startar och slutar riktade virtuella datorer. Det finns stöd för att konfigurera den här lösningen till att bara stoppa virtuella datorer, se [ändra scheman för start och avstängning](#modify-the-startup-and-shutdown-schedules) för att lära dig hur du konfigurerar ett anpassat schema.

> [!NOTE]
> Tids zonen är din aktuella tidszon när du konfigurerar parametern för schema tid. Den lagras dock i UTC-format i Azure Automation. Du behöver inte göra någon tids zons konvertering eftersom detta hanteras under distributionen.

Du styr vilka virtuella datorer som omfattas genom att konfigurera följande variabler: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**och **External_ExcludeVMNames**.

Du kan aktivera antingen åtgärden för en prenumeration och en resurs grupp, eller rikta in en speciell lista över virtuella datorer, men inte båda.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta in dig på Start-och stopp åtgärder mot en prenumeration och resurs grupp

1. Konfigurera de **External_Stop_ResourceGroupNames** och **External_ExcludeVMNames** variabler för att ange de virtuella mål datorerna.
2. Aktivera och uppdatera **schemalagda StartVM** -och **schemalagda StopVM-** scheman.
3. Kör **ScheduledStartStop_Parent** RUNBOOK med åtgärds parametern inställd på **Start** och parametern WHATIF har angetts till **True** för att förhandsgranska ändringarna.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Ange start-och stopp åtgärd per VM-lista

1. Kör **ScheduledStartStop_Parent** RUNBOOK med åtgärds parametern inställd på att **Starta**, Lägg till en kommaavgränsad lista med virtuella datorer i parametern *VMLIST* och ange sedan parametern WHATIF till **True**. Förhandsgranska ändringarna.
1. Konfigurera parametern **External_ExcludeVMNames** med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3).
1. I det här scenariot respekteras inte **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** variabler. I det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [Schemalägga en Runbook i Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Värdet för **mål ResourceGroup-namn** lagras som värdet för både **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames**. För ytterligare granularitet kan du ändra var och en av dessa variabler för att rikta in sig på olika resurs grupper. Använd **External_Start_ResourceGroupNames**för att starta åtgärd, och Använd **External_Stop_ResourceGroupNames**för att stoppa åtgärden. Virtuella datorer läggs automatiskt till i Start-och stopp scheman.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenario 2: Starta/stoppa virtuella datorer i följd med hjälp av Taggar

I en miljö som innehåller två eller flera komponenter på flera virtuella datorer som har stöd för en distribuerad arbets belastning, är det viktigt med stöd för den sekvens i vilken komponenter startas och stoppas i ordning. Du kan utföra det här scenariot genom att utföra följande steg:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta in dig på Start-och stopp åtgärder mot en prenumeration och resurs grupp

1. Lägg till en **sequencestart** och en **sequencestop** -tagg med ett positivt heltals värde för virtuella datorer som är riktade till **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** variabler. Start-och stopp åtgärderna utförs i stigande ordning. Information om hur du taggar en virtuell dator finns i [tagga en virtuell Windows-dator i Azure](../virtual-machines/windows/tag.md) och [tagga en virtuell Linux-dator i Azure](../virtual-machines/linux/tag.md).
1. Ändra schemana **Sequenced-StartVM** och **Sequenced-StopVM** till det datum och den tid som uppfyller dina krav och aktivera schemat.
1. Kör **SequencedStartStop_Parent** RUNBOOK med åtgärds parametern inställd på **Start** och parametern WHATIF har angetts till **True** för att förhandsgranska ändringarna.
1. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar de virtuella datorerna i produktionen. När du är klar kan du köra runbooken manuellt med parametern inställt på **falskt**eller låta Automation-schemat **sekvenserat-StartVM** och **sekvenserat-StopVM** köras automatiskt efter ditt schema.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Ange start-och stopp åtgärd per VM-lista

1. Lägg till en **sequencestart** och en **sequencestop** -tagg med ett positivt heltals värde för de virtuella datorer som du planerar att lägga till i parametern **VMList** .
1. Kör **SequencedStartStop_Parent** RUNBOOK med åtgärds parametern inställd på att **Starta**, Lägg till en kommaavgränsad lista med virtuella datorer i parametern *VMLIST* och ange sedan parametern WHATIF till **True**. Förhandsgranska ändringarna.
1. Konfigurera parametern **External_ExcludeVMNames** med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3).
1. I det här scenariot respekteras inte **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** variabler. I det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [Schemalägga en Runbook i Azure Automation](../automation/automation-schedules.md).
1. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar de virtuella datorerna i produktionen. När du är klar kan du köra övervakning-och-diagnostik/Monitor-groupsrunbook med parametern inställt på **falskt**eller låta Automation **-schemat sekvenserat-StartVM** och **Sequenced-StopVM** köra automatiskt efter ditt schema.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scenario 3: Starta/stoppa automatiskt baserat på processor användning

Den här lösningen kan hjälpa till att hantera kostnaden för att köra virtuella datorer i din prenumeration genom att utvärdera virtuella Azure-datorer som inte används under perioder med låg belastning, till exempel efter timmar, och sedan stänga ned dem automatiskt om processor belastningen är mindre än x%.

Som standard är lösningen förkonfigurerad för att utvärdera procent andelen av processor mått för att se om den genomsnittliga användningen är 5 procent eller mindre. Det här scenariot styrs av följande variabler och kan ändras om standardvärdena inte uppfyller dina krav:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Du kan aktivera antingen åtgärden för en prenumeration och en resurs grupp, eller rikta in en speciell lista över virtuella datorer, men inte båda.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Rikta in stopp åtgärden mot en prenumeration och resurs grupp

1. Konfigurera de **External_Stop_ResourceGroupNames** och **External_ExcludeVMNames** variabler för att ange de virtuella mål datorerna.
1. Aktivera och uppdatera **Schedule_AutoStop_CreateAlert_Parent** schemat.
1. Kör **AutoStop_CreateAlert_Parent** RUNBOOK med åtgärds parametern inställd på **Start** och parametern WHATIF har angetts till **True** för att förhandsgranska ändringarna.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Ange start-och stopp åtgärd per VM-lista

1. Kör **AutoStop_CreateAlert_Parent** RUNBOOK med åtgärds parametern inställd på att **Starta**, Lägg till en kommaavgränsad lista med virtuella datorer i parametern *VMLIST* och ange sedan parametern WHATIF till **True**. Förhandsgranska ändringarna.
1. Konfigurera parametern **External_ExcludeVMNames** med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3).
1. I det här scenariot respekteras inte **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** variabler. I det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [Schemalägga en Runbook i Azure Automation](../automation/automation-schedules.md).

Nu när du har ett schema för att stoppa virtuella datorer baserat på processor användning måste du aktivera något av följande scheman för att starta dem.

- Mål start åtgärd efter prenumeration och resurs grupp. Se stegen i [Scenario 1](#scenario-1-startstop-vms-on-a-schedule) för testning och aktivering av **schemalagda StartVM-** scheman.
- Mål start åtgärd efter prenumeration, resurs grupp och tagg. Se stegen i [Scenario 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) för att testa och aktivera **sekvenserade StartVM-** scheman.

## <a name="solution-components"></a>Lösningskomponenter

Den här lösningen innehåller förkonfigurerade Runbooks, scheman och integrering med Azure Monitor loggar så att du kan skräddarsy Start och avstängning av dina virtuella datorer så att de passar dina affärs behov.

### <a name="runbooks"></a>Runbooks

I följande tabell visas de Runbooks som distribueras till ditt Automation-konto med den här lösningen. Gör inte ändringar i Runbook-koden. Skriv i stället din egen Runbook för nya funktioner.

> [!IMPORTANT]
> Kör inte någon Runbook med "underordnad" till namnet.

Alla överordnade Runbooks innehåller parametern _whatIf_ . När värdet är **True**, stöder _whatIf_ information om det exakta beteende som Runbook tar när den körs utan parametern _whatIf_ och kontrollerar att rätt virtuella datorer är riktade. En Runbook utför bara sina definierade åtgärder när _whatIf_ -parametern har angetts till **false**.

|Runbook | Parametrar | Beskrivning|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Anropas från den överordnade runbooken. Denna Runbook skapar aviseringar per resurs för scenariot för automatiskt stopp.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true eller false  | Skapar eller uppdaterar Azures aviserings regler på virtuella datorer i mål prenumerationen eller resurs grupperna. <br> VMList: kommaavgränsad lista över virtuella datorer. Till exempel _VM1, VM2, vm3_.<br> *WhatIf* validerar Runbook-logiken utan att köra.|
|AutoStop_Disable | ingen | Inaktiverar autostopp-aviseringar och standard schema.|
|AutoStop_StopVM_Child | WebHookData | Anropas från den överordnade runbooken. Aviserings regler anropar denna Runbook för att stoppa den virtuella datorn.|
|Bootstrap_Main | ingen | Används en gång för att ställa in bootstrap-konfigurationer som webhookURI, som normalt inte är tillgängliga från Azure Resource Manager. Denna Runbook tas bort automatiskt vid lyckad distribution.|
|ScheduledStartStop_Child | VMName <br> Åtgärd: starta eller stoppa <br> ResourceGroupName | Anropas från den överordnade runbooken. Kör en start-eller stopp åtgärd för det schemalagda steget.|
|ScheduledStartStop_Parent | Åtgärd: starta eller stoppa <br>VMList <br> WhatIf: true eller false | Den här inställningen påverkar alla virtuella datorer i prenumerationen. Redigera **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** så att de bara körs på dessa mål resurs grupper. Du kan också undanta vissa virtuella datorer genom att uppdatera variabeln **External_ExcludeVMNames** .<br> VMList: kommaavgränsad lista över virtuella datorer. Till exempel _VM1, VM2, vm3_.<br> _WhatIf_ validerar Runbook-logiken utan att köra.|
|SequencedStartStop_Parent | Åtgärd: starta eller stoppa <br> WhatIf: true eller false<br>VMList| Skapa Taggar med namnet **sequencestart** och **sequencestop** på varje virtuell dator för vilken du vill starta/stoppa aktivitet. Dessa taggnamn är Skift läges känsliga. Taggens värde måste vara ett positivt heltal (1, 2, 3) som motsvarar den ordning som du vill starta eller stoppa. <br> VMList: kommaavgränsad lista över virtuella datorer. Till exempel _VM1, VM2, vm3_. <br> _WhatIf_ validerar Runbook-logiken utan att köra. <br> **Obs!** virtuella datorer måste finnas i resurs grupper som definieras som External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames och External_ExcludeVMNames i Azure Automation variabler. De måste ha lämpliga taggar för att åtgärder ska börja gälla.|

### <a name="variables"></a>Variabler

I följande tabell visas variablerna som skapas i ditt Automation-konto. Ändra endast variabler som föregås av **external**. Om du ändrar variabler som föregås av **interna** orsaker får oönskade effekter.

|Variabel | Beskrivning|
|---------|------------|
|External_AutoStop_Condition | Den villkorliga operator som krävs för att konfigurera villkoret innan en avisering utlöses. Godkända värden är **GreaterThan**, **GreaterThanOrEqual**, **LessThan**och **LessThanOrEqual**.|
|External_AutoStop_Description | Aviseringen för att stoppa den virtuella datorn om PROCESSORns procents ATS överstiger tröskelvärdet.|
|External_AutoStop_MetricName | Namnet på det prestanda mått som Azure-aviserings regeln ska konfigureras för.|
|External_AutoStop_Threshold | Tröskelvärdet för den Azure Alert-regel som anges i variabeln _External_AutoStop_MetricName_. Procent värden kan vara mellan 1 och 100.|
|External_AutoStop_TimeAggregationOperator | Tids mängds operatorn, som används för den valda fönster storleken för att utvärdera villkoret. Godkända värden är **Average**, **minimum**, **maximum**, **Total**och **Last**.|
|External_AutoStop_TimeWindow | Fönster storlek då Azure analyserar valda mått för att utlösa en avisering. Den här parametern accepterar inmatade i TimeSpan-format. Möjliga värden är mellan 5 och 6 timmar.|
|External_EnableClassicVMs| Anger om de klassiska virtuella datorerna är riktade mot lösningen. Standardvärdet är True. Detta ska vara inställt på falskt för CSP-prenumerationer. Klassiska virtuella datorer kräver ett [klassiskt kör som-konto](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Ange namn på virtuella datorer som ska uteslutas, separera namn genom att använda ett kommatecken utan blank steg. Detta är begränsat till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i den här kommaseparerade listan kan virtuella datorer som är inställda på att undantas oavsiktligt startas eller stoppas.|
|External_Start_ResourceGroupNames | Anger en eller flera resurs grupper och åtskiljer värden med hjälp av ett kommatecken som är mål för start åtgärder.|
|External_Stop_ResourceGroupNames | Anger en eller flera resurs grupper, åtskilja värden med ett kommatecken för att stoppa åtgärder.|
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|
|Internal_AutoSnooze_WebhookUri | Anger webhook-URI: n som anropas för scenariot för Autostopp.|
|Internal_AzureSubscriptionId | Anger ID för Azure-prenumerationen.|
|Internal_ResourceGroupName | Anger namnet på resurs gruppen för Automation-kontot.|

I alla scenarier är variablerna **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**och **External_ExcludeVMNames** nödvändiga för virtuella datorer, med undantag för att tillhandahålla en kommaavgränsad lista över virtuella datorer för **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**och **ScheduledStartStop_Parent** Runbooks. Det innebär att de virtuella datorerna måste finnas i mål resurs grupper för att start-och stopp åtgärder ska kunna utföras. Logiken fungerar ungefär som i Azure policy, i att du kan rikta in dig på prenumerationen eller resurs gruppen och ha åtgärder som ärvts av nyskapade virtuella datorer. Den här metoden gör att du inte behöver ha ett separat schema för varje virtuell dator och hantera startar och stoppas i stor skala.

### <a name="schedules"></a>Scheman

I följande tabell visas alla standard scheman som skapats i ditt Automation-konto. Du kan ändra dem eller skapa egna anpassade scheman. Som standard inaktive ras alla scheman förutom för **Scheduled_StartVM** och **Scheduled_StopVM**.

Du bör inte aktivera alla scheman eftersom det kan skapa överlappande schema åtgärder. Det är bäst att avgöra vilka optimeringar du vill utföra och ändra i enlighet med detta. I exempel scenarierna i översikts avsnittet finns ytterligare förklaring.

|Schema namn | Frequency | Beskrivning|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Var 8:e timme | Kör AutoStop_CreateAlert_Parent Runbook var 8: e timme, vilket i sin tur stoppar de VM-baserade värdena i External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames och External_ExcludeVMNames i Azure Automation variabler. Alternativt kan du ange en kommaavgränsad lista över virtuella datorer med hjälp av parametern VMList.|
|Scheduled_StopVM | Användardefinierad, daglig | Kör Scheduled_Parent Runbook med en parameter för _Stop_ varje dag vid den angivna tiden. Stoppar automatiskt alla virtuella datorer som uppfyller de regler som definieras av till gångs variabler. Aktivera det relaterade schemat, **schemalagda StartVM**.|
|Scheduled_StartVM | Användardefinierad, daglig | Kör Scheduled_Parent Runbook med en parameter för _Start_ varje dag vid den angivna tiden. Startar automatiskt alla virtuella datorer som uppfyller de regler som definierats av lämpliga variabler. Aktivera det relaterade schemat, **schemalagda StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), varje fredag | Kör Sequenced_Parent Runbook med en parameter för _stopp_ varje fredag vid den angivna tiden. I tur och ordning stoppas alla virtuella datorer med en tagg **SequenceStop** som definieras av lämpliga variabler. Mer information om tagg värden och till gångs variabler finns i avsnittet Runbooks. Aktivera relaterat schema, **sekvenserat-StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), varje måndag | Kör Sequenced_Parent Runbook med en parameter för _Start_ varje måndag vid den angivna tiden. Sekventiellt (fallande) startar alla virtuella datorer med en tagg för **SequenceStart** som definieras av lämpliga variabler. Mer information om tagg värden och till gångs variabler finns i avsnittet Runbooks. Aktivera relaterat schema, **sekvenserat-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Azure Monitor loggar poster

Automation skapar två typer av poster i arbets ytan Log Analytics: jobb loggar och jobb strömmar.

### <a name="job-logs"></a>Jobbloggar

|Egenskap | Beskrivning|
|----------|----------|
|Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
|Kategori | Klassificering av typ av data. För Automation är värdet JobLogs.|
|CorrelationId | GUID som är korrelations-ID för Runbook-jobbet.|
|JobId | GUID som är ID för Runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet jobb.|
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
|JobId | GUID som är ID för Runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet jobb.|
|ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
|resourceId | Anger resurs-ID i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
|ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|resultType | Resultatet av runbook-jobbet då händelsen skapades. Ett möjligt värde är:<br>- Ärendeposter|
|resultDescription | Innehåller utdataströmmen från runbook.|
|RunbookName | Anger namnet på runbooken.|
|SourceSystem | Anger källsystemet för data som skickats. För Automation är värdet OpsManager.|
|StreamType | Typ av jobbström. Möjliga värden:<br>– Förlopp<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose|
|Tid | Datum och tid då runbook-jobbet körs.|

När du utför en loggs ökning som returnerar kategori poster för **JobLogs** eller **JobStreams**kan du välja vyn **JobLogs** eller **JobStreams** , som visar en uppsättning paneler som sammanfattar de uppdateringar som returneras av sökningen.

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på sökningar i loggen för jobbposter som har samlats in av den här lösningen.

|Söka i data | Beskrivning|
|----------|----------|
|Hitta jobb för Runbook ScheduledStartStop_Parent som har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Hitta jobb för Runbook SequencedStartStop_Parent som har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Visa lösningen

För att få åtkomst till lösningen, navigera till ditt Automation-konto, Välj **arbets yta** under **relaterade resurser**. På sidan Log Analytics väljer du **lösningar** under **Allmänt**. På sidan **lösningar** väljer du lösningen **Start-Stop-VM [arbets yta]** i listan.

Om du väljer lösningen visas lösnings sidan **Starta-Stop-VM [Workspace]** . Här kan du granska viktig information som **StartStopVM** -panelen. Precis som i arbets ytan Log Analytics visar den här panelen ett antal och en grafisk representation av Runbook-jobben för lösningen som har startat och har slutförts.

![Sidan Automation Uppdateringshantering-lösning](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Härifrån kan du utföra ytterligare analyser av jobb posterna genom att klicka på Ring panelen. Instrument panelen för lösningen visar jobb historik och fördefinierade logg Sök frågor. Växla till logganalys-portalen i Log Analytics om du vill söka utifrån dina Sök frågor.

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Ändra åtgärds gruppen som skapades under distributionen om du vill ändra e-postaviseringar när lösningen har distribuerats.  

> [!NOTE]
> Prenumerationer i Azure Government molnet har inte stöd för den här lösningens e-postfunktioner.

I Azure Portal går du till övervaka-> åtgärds grupper. Välj åtgärds gruppen **StartStop_VM_Notication**.

![Sidan Automation Uppdateringshantering-lösning](media/automation-solution-vm-management/azure-monitor.png)

På sidan **StartStop_VM_Notification** klickar du på **Redigera information** under **information**. Då öppnas sidan **e-post/SMS/push/Voice** . Uppdatera e-postadressen och spara ändringarna genom att klicka på **OK** .

![Sidan Automation Uppdateringshantering-lösning](media/automation-solution-vm-management/change-email.png)

Alternativt kan du lägga till ytterligare åtgärder i åtgärds gruppen och läsa mer om åtgärds grupper i [Åtgärds grupper](../azure-monitor/platform/action-groups.md)

Följande är ett exempel på ett e-postmeddelande som skickas när lösningen stänger av virtuella datorer.

![Sidan Automation Uppdateringshantering-lösning](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Lägg till/exkludera virtuella datorer

Lösningen ger möjlighet att lägga till virtuella datorer som ska omfattas av lösningen eller undanta datorer från lösningen.

### <a name="add-a-vm"></a>Lägg till en virtuell dator

Det finns ett par alternativ som du kan använda för att se till att en virtuell dator ingår i starta/stoppa-lösningen när den körs.

* Varje överordnad [Runbooks](#runbooks) i lösningen har en **VMList** -parameter. Du kan skicka en kommaavgränsad lista över namn på virtuella datorer till den här parametern när du schemalägger lämplig överordnad Runbook för din situation och de virtuella datorerna kommer att inkluderas när lösningen körs.

* Om du vill välja flera virtuella datorer anger du **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** med de resurs grupp namn som innehåller de virtuella datorer som du vill starta eller stoppa. Du kan också ange det här värdet till `*`om du vill att lösningen ska köras mot alla resurs grupper i prenumerationen.

### <a name="exclude-a-vm"></a>Undanta en virtuell dator

Om du vill utesluta en virtuell dator från lösningen kan du lägga till den i **External_ExcludeVMNames** variabeln. Den här variabeln är en kommaavgränsad lista med vissa virtuella datorer som ska undantas från start-/stopp lösningen. Den här listan är begränsad till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i den här kommaseparerade listan kan virtuella datorer som är inställda på att undantas oavsiktligt startas eller stoppas.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Ändra scheman för start och avstängning

Hantering av start-och avstängnings scheman i den här lösningen följer samma steg som beskrivs i [schemaläggning av en Runbook i Azure Automation](automation-schedules.md). Det måste finnas ett separat schema för att starta och stoppa virtuella datorer.

Det finns stöd för att konfigurera lösningen till att bara stoppa virtuella datorer vid en viss tid. I det här scenariot skapar du bara ett **stopp** schema och ingen motsvarande **Start** schemalagd. Om du vill göra det måste du:

1. Se till att du har lagt till resurs grupperna för de virtuella datorerna som ska stängas av i **External_Stop_ResourceGroupNames** -variabeln.
2. Skapa ett eget schema för den tid som du vill stänga av de virtuella datorerna.
3. Navigera till **ScheduledStartStop_Parent** Runbook och klicka på **Schemalägg**. På så sätt kan du välja det schema som du skapade i föregående steg.
4. Välj **parametrar och kör inställningar** och ange åtgärds parametern till "stoppa".
5. Spara ändringarna genom att klicka på **OK**.

## <a name="update-the-solution"></a>Uppdatera lösningen

Om du har distribuerat en tidigare version av den här lösningen måste du först ta bort den från ditt konto innan du distribuerar en uppdaterad version. Följ stegen för att [ta bort lösningen](#remove-the-solution) och följ sedan stegen ovan för att [distribuera lösningen](#deploy-the-solution).

## <a name="remove-the-solution"></a>Ta bort lösningen

Om du vill att du inte längre behöver använda lösningen kan du ta bort den från Automation-kontot. Att ta bort lösningen tar bara bort Runbooks. De scheman eller variabler som skapades när lösningen lades till tas inte bort. De till gångar du behöver ta bort manuellt om du inte använder dem med andra Runbooks.

Utför följande steg för att ta bort lösningen:

1. Från ditt Automation-konto, under **relaterade resurser**, väljer du **länkad arbets yta**.
1. Välj **gå till arbets yta**.
1. Under **Allmänt**väljer du **lösningar**. 
1. På sidan **lösningar** väljer du lösningen **Start-Stop-VM [arbetsyte]** . På sidan **VMManagementSolution [Workspace]** väljer du **ta bort**på menyn.<br><br> ![ta bort lösningen för VM-hantering](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. I fönstret **ta bort lösning** bekräftar du att du vill ta bort lösningen.
1. Medan informationen verifieras och lösningen tas bort, kan du följa förloppet under **meddelanden** på menyn. Du kommer tillbaka till **lösnings** sidan när du har tagit bort lösningen.

Automation-kontot och Log Analytics-arbetsytan tas inte bort som en del av den här processen. Om du inte vill behålla arbets ytan Log Analytics måste du ta bort den manuellt. Detta kan åstadkommas från Azure Portal:

1. I Azure Portal söker du efter och väljer **Log Analytics arbets ytor**.
1. På sidan **Log Analytics arbets ytor** väljer du arbets ytan.
1. Välj **ta bort** på menyn på sidan Inställningar för arbets yta.

Om du inte vill behålla Azure Automation konto komponenter kan du ta bort dem manuellt. En lista över Runbooks, variabler och scheman som skapats av lösningen finns i [lösnings komponenterna](#solution-components).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar olika Sök frågor och granskar automatiserings jobb loggar med Azure Monitor loggar finns i [loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md).
- Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
- Mer information om Azure Monitor loggar och data insamlings källor finns i [samla in Azure Storage-data i Azure Monitor loggar översikt](../azure-monitor/platform/collect-azure-metrics-logs.md).
