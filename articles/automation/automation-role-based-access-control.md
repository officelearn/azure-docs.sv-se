---
title: Rollbaserad åtkomstkontroll i Azure Automation
description: Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Den här artikeln beskriver hur du konfigurerar RBAC i Azure Automation.
keywords: automation rbac, rollbaserad åtkomstkontroll, azure rbac
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 93a4befce1f54dcc06d9a8faf31b04e5c0280276
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="role-based-access-control-in-azure-automation"></a>Rollbaserad åtkomstkontroll i Azure Automation

Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Med hjälp av [RBAC](../role-based-access-control/role-assignments-portal.md), du kan särskilja uppgifter i din grupp och ge bara mängden åtkomst till användare, grupper och program som de behöver för att utföra sitt arbete. Rollbaserad åtkomst kan beviljas till användare som använder Azure Portal, Azure-kommandoradsverktygen eller Azure Management-API:er.

## <a name="roles-in-automation-accounts"></a>Roller i Automation-konton

I Azure Automation beviljas åtkomst genom att lämplig RBAC-roll tilldelas till användare, grupper och program i Automation-kontoomfånget. Följande är de inbyggda roller som stöds av ett Automation-konto:

| **Roll** | **Beskrivning** |
|:--- |:--- |
| Ägare |Rollen som ägare ger åtkomst till alla resurser och åtgärder i ett Automation-konto som bland annat ge tillgång till andra användare, grupper och program för att hantera Automation-kontot. |
| Deltagare |Med deltagarrollen kan du hantera allt, men du kan inte ändra andra användares åtkomstbehörighet till ett Automation-konto. |
| Läsare |Med läsarrollen kan du visa alla resurser i ett Automation-konto men inte göra några ändringar. |
| Automation-operatör |Rollen Automation operatör kan du visa runbook-namn och egenskaper att skapa och hantera jobb för alla runbooks i ett Automation-konto. Den här rollen är användbar om du vill skydda dina Automation-kontoresurser, t.ex. autentiseringstillgångar och runbooks, så att de inte kan visas eller ändras men fortfarande vill att medlemmar i din organisation ska kunna köra dessa runbooks. |
|Automation-jobboperator|Rollen operatör för Automation-jobb kan du skapa och hantera jobb för alla runbooks i ett Automation-konto.|
|Automation Runbook-operator|Rollen Automation Runbook-operatör kan du visa namn och egenskaper i en runbook.|
| Log Analytics Contributor | Log Analytics deltagarrollen kan du läsa alla övervakningsdata och redigera inställningarna för övervakning. Redigera inställningarna för övervakning innehåller lägger till VM-tillägget på virtuella datorer kan läsa lagringskontonycklar för att kunna konfigurera samlingen loggar från Azure storage, skapa och konfigurera Automation-konton, att lägga till lösningar och konfigurerar Azure-diagnostik i alla Azure-resurser.|
| Log Analytics Reader | Rollen Log Analytics läsare kan du visa och Sök alla övervakning data samt visa inställningar för övervakning. Detta inkluderar visar konfigurationen för Azure-diagnostik på alla Azure-resurser. |
| Övervaka deltagare | Övervaka deltagarrollen kan du läsa alla data och uppdatera inställningar för övervakning.|
| Övervaka läsare | Rollen Montioring läsare kan du läsa alla övervakningsdata. |
| Administratör för användaråtkomst |Med rollen Administratör för användaråtkomst kan du hantera användaråtkomsten till Azure Automation-konton. |

## <a name="role-permissions"></a>Rollbehörigheter

I följande tabeller beskrivs de specifika behörigheter som tilldelats varje roll. Detta kan inkludera åtgärder, som ger behörigheter och NotActions som begränsar dem.

### <a name="owner"></a>Ägare

En ägare kan hantera allt, inklusive åtkomst. I följande tabell visas behörigheter för rollen:

|Åtgärder|Beskrivning|
|---|---|
|Microsoft.Automation/automationAccounts/|Skapa och hantera resurser av alla typer.|

### <a name="contributor"></a>Deltagare

Deltagare kan hantera allt utom åtkomst. I följande tabell visas de behörigheter som beviljas och nekas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Skapa och hantera resurser av alla typer av|
|**Inte åtgärder**||
|Microsoft.Authorization/*/Delete| Ta bort roller och rolltilldelningar.       |
|Microsoft.Authorization/*/Write     |  Skapa roller och rolltilldelningar.       |
|Microsoft.Authorization/elevateAccess/Action    | Nekar möjlighet att skapa en administratör för användaråtkomst.       |

