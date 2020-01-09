---
title: Rollbaserad åtkomstkontroll i Azure Automation
description: Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Den här artikeln beskriver hur du konfigurerar RBAC i Azure Automation.
keywords: automation rbac, rollbaserad åtkomstkontroll, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: 122e1f69e952acc00aba3cad2d75cb87b8fd08ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421595"
---
# <a name="role-based-access-control-in-azure-automation"></a>Rollbaserad åtkomstkontroll i Azure Automation

Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Med [RBAC](../role-based-access-control/overview.md)kan du åtskilja uppgifter i ditt team och endast bevilja den mängd åtkomst till användare, grupper och program som de behöver för att utföra sina jobb. Rollbaserad åtkomst kan beviljas till användare som använder Azure Portal, Azure-kommandoradsverktygen eller Azure Management-API:er.

## <a name="roles-in-automation-accounts"></a>Roller i Automation-konton

I Azure Automation beviljas åtkomst genom att lämplig RBAC-roll tilldelas till användare, grupper och program i Automation-kontoomfånget. Följande är de inbyggda roller som stöds av ett Automation-konto:

| **Roll** | **Beskrivning** |
|:--- |:--- |
| Ägare |Ägar rollen ger till gång till alla resurser och åtgärder i ett Automation-konto, inklusive att ge åtkomst till andra användare, grupper och program för att hantera Automation-kontot. |
| Deltagare |Med deltagarrollen kan du hantera allt, men du kan inte ändra andra användares åtkomstbehörighet till ett Automation-konto. |
| Läsare |Med läsarrollen kan du visa alla resurser i ett Automation-konto men inte göra några ändringar. |
| Automation-operatör |Med rollen som Automation-operatör kan du Visa Runbook-namn och egenskaper och skapa och hantera jobb för alla Runbooks i ett Automation-konto. Den här rollen är användbar om du vill skydda dina Automation-kontoresurser, t.ex. autentiseringstillgångar och runbooks, så att de inte kan visas eller ändras men fortfarande vill att medlemmar i din organisation ska kunna köra dessa runbooks. |
|Automatiserings jobb operatör|Med rollen automatiserings jobb operatör kan du skapa och hantera jobb för alla Runbooks i ett Automation-konto.|
|Automation Runbook-operator|Med rollen Automation Runbook-operatör kan du Visa namn och egenskaper för en Runbook.|
| Log Analytics Contributor | Med rollen Log Analytics Contributor kan du läsa alla övervaknings data och redigera övervaknings inställningar. Genom att redigera övervaknings inställningarna kan du lägga till VM-tillägget till virtuella datorer, läsa lagrings konto nycklar för att kunna konfigurera insamling av loggar från Azure Storage, skapa och konfigurera Automation-konton, lägga till lösningar och konfigurera Azure Diagnostics på alla Azure-resurser.|
| Log Analytics Reader | Med rollen Log Analytics läsare kan du Visa och söka i alla övervaknings data samt Visa övervaknings inställningar. Detta inkluderar visning av Azure Diagnostics-konfigurationen på alla Azure-resurser. |
| Övervaknings deltagare | Med rollen övervaknings deltagare kan du läsa alla övervaknings data och uppdatera övervaknings inställningar.|
| Övervaknings läsare | Med rollen övervaknings läsare kan du läsa alla övervaknings data. |
| Administratör för användaråtkomst |Med rollen Administratör för användaråtkomst kan du hantera användaråtkomsten till Azure Automation-konton. |

## <a name="role-permissions"></a>Roll behörigheter

I följande tabeller beskrivs de angivna behörigheterna för varje roll. Detta kan omfatta åtgärder som ger behörigheter och NotActions, vilket begränsar dem.

### <a name="owner"></a>Ägare

En ägare kan hantera allt, inklusive åtkomst. Följande tabell visar de behörigheter som har beviljats för rollen:

|Åtgärder|Beskrivning|
|---|---|
|Microsoft. Automation/automationAccounts/|Skapa och hantera resurser av alla typer.|

### <a name="contributor"></a>Deltagare

En deltagare kan hantera allt utom åtkomst. Följande tabell visar de behörigheter som beviljas och nekas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/|Skapa och hantera resurser av alla typer|
|**Inte åtgärder**||
|Microsoft. Authorization/*/Delete| Ta bort roller och roll tilldelningar.       |
|Microsoft. Authorization/*/Write     |  Skapa roller och roll tilldelningar.       |
|Microsoft.Authorization/elevateAccess/Action    | Nekar möjligheten att skapa en administratör för användar åtkomst.       |

### <a name="reader"></a>Läsare

En läsare kan visa alla resurser i ett Automation-konto, men kan inte göra några ändringar.

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Visa alla resurser i ett Automation-konto. |

### <a name="automation-operator"></a>Automation-operatör

En Automation-operatör kan skapa och hantera jobb och läsa Runbook-namn och egenskaper för alla Runbooks i ett Automation-konto.  Obs! Om du vill kontrol lera operatörs åtkomst till enskilda Runbooks anger du inte den här rollen och använder i stället rollerna "automatiserings jobb operatör" och "Automation Runbook-operatör" i kombination. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Authorization/*/read|Läs behörighet.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Läs Hybrid Runbook Worker-resurser.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista jobb för Runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Återuppta ett jobb som har pausats.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Avbryt ett pågående jobb.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Läs jobb strömmar och utdata.|
|Microsoft. Automation/automationAccounts/Jobs/utdata/Read|Hämta utdata för ett jobb.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausa ett pågående jobb.|
|Microsoft.Automation/automationAccounts/jobs/write|Skapa jobb.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Hämta ett jobb schema för Azure Automation.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Skapa ett jobb schema för Azure Automation.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Hämta arbets ytan som är länkad till Automation-kontot.|
|Microsoft.Automation/automationAccounts/read|Hämta ett Azure Automation-konto.|
|Microsoft.Automation/automationAccounts/runbooks/read|Hämta en Azure Automation Runbook.|
|Microsoft.Automation/automationAccounts/schedules/read|Få en Azure Automation schema till gång.|
|Microsoft.Automation/automationAccounts/schedules/write|Skapa eller uppdatera en Azure Automation schema till gång.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Läs roller och roll tilldelningar.         |
|Microsoft. Resources/Deployments/*      |Skapa och hantera distributioner av resurs grupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera aviserings regler.        |
|Microsoft.Support/* |Skapa och hantera support ärenden.|

### <a name="automation-job-operator"></a>Automatiserings jobb operatör

En rollen automatiserings jobb operatör beviljas i omfånget Automation-konto. Detta gör det möjligt för operatörs behörighet att skapa och hantera jobb för alla Runbooks i kontot. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Authorization/*/read|Läs behörighet.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista jobb för Runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Återuppta ett jobb som har pausats.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Avbryt ett pågående jobb.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Läs jobb strömmar och utdata.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausa ett pågående jobb.|
|Microsoft.Automation/automationAccounts/jobs/write|Skapa jobb.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Läs roller och roll tilldelningar.       |
|Microsoft. Resources/Deployments/*      |Skapa och hantera distributioner av resurs grupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera aviserings regler.        |
|Microsoft.Support/* |Skapa och hantera support ärenden.|

### <a name="automation-runbook-operator"></a>Automation Runbook-operator

En rollen Automation Runbook-operatör beviljas i Runbook-omfånget. En Automation Runbook-operatör kan visa Runbooks namn och egenskaper.  Den här rollen tillsammans med rollen automatiserings jobb operatör gör det möjligt för operatören att även skapa och hantera jobb för runbooken. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Visa en lista över Runbooks.        |
|Microsoft.Authorization/*/read      | Läs behörighet.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Läs roller och roll tilldelningar.         |
|Microsoft. Resources/Deployments/*      | Skapa och hantera distributioner av resurs grupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera aviserings regler.        |
|Microsoft.Support/*      | Skapa och hantera support ärenden.        |

### <a name="log-analytics-contributor"></a>Log Analytics Contributor

En Log Analytics deltagare kan läsa alla övervaknings data och redigera övervaknings inställningar. Genom att redigera övervaknings inställningarna lägger du till VM-tillägget till virtuella datorer. läsning av lagrings konto nycklar för att kunna konfigurera samling av loggar från Azure Storage. Skapa och konfigurera Automation-konton. lägga till lösningar. och konfigurera Azure Diagnostics på alla Azure-resurser. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs resurser av alla typer, förutom hemligheter.|
|Microsoft. Automation/automationAccounts/*|Hantera Automation-konton.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Skapa och hantera tillägg för virtuella datorer.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Lista klassiska lagrings konto nycklar.|
|Microsoft.Compute/virtualMachines/extensions/*|Skapa och hantera klassiska tillägg för virtuella datorer.|
|Microsoft.Insights/alertRules/*|Läsa/skriva/ta bort aviserings regler.|
|Microsoft.Insights/diagnosticSettings/*|Läsa/skriva/ta bort diagnostikinställningar.|
|Microsoft.OperationalInsights/*|Hantera Azure Monitor loggar.|
|Microsoft.OperationsManagement/*|Hantera lösningar i arbets ytor.|
|Microsoft. Resources/Deployments/*|Skapa och hantera distributioner av resurs grupper.|
|Microsoft. Resources/Subscriptions/ResourceGroups/distributions/*|Skapa och hantera distributioner av resurs grupper.|
|Microsoft. Storage/storageAccounts/Listnycklar/åtgärd|Lista lagrings konto nycklar.|
|Microsoft.Support/*|Skapa och hantera support ärenden.|

### <a name="log-analytics-reader"></a>Log Analytics Reader

En Log Analytics läsare kan visa och söka i alla övervaknings data samt Visa övervaknings inställningar, inklusive Visa konfigurationen av Azure Diagnostics på alla Azure-resurser. Följande tabell visar de behörigheter som beviljas eller nekas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs resurser av alla typer, förutom hemligheter.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Hantera frågor i Azure Monitor loggar.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök Azure Monitor loggdata.|
|Microsoft.Support/*|Skapa och hantera support ärenden.|
|**Inte åtgärder**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Det går inte att läsa nycklar för delad åtkomst.|

### <a name="monitoring-contributor"></a>Övervaknings deltagare

En övervaknings deltagare kan läsa alla övervaknings data och uppdatera övervaknings inställningar. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs resurser av alla typer, förutom hemligheter.|
|Microsoft. AlertsManagement/Alerts/*|Hantera aviseringar.|
|Microsoft.AlertsManagement/alertsSummary/*|Hantera aviserings instrument panelen.|
|Microsoft.Insights/AlertRules/*|Hantera aviserings regler.|
|Microsoft.Insights/components/*|Hantera Application Insights-komponenter.|
|Microsoft.Insights/DiagnosticSettings/*|Hantera diagnostikinställningar.|
|Microsoft.Insights/eventtypes/*|Visa lista över aktivitets logg händelser (hanterings händelser) i en prenumeration. Den här behörigheten gäller för både program mässig och Portal åtkomst till aktivitets loggen.|
|Microsoft.Insights/LogDefinitions/*|Den här behörigheten krävs för användare som behöver åtkomst till aktivitets loggar via portalen. Lista logg kategorier i aktivitets loggen.|
|Microsoft.Insights/MetricDefinitions/*|Läs mått definitioner (lista över tillgängliga mått typer för en resurs).|
|Microsoft.Insights/Metrics/*|Läs mått för en resurs.|
|Microsoft.Insights/Register/Action|Registrera Microsoft. Insights-providern.|
|Microsoft.Insights/webtests/*|Hantera Application Insights webbtester.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Hantera lösnings paket för Azure Monitor loggar.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Hantera sparade sökningar i Azure Monitor loggar.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök Log Analytics arbets ytor.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lista nycklar för en Log Analytics-arbetsyta.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Hantera Azure Monitor loggar lagrings Insight-konfigurationer.|
|Microsoft.Support/*|Skapa och hantera support ärenden.|
|Microsoft. WorkloadMonitor/arbets belastningar/*|Hantera arbets belastningar.|

### <a name="monitoring-reader"></a>Övervaknings läsare

En övervaknings läsare kan läsa alla övervaknings data. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs resurser av alla typer, förutom hemligheter.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök Log Analytics arbets ytor.|
|Microsoft.Support/*|Skapa och hantera support biljetter|

### <a name="user-access-administrator"></a>Administratör för användaråtkomst

En administratör för användar åtkomst kan hantera användar åtkomst till Azure-resurser. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs alla resurser|
|Microsoft. Authorization/*|Hantera auktorisering|
|Microsoft.Support/*|Skapa och hantera support biljetter|

## <a name="onboarding"></a>Publicering

I följande tabeller visas minimi kraven för de behörigheter som krävs för att registrera virtuella datorer för lösningarna för ändrings spårning eller uppdaterings hantering.

### <a name="onboarding-from-a-virtual-machine"></a>Onboarding från en virtuell dator

|**Åtgärd**  |**Permission**  |**Minsta omfång**  |
|---------|---------|---------|
|Skriv ny distribution      | Microsoft. Resources/Deployments/*          |Prenumeration          |
|Skriv ny resurs grupp      | Microsoft. Resources/Subscriptions/resourceGroups/Write        | Prenumeration          |
|Skapa ny standard arbets yta      | Microsoft. OperationalInsights/arbets ytor/Skriv         | Resursgrupp         |
|Skapa nytt konto      |  Microsoft. Automation/automationAccounts/Write        |Resursgrupp         |
|Länka arbets yta och konto      |Microsoft. OperationalInsights/arbets ytor/Skriv</br>Microsoft.Automation/automationAccounts/read|Arbetsyta</br>Automation-konto
|Skapa lösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Resursgrupp          |
|Skapa MMA-tillägg      | Microsoft.Compute/virtualMachines/write         | Virtuell maskin         |
|Skapa Sparad sökning      | Microsoft. OperationalInsights/arbets ytor/Skriv          | Arbetsyta         |
|Skapa omfattnings konfiguration      | Microsoft. OperationalInsights/arbets ytor/Skriv          | Arbetsyta         |
|Länka lösning till omfångs konfiguration      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Lösning         |
|Registrerings tillstånds kontroll – Läs arbets yta      | Microsoft.OperationalInsights/workspaces/read         | Arbetsyta         |
|Registrerings tillstånds kontroll – Läs den länkade arbets ytans egenskap för konto     | Microsoft.Automation/automationAccounts/read      | Automation-konto        |
|Registrerings tillstånds kontroll – Läs lösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Lösning         |
|Registrerings tillstånds kontroll – läsa virtuell dator      | Microsoft.Compute/virtualMachines/read         | Virtuell maskin         |
|Registrerings tillstånds kontroll – Läs konto      | Microsoft.Automation/automationAccounts/read  |  Automation-konto   |
| Kontroll av arbets ytan för VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Prenumeration         |
| Registrera Log Analytics-providern |Microsoft. Insights/register/åtgärd | Prenumeration|

<sup>1</sup> den här behörigheten krävs för att publicera via den virtuella datorns Portal upplevelse.

### <a name="onboarding-from-automation-account"></a>Onboarding från Automation-konto

|**Åtgärd**  |**Permission** |**Minsta omfång**  |
|---------|---------|---------|
|Skapa ny distribution     | Microsoft. Resources/Deployments/*        | Prenumeration         |
|Skapa ny resurs grupp     | Microsoft. Resources/Subscriptions/resourceGroups/Write         | Prenumeration        |
|Bladet AutomationOnboarding – skapa ny arbets yta     |Microsoft. OperationalInsights/arbets ytor/Skriv           | Resursgrupp        |
|AutomationOnboarding blad – Läs länkad arbets yta     | Microsoft.Automation/automationAccounts/read        | Automation-konto       |
|AutomationOnboarding-lösning för blad läsning     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Lösning        |
|AutomationOnboarding blad – Läs arbets yta     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Arbetsyta        |
|Skapa länk för arbets yta och konto     | Microsoft. OperationalInsights/arbets ytor/Skriv        | Arbetsyta        |
|Skriv konto för sko      | Microsoft. Automation/automationAccounts/Write        | Konto        |
|Skapa lösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Resursgrupp         |
|Skapa/redigera Sparad sökning     | Microsoft. OperationalInsights/arbets ytor/Skriv        | Arbetsyta        |
|Skapa/redigera omfattnings konfiguration     | Microsoft. OperationalInsights/arbets ytor/Skriv        | Arbetsyta        |
|Länka lösning till omfångs konfiguration      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Lösning         |
| Registrera Log Analytics-providern |Microsoft. Insights/register/åtgärd | Prenumeration|
|**Steg 2 – publicera flera virtuella datorer**     |         |         |
|Bladet VMOnboarding – skapa MMA-tillägg     | Microsoft.Compute/virtualMachines/write           | Virtuell maskin        |
|Skapa/redigera Sparad sökning     | Microsoft. OperationalInsights/arbets ytor/Skriv           | Arbetsyta        |
|Skapa/redigera omfattnings konfiguration  | Microsoft. OperationalInsights/arbets ytor/Skriv   | Arbetsyta|

## <a name="update-management"></a>Hantering av uppdateringar

Uppdaterings hanteringen når över flera tjänster för att tillhandahålla tjänsten. I följande tabell visas de behörigheter som krävs för att hantera distributioner av uppdaterings hantering:

|**Resurs**  |**Roll**  |**Omfång**  |
|---------|---------|---------|
|Automation-konto     | Log Analytics Contributor       | Automation-konto        |
|Automation-konto    | Virtuell datordeltagare        | Resurs grupp för kontot        |
|Log Analytics-arbetsyta     | Log Analytics Contributor| Log Analytics-arbetsyta        |
|Log Analytics-arbetsyta |Log Analytics Reader| Prenumeration|
|Lösning     |Log Analytics Contributor         | Lösning|
|Virtuell maskin     | Virtuell datordeltagare        | Virtuell maskin        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurera RBAC för ditt Automation-konto

I följande avsnitt visas hur du konfigurerar RBAC på ditt Automation-konto via [portalen](#configure-rbac-using-the-azure-portal) och [PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurera RBAC med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com/) och öppna Automation-kontot från sidan Automation-konton.
2. Klicka på kontrollen **åtkomst kontroll (IAM)** i det övre vänstra hörnet. Då öppnas sidan **åtkomst kontroll (IAM)** där du kan lägga till nya användare, grupper och program för att hantera ditt Automation-konto och visa befintliga roller som kan konfigureras för Automation-kontot.
3. Klicka på den **rolltilldelningar** fliken.

   ![Knappen Åtkomst](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Lägga till en ny användare och tilldela en roll

1. På sidan **åtkomst kontroll (IAM)** klickar du på **+ Lägg till roll tilldelning** för att öppna sidan **Lägg till roll tilldelning** där du kan lägga till en användare, grupp eller ett program och tilldela dem en roll.

2. Välj en roll i listan över tillgängliga roller. Du kan välja någon av de tillgängliga inbyggda rollerna som ett Automation-konto har stöd för eller en anpassad roll som du kan ha definierat.

3. Ange användar namnet för den användare som du vill ge behörighet till i fältet **Välj** . Välj användaren i listan och klicka på **Spara**.

   ![Lägga till användare](media/automation-role-based-access-control/automation-04-add-users.png)

   Nu bör du se användaren som har lagts till på sidan **användare** med den valda rollen tilldelad

   ![Visa användare](media/automation-role-based-access-control/automation-05-list-users.png)

   Du kan också tilldela en roll till användaren från sidan **Roller**.
4. Öppna sidan **roller** genom att klicka på **roller** på sidan **åtkomst kontroll (IAM)** . Härifrån kan du visa namnet på rollen och antalet användare och grupper som har tilldelats till rollen.

    ![Tilldela en roll från sidan Användare](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Rollbaserad åtkomst kontroll kan bara ställas in i Automation-kontots omfattning och inte på någon resurs under Automation-kontot.

#### <a name="remove-a-user"></a>Ta bort en användare

Du kan ta bort åtkomst behörigheten för en användare som inte hanterar Automation-kontot eller som inte längre fungerar för organisationen. Nedan följer stegen för att ta bort en användare:

1. På sidan **åtkomst kontroll (IAM)** väljer du den användare som du vill ta bort och klickar på **ta bort**.
2. Klicka på knappen **Ta bort** på sidan med tilldelningsinformation.
3. Bekräfta borttagningen genom att klicka på **Ja**.

   ![Ta bort användare](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurera RBAC med PowerShell

Rollbaserad åtkomst kan också konfigureras till ett Automation-konto med hjälp av följande [Azure PowerShell-cmdlet: ar](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) visar alla RBAC-roller som är tillgängliga i Azure Active Directory. Du kan använda det här kommandot tillsammans med egenskapen **Namn** för att visa en lista över alla de åtgärder som kan vidtas av en viss roll.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Följande är exempel på utdata:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) visar Azure AD RBAC-roll tilldelningar i det angivna omfånget. Utan parametrar returnerar detta kommando alla rolltilldelningar som skapats under prenumerationen. Använd parametern **ExpandPrincipalGroups** om du vill visa en lista med alla åtkomsttilldelningar för den angivna användaren och för de grupper som användaren är medlem i.
    **Exempel:** Använd följande kommando om du vill visa alla användare och deras roller i ett Automation-konto.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Följande är exempel på utdata:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

[New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) för att tilldela åtkomst till användare, grupper och program till ett visst omfång.
    **Exempel:** Använd följande kommando för att tilldela rollen "Automation-operatör" för en användare i Automation-kontots omfattning.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Följande är exempel på utdata:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Använd [Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) om du vill ta bort åtkomsten till en angiven användare, grupp eller program från ett visst omfång.
    **Exempel:** Använd följande kommando för att ta bort användaren från rollen "Automation-operatör" i Automation-kontots omfattning.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

I föregående exempel ersätter du **inloggnings-ID**, **prenumerations-ID**, **resurs grupp namn**och **namn på Automation-konto** med din konto information. Välj **Ja** när du uppmanas att bekräfta innan du fortsätter att ta bort rolltilldelningen för användaren.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Användar upplevelse för rollen Automation-operatör – Automation-konto

När en användare som är tilldelad rollen som Automation-operatör i Automation-kontots omfång visar det Automation-konto som de är tilldelade till, kan de bara visa listan över Runbooks, Runbook-jobb och scheman som skapats i Automation-kontot, men kan inte visa sina definition. Användaren kan starta, stoppa, pausa, återuppta eller schemalägga runbook-jobbet. Användaren har inte åtkomst till andra Automation-resurser, till exempel konfigurationer, hybrid Worker-grupper eller DSC-noder.

![Ingen åtkomst till resurser](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurera RBAC för Runbooks

Med Azure Automation kan du tilldela RBAC till vissa Runbooks. Gör detta genom att köra följande skript för att lägga till en användare till en angiven Runbook. Följande skript kan köras av en administratör för Automation-kontot eller klient organisations administratören.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation Account
$automationAccountName ="<Automation Account Name>" # Name of the Automation Account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation Account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be ran once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

När du har kört användaren loggar du in på Azure Portal och visar **alla resurser**. I listan visas den Runbook som de lades till som en **Automation Runbook-operator** för.

![Runbook RBAC i portalen](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Användar upplevelse för Automation-operatörs roll – Runbook

När en användare, som är tilldelad rollen som Automation-operatör i Runbook-omfånget, visar en Runbook som de är kopplade till, kan de bara starta runbooken och Visa Runbook-jobben.

![Har bara åtkomst att starta](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du kan konfigurera RBAC på olika sätt med Azure Automation finns i [Hantera rollbaserad åtkomstkontroll med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Mer information om hur du kan starta en runbook på olika sätt finns i [Starta en runbook](automation-starting-a-runbook.md)
* Information om olika runbook-typer finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)