### <a name="reader"></a>Läsare

En läsare kan visa alla resurser i ett Automation-konto men göra inte några ändringar.

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Visa alla resurser i ett Automation-konto. |

### <a name="automation-job-operator"></a>Automation-jobboperator

Ett Automation-jobb operatörsrollen beviljas definitionsområdet Automation-konto. På så sätt kan operatorn behörigheter att skapa och hantera jobb för alla runbooks i kontot. I följande tabell visas behörigheter för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Authorization/*/read|Läsa tillstånd.|
|Microsoft.Automation/automationAccounts/jobs/read|Visa jobb för runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Återuppta ett jobb som är pausad.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Avbryta ett jobb pågår.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Läs dataströmmar för jobbet och utdata.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausa ett jobb pågår.|
|Microsoft.Automation/automationAccounts/jobs/write|Skapa jobb.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Läsa roller och rolltilldelningar.       |
|Microsoft.Resources/deployments/*      |Skapa och hantera distributionen av resursgrupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera Varningsregler.        |
|Microsoft.Support/* |Skapa och hantera supportärenden.|

### <a name="automation-runbook-operator"></a>Automation Runbook-operator

En roll för Automation Runbook-operatör beviljas definitionsområdet Runbook. En Operator för Automation-Runbook kan visa runbook-namn och egenskaper.  Den här rollen i kombination med rollen Automation jobbet-operatorn kan operatorn för att skapa och hantera jobb för runbook. I följande tabell visas behörigheter för rollen:

> [!NOTE]
> Ange inte rollen Automation-operatorn om du inte vill ge en operator möjligheten att hantera jobb för alla runbooks i kontot.

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Visa en lista med runbooks.        |
|Microsoft.Authorization/*/read      | Läsa tillstånd.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Läsa roller och rolltilldelningar.         |
|Microsoft.Resources/deployments/*      | Skapa och hantera distributionen av resursgrupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera Varningsregler.        |
|Microsoft.Support/*      | Skapa och hantera supportärenden.        |

### <a name="automation-operator"></a>Automation-operatör

En Automation-Operator kan skapa och hantera jobb och läsa egenskaper för alla runbooks i ett Automation-konto och runbook-namn.  Obs: Om du vill styra operatorn åtkomsten till enskilda runbooks och sedan inte ange den här rollen, och i stället använda rollerna Automation jobbet-operatorn och Automation Runbook-operatör.  I följande tabell visas behörigheter för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Authorization/*/read|Läsa tillstånd.|
|Microsoft.Automation/automationAccounts/jobs/read|Visa jobb för runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Återuppta ett jobb som är pausad.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Avbryta ett jobb pågår.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Läs dataströmmar för jobbet och utdata.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausa ett jobb pågår.|
|Microsoft.Automation/automationAccounts/jobs/write|Skapa jobb.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Läsa roller och rolltilldelningar.         |
|Microsoft.Resources/deployments/*      |Skapa och hantera distributionen av resursgrupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera Varningsregler.        |
|Microsoft.Support/* |Skapa och hantera supportärenden.|

### <a name="log-analytics-contributor"></a>Log Analytics Contributor

Log Analytics deltagare kan läsa alla övervakningsdata och redigera inställningarna för övervakning. Redigera inställningarna för övervakning innehåller lägger till VM-tillägget på virtuella datorer; läsa lagringskontonycklar för att kunna konfigurera samlingen loggar från Azure Storage; Skapa och konfigurera Automation-konton lägga till lösningar. och konfigurera Azure-diagnostik på alla Azure-resurser. I följande tabell visas behörigheter för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa resurser av alla typer utom hemligheter.|
|Microsoft.Automation/automationAccounts/*|Hantera automation-konton.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Skapa och hantera tillägg för virtuell dator.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Visa en lista med klassiska lagringskontonycklar.|
|Microsoft.Compute/virtualMachines/extensions/*|Skapa och hantera klassiska virtuella datortillägg.|
|Microsoft.Insights/alertRules/*|Läs/Skriv/ta bort Varningsregler.|
|Microsoft.Insights/diagnosticSettings/*|Diagnostikinställningar för Läs/Skriv/ta bort.|
|Microsoft.OperationalInsights/*|Hantera logganalys.|
|Microsoft.OperationsManagement/*|Hantera lösningar i arbetsytor.|
|Microsoft.Resources/deployments/*|Skapa och hantera distributionen av resursgrupper.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Skapa och hantera distributionen av resursgrupper.|
|Microsoft.Storage/storageAccounts/listKeys/action|Lista nycklar för lagringskonto.|
|Microsoft.Support/*|Skapa och hantera supportärenden.|

### <a name="log-analytics-reader"></a>Log Analytics Reader

En Log Analytics läsare kan visa och söka efter alla övervakningsdata och visa övervakningsinställningarna, inklusive visar konfigurationen för Azure-diagnostik på alla Azure-resurser. I följande tabell visas de behörigheter som beviljas eller nekas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa resurser av alla typer utom hemligheter.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Hantera frågor i logganalys.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök Log Analytics-data.|
|Microsoft.Support/*|Skapa och hantera supportärenden.|
|**Inte åtgärder**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Det går inte att läsa nycklar för delad åtkomst.|

### <a name="monitoring-contributor"></a>Övervaka deltagare

Övervaka deltagare kan läsa alla övervakningsdata och uppdatera inställningarna för övervakning. I följande tabell visas behörigheter för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa resurser av alla typer utom hemligheter.|
|Microsoft.AlertsManagement/alerts/*|Hantera aviseringar.|
|Microsoft.AlertsManagement/alertsSummary/*|Hantera aviseringar instrumentpanelen.|
|Microsoft.Insights/AlertRules/*|Hantera Varningsregler.|
|Microsoft.Insights/components/*|Hantera Application Insights-komponenter.|
|Microsoft.Insights/DiagnosticSettings/*|Hantera diagnostikinställningar.|
|Microsoft.Insights/eventtypes/*|Lista över aktivitetsloggen händelser (management-händelser) i en prenumeration. Den här behörigheten gäller både programmässiga och portal åtkomst till aktivitetsloggen.|
|Microsoft.Insights/LogDefinitions/*|Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggen kategorier i aktivitetsloggen.|
|Microsoft.Insights/MetricDefinitions/*|Läs måttdefinitionerna (lista över tillgängliga mått typer för en resurs).|
|Microsoft.Insights/Metrics/*|Läsa måtten för en resurs.|
|Microsoft.Insights/Register/Action|Registrera providern Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Hantera Application Insights webbtester.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Hantera logganalys lösning packs.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Hantera logganalys sparade sökningar.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök logganalys arbetsytor.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lista nycklar för logganalys-arbetsytan.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Hantera logganalys insight lagringskonfigurationer.|
|Microsoft.Support/*|Skapa och hantera supportärenden.|
|Microsoft.WorkloadMonitor/workloads/*|Hantera arbetsbelastningar.|

### <a name="monitoring-reader"></a>Övervaka läsare

En övervakning läsare kan läsa alla övervakningsdata. I följande tabell visas behörigheter för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa resurser av alla typer utom hemligheter.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök logganalys arbetsytor.|
|Microsoft.Support/*|Skapa och hantera supportärenden|

### <a name="user-access-administrator"></a>Administratör för användaråtkomst

En administratör för användaråtkomst kan hantera användarnas åtkomst till Azure-resurser. I följande tabell visas behörigheter för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa alla resurser|
|Microsoft.Authorization/*|Hantera auktorisering|
|Microsoft.Support/*|Skapa och hantera supportärenden|

## <a name="onboarding"></a>Publicering

I följande tabeller visas lägsta behörighet som krävs för onboarding virtuella datorer för ändringsspårning eller uppdatera lösningar för hantering.

### <a name="onboarding-from-a-virtual-machine"></a>Onboarding från en virtuell dator

|**Åtgärd**  |**Behörighet**  |**Minsta scope**  |
|---------|---------|---------|
|Skriva ny distribution      | Microsoft.Resources/deployments/*          |Prenumeration          |
|Skriv den nya resursgruppen.      | Microsoft.Resources/subscriptions/resourceGroups/write        | Prenumeration          |
|Skapa nya standarden arbetsytan      | Microsoft.OperationalInsights/workspaces/write         | Resursgrupp         |
|Skapa nytt konto      |  Microsoft.Automation/automationAccounts/write        |Resursgrupp         |
|Länken arbetsytan och konto      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Arbetsyta</br>Automation-konto
|Skapa lösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Resursgrupp          |
|Skapa MMA tillägg      | Microsoft.Compute/virtualMachines/write         | Virtuell dator         |
|Skapa en sparad sökning      | Microsoft.OperationalInsights/workspaces/write          | Arbetsyta         |
|Skapa scope config      | Microsoft.OperationalInsights/workspaces/write          | Arbetsyta         |
|Länken lösning scope config      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Lösning         |
|Kontrollera tillståndet för Onboarding - Läs arbetsytan      | Microsoft.OperationalInsights/workspaces/read         | Arbetsyta         |
|Onboarding tillstånd check - Läs länkade arbetsytan-egenskapen för kontot     | Microsoft.Automation/automationAccounts/read      | Automation-konto        |
|Kontrollera tillståndet för Onboarding - Läs lösningen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Lösning         |
|Onboarding tillstånd check - Läs VM      | Microsoft.Compute/virtualMachines/read         | Virtuell dator         |
|Kontrollera tillståndet för Onboarding - läsa konto      | Microsoft.Automation/automationAccounts/read  |  Automation-konto   |

### <a name="onboarding-from-automation-account"></a>Onboarding från Automation-konto

|**Åtgärd**  |**Behörighet** |**Minsta Scope**  |
|---------|---------|---------|
|Skapa ny distribution     | Microsoft.Resources/deployments/*        | Prenumeration         |
|Skapa ny resursgrupp     | Microsoft.Resources/subscriptions/resourceGroups/write         | Prenumeration        |
|AutomationOnboarding bladet – Skapa ny arbetsyta     |Microsoft.OperationalInsights/workspaces/write           | Resursgrupp        |
|AutomationOnboarding-bladet – Läs länkade arbetsytan     | Microsoft.Automation/automationAccounts/read        | Automation-konto       |
|AutomationOnboarding-bladet – Läs lösningen     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Lösning        |
|AutomationOnboarding-bladet – Läs arbetsytan     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Arbetsyta        |
|Skapa länk för arbetsytan och konto     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Skriva konto för lådan      | Microsoft.Automation/automationAccounts/write        | Konto        |
|Skapa lösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Resursgrupp         |
|Skapa eller redigera sparade sökningen     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Skapa och redigera scope-konfiguration     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Länken lösning scope config      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Lösning         |
|**Steg 2 – publicera flera virtuella datorer**     |         |         |
|VMOnboarding bladet – skapa MMA tillägg     | Microsoft.Compute/virtualMachines/write           | Virtuell dator        |
|Skapa / redigera sparad sökning     | Microsoft.OperationalInsights/workspaces/write           | Arbetsyta        |
|Skapa / redigera scope-konfiguration  | Microsoft.OperationalInsights/workspaces/write   | Arbetsyta|

## <a name="update-management"></a>Hantering av uppdateringar

Uppdateringshantering når över flera tjänster att tillhandahålla sin tjänst. I följande tabell visas de behörigheter som krävs för att hantera distributioner av hantering:

|**Resurs**  |**Roll**  |**Omfång**  |
|---------|---------|---------|
|Automation-konto     | Log Analytics Contributor       | Automation-konto        |
|Automation-konto    | Virtuell datordeltagare        | Resursgruppen för kontot        |
|Log Analytics-arbetsyta     | Log Analytics Contributor| Log Analytics-arbetsyta        |
|Log Analytics-arbetsyta |Log Analytics Reader| Prenumeration|
|Lösning     |Log Analytics Contributor         | Lösning|
|Virtuell dator     | Virtuell datordeltagare        | Virtuell dator        |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Konfigurera RBAC för ditt Automation-konto med hjälp av Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com/) och öppna Automation-kontot från sidan Automation-konton.
2. Klicka på den **åtkomstkontroll (IAM)** kontrollen i det övre vänstra hörnet. Då öppnas den **åtkomstkontroll (IAM)** sidan där du kan lägga till nya användare, grupper och program för att hantera ditt Automation-kontot och visa befintliga roller som kan konfigureras för Automation-kontot.

   ![Knappen Åtkomst](media/automation-role-based-access-control/automation-01-access-button.png)

### <a name="add-a-new-user-and-assign-a-role"></a>Lägga till en ny användare och tilldela en roll

1. Från den **åtkomstkontroll (IAM)** klickar du på **+ Lägg till** att öppna den **lägga till behörigheter** sida där du kan lägga till en användare, grupp eller ett program och tilldela dem en roll.

2. Välj en roll i listan över tillgängliga roller. Du kan välja någon av de tillgängliga inbyggda roller som har stöd för ett Automation-konto eller en anpassad roll som du har definierat.

3. Ange användarnamnet för användaren som du vill ge behörighet till i den **Välj** fältet. Välj användaren i listan och klicka på **spara**.

   ![Lägga till användare](media/automation-role-based-access-control/automation-04-add-users.png)

   Nu bör du se den användare som lagts till i den **användare** sidan med den valda rollen tilldelas

   ![Visa användare](media/automation-role-based-access-control/automation-05-list-users.png)

   Du kan också tilldela en roll till användaren från sidan **Roller**.
4. Klicka på **roller** från den **åtkomstkontroll (IAM)** kan du öppna den **roller** sidan. Härifrån kan du visa namnet på rollen och antalet användare och grupper som har tilldelats till rollen.

    ![Tilldela en roll från sidan Användare](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Rollbaserad åtkomstkontroll kan bara anges i omfånget för Automation-konto och inte på en resurs under Automation-kontot.

### <a name="remove-a-user"></a>Ta bort en användare

Du kan ta bort åtkomstbehörighet för en användare som inte hanterar Automation-kontot eller som inte längre fungerar för organisationen. Nedan följer stegen för att ta bort en användare:

1. Från den **åtkomstkontroll (IAM)** väljer användaren vill ta bort och klicka på **ta bort**.
2. Klicka på knappen **Ta bort** på sidan med tilldelningsinformation.
3. Bekräfta borttagningen genom att klicka på **Ja**.

   ![Ta bort användare](media/automation-role-based-access-control/automation-08-remove-users.png)

## <a name="role-assigned-user"></a>Rollen tilldelad användare

När en användare som tilldelats en roll loggar in på Azure och väljer deras Automation-konto, kan de nu se ägarens konto som angetts i listan över **kataloger**. För att visa det Automation-konto som de har lagts till i måste de byta standardkatalogen till ägarens standardkatalog.

### <a name="user-experience-for-automation-operator-role"></a>Användarupplevelsen för Automation-operatörsrollen

När en användare som har tilldelats Automation operatorn rollen vyer Automation-konto som har tilldelats, kan bara visa listan över runbooks, runbook-jobb och scheman i Automation-konto men det går inte att visa deras definition. Användaren kan starta, stoppa, pausa, återuppta eller schemalägga runbook-jobbet. Användaren har inte åtkomst till andra Automation-resurser, till exempel konfigurationer, hybrid worker-grupper eller DSC-noder.

![Ingen åtkomst till resurser](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Användaren kan visa och skapa scheman, men har inte åtkomst till någon annan typ av tillgång.

Den här användaren kan inte heller visa webhooks som är associerade med en runbook.

![Ingen åtkomst till webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Konfigurera RBAC för ditt Automation-konto med hjälp av Azure PowerShell

Du kan också konfigurera rollbaserad åtkomst till ett Automation-konto med hjälp av följande [Azure PowerShell-cmdlets](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) listar alla RBAC-roller som är tillgängliga i Azure Active Directory. Du kan använda det här kommandot tillsammans med egenskapen **Namn** för att visa en lista över alla de åtgärder som kan vidtas av en viss roll.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Följande är exempel på utdata:

```azurepowershell-interactive
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) listar Azure AD RBAC rolltilldelningar i det specificerade omfånget. Utan parametrar returnerar detta kommando alla rolltilldelningar som skapats under prenumerationen. Använd parametern **ExpandPrincipalGroups** om du vill visa en lista med alla åtkomsttilldelningar för den angivna användaren och för de grupper som användaren är medlem i.
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

[Nya AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) du tilldelar åtkomst till användare, grupper och program till ett visst scope.
    **Exempel:** använder du följande kommando för att tilldela rollen ”Automation-operatör” för en användare i området för Automation-konto.

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

Använd [ta bort AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) att ta bort åtkomsten till en specifik användare, grupp eller ett program från ett visst scope.
    **Exempel:** använder du följande kommando för att ta bort användaren från rollen ”Automation-operatör” i omfånget för Automation-konto.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

I föregående exempel ersätta **inloggning Id**, **prenumerations-Id**, **resursgruppens namn**, och **Automation kontonamn** med din kontoinformation. Välj **Ja** när du uppmanas att bekräfta innan du fortsätter att ta bort rolltilldelningen för användaren.

## <a name="next-steps"></a>Nästa steg

* Information om hur du kan konfigurera RBAC på olika sätt med Azure Automation finns i [Hantera rollbaserad åtkomstkontroll med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Mer information om hur du kan starta en runbook på olika sätt finns i [Starta en runbook](automation-starting-a-runbook.md)
* Information om olika runbook-typer finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
