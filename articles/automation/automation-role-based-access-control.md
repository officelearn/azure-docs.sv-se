---
title: Rollbaserad åtkomstkontroll i Azure Automation
description: Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Den här artikeln beskriver hur du konfigurerar RBAC i Azure Automation.
keywords: automation rbac, rollbaserad åtkomstkontroll, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: 8caf502db91ab09eea48fc8a902dacf6bf40f24c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278641"
---
# <a name="role-based-access-control-in-azure-automation"></a>Rollbaserad åtkomstkontroll i Azure Automation

Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Med HJÄLP AV [RBAC](../role-based-access-control/overview.md)kan du segregera uppgifter inom ditt team och endast bevilja åtkomst till användare, grupper och program som de behöver för att utföra sina jobb. Du kan bevilja rollbaserad åtkomst till användare med hjälp av Azure-portalen, Azure Command-Line-verktygen eller Azure Management API:er.

## <a name="roles-in-automation-accounts"></a>Roller i Automation-konton

I Azure Automation beviljas åtkomst genom att lämplig RBAC-roll tilldelas till användare, grupper och program i Automation-kontoomfånget. Följande är de inbyggda roller som stöds av ett Automation-konto:

| **Roll** | **Beskrivning** |
|:--- |:--- |
| Ägare |Ägarrollen ger åtkomst till alla resurser och åtgärder i ett Automation-konto, inklusive åtkomst till andra användare, grupper och program för att hantera Automation-kontot. |
| Deltagare |Med deltagarrollen kan du hantera allt, men du kan inte ändra andra användares åtkomstbehörighet till ett Automation-konto. |
| Läsare |Med läsarrollen kan du visa alla resurser i ett Automation-konto men inte göra några ändringar. |
| Automation-operatör |Med rollen Automationsoperatör kan du visa runbooknamn och egenskaper och skapa och hantera jobb för alla runbooks i ett Automation-konto. Den här rollen är användbar om du vill skydda dina Automation-kontoresurser som autentiseringstillgångar och runbooks från att visas eller ändras, men ändå tillåter medlemmar i organisationen att köra dessa runbooks. |
|Jobboperator för automatisering|Med rollen Automation Job Operator kan du skapa och hantera jobb för alla runbooks i ett Automation-konto.|
|Automation Runbook Operatör|Med rollen Automation Runbook Operator kan du visa en runbooks namn och egenskaper.|
| Log Analytics Contributor | Med rollen Log Analytics Contributor kan du läsa alla övervakningsdata och redigera övervakningsinställningar. Redigering av övervakningsinställningarna inkluderar att lägga till vm-tillägget till virtuella datorer, läsa lagringskontonycklar för att kunna konfigurera insamling av loggar från Azure-lagring, skapa och konfigurera Automation-konton, lägga till lösningar och konfigurera Azure-diagnostik på alla Azure-resurser.|
| Log Analytics Reader | Med rollen Log Analytics Reader kan du visa och söka efter alla övervakningsdata samt visa övervakningsinställningar. Detta inkluderar visning av konfigurationen av Azure-diagnostik på alla Azure-resurser. |
| Övervakningsbidragsgivare | Med rollen Övervakningsbidragsgivare kan du läsa alla övervakningsdata och uppdatera övervakningsinställningar.|
| Övervakningsläsare | Med rollen Övervakningsläsare kan du läsa alla övervakningsdata. |
| Administratör för användaråtkomst |Med rollen Administratör för användaråtkomst kan du hantera användaråtkomsten till Azure Automation-konton. |

## <a name="role-permissions"></a>Behörigheter för roll

I följande tabeller beskrivs de specifika behörigheter som ges till varje roll. Detta kan omfatta åtgärder som ger behörigheter och NotActions, som begränsar dem.

### <a name="owner"></a>Ägare

En ägare kan hantera allt, inklusive åtkomst. I följande tabell visas de behörigheter som beviljats för rollen:

|Åtgärder|Beskrivning|
|---|---|
|Microsoft.Automation/automationKonton/|Skapa och hantera resurser av alla slag.|

### <a name="contributor"></a>Deltagare

En deltagare kan hantera allt utom åtkomst. I följande tabell visas de behörigheter som beviljats och nekats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationKonton/|Skapa och hantera resurser av alla typer|
|**Åtgärder inte**||
|Microsoft.Auktorisering/*/Ta bort| Ta bort roller och rolltilldelningar.       |
|Microsoft.Auktorisering/*/skrivning     |  Skapa roller och rolltilldelningar.       |
|Microsoft.Authorization/elevateAccess/Åtgärd    | Nekar möjligheten att skapa en administratör för användaråtkomst.       |

### <a name="reader"></a>Läsare

En läsare kan visa alla resurser i ett Automation-konto men kan inte göra några ändringar.

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationKonto/läsa|Visa alla resurser i ett Automation-konto. |

### <a name="automation-operator"></a>Automation-operatör

En Automation Operator kan skapa och hantera jobb och läsa runbooknamn och egenskaper för alla runbooks i ett Automation-konto.  Om du vill styra operatörens åtkomst till enskilda runbooks ska du inte ange den här rollen och använd i stället rollerna "Automation Job Operator" och "Automation Runbook Operator" i kombination. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Auktorisering/*/läs|Läs auktorisering.|
|Microsoft.Automation/automationKonto/hybridRunbookWorkerGroups/read|Läs Hybrid Runbook Worker Resources.|
|Microsoft.Automation/automationKonto/jobb/läsa|Lista jobb för runbooken.|
|Microsoft.Automation/automationKonto/jobb/återuppta/åtgärd|Återuppta ett jobb som är pausat.|
|Microsoft.Automation/automationKonto/jobb/stopp/åtgärd|Avbryt ett pågående jobb.|
|Microsoft.Automation/automationKonto/jobb/strömmar/läsa|Läs jobbströmmarna och utdata.|
|Microsoft.Automation/automationKonto/jobb/utdata/läsa|Hämta utdata för ett jobb.|
|Microsoft.Automation/automationKonto/jobb/pausa/åtgärd|Pausa ett pågående jobb.|
|Microsoft.Automation/automationKonton/jobb/skrivning|Skapa jobb.|
|Microsoft.Automation/automationKonton/jobbPlaner/läsa|Skaffa ett Azure Automation-jobbschema.|
|Microsoft.Automation/automationKonton/jobbSchedules/write|Skapa ett Azure Automation-jobbschema.|
|Microsoft.Automation/automationKonto/linkedWorkspace/read|Få arbetsytan länkad till Automation-kontot.|
|Microsoft.Automation/automationKonto/läsa|Skaffa ett Azure Automation-konto.|
|Microsoft.Automation/automationKonto/runbooks/read|Skaffa en Azure Automation-runbook.|
|Microsoft.Automation/automationKonto/scheman/läsa|Skaffa en Azure Automation-schematillgång.|
|Microsoft.Automation/automationKonton/scheman/skrivning|Skapa eller uppdatera en Azure Automation-schematillgång.|
|Microsoft.Resurser/prenumerationer/resourceGroups/read      |Läs roller och rolltilldelningar.         |
|Microsoft.Resources/deployments/*      |Skapa och hantera resursgruppsdistributioner.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera varningsregler.        |
|Microsoft.Support/* |Skapa och hantera supportbiljetter.|

### <a name="automation-job-operator"></a>Jobboperator för automatisering

En roll för en automationsjobboperatör beviljas i scopet för Automation-konto.På så sätt kan operatörsbehörigheterna skapa och hantera jobb för alla runbooks i kontot. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Auktorisering/*/läs|Läs auktorisering.|
|Microsoft.Automation/automationKonto/jobb/läsa|Lista jobb för runbooken.|
|Microsoft.Automation/automationKonto/jobb/återuppta/åtgärd|Återuppta ett jobb som är pausat.|
|Microsoft.Automation/automationKonto/jobb/stopp/åtgärd|Avbryt ett pågående jobb.|
|Microsoft.Automation/automationKonto/jobb/strömmar/läsa|Läs jobbströmmarna och utdata.|
|Microsoft.Automation/automationKonto/jobb/pausa/åtgärd|Pausa ett pågående jobb.|
|Microsoft.Automation/automationKonton/jobb/skrivning|Skapa jobb.|
|Microsoft.Resurser/prenumerationer/resourceGroups/read      |  Läs roller och rolltilldelningar.       |
|Microsoft.Resources/deployments/*      |Skapa och hantera resursgruppsdistributioner.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera varningsregler.        |
|Microsoft.Support/* |Skapa och hantera supportbiljetter.|

### <a name="automation-runbook-operator"></a>Automation Runbook Operatör

Rollen Automation Runbook Operator beviljas i Runbook-scopet. En Automation Runbook Operator kan visa runbookens namn och egenskaper.Den här rollen i kombination med rollen "Automation Job Operator" gör det möjligt för operatören att även skapa och hantera jobb för runbooken. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationKonto/runbooks/read     | Lista runbooks.        |
|Microsoft.Auktorisering/*/läs      | Läs auktorisering.        |
|Microsoft.Resurser/prenumerationer/resourceGroups/read      |Läs roller och rolltilldelningar.         |
|Microsoft.Resources/deployments/*      | Skapa och hantera resursgruppsdistributioner.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera varningsregler.        |
|Microsoft.Support/*      | Skapa och hantera supportbiljetter.        |

### <a name="log-analytics-contributor"></a>Log Analytics Contributor

En Log Analytics-deltagare kan läsa alla övervakningsdata och redigera övervakningsinställningar. Redigering av övervakningsinställningarna inkluderar att lägga till vm-tillägget till virtuella datorer. läsa lagringskontonycklar för att kunna konfigurera insamling av loggar från Azure Storage; skapa och konfigurera Automation-konton; Lägga till lösningar; och konfigurera Azure-diagnostik på alla Azure-resurser. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läs|Läs resurser av alla slag, utom hemligheter.|
|Microsoft.Automation/automationKonto/*|Hantera Automation-konton.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Skapa och hantera tillägg för virtuella datorer.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Lista klassiska lagringskontonycklar.|
|Microsoft.Compute/virtualMachines/extensions/*|Skapa och hantera klassiska tillägg för virtuella datorer.|
|Microsoft.Insights/alertRules/*|Läs/skriv/ta bort varningsregler.|
|Microsoft.Insights/diagnosticSettings/*|Läs/skriv/ta bort diagnostikinställningar.|
|Microsoft.OperationalInsights/*|Hantera Azure Monitor-loggar.|
|Microsoft.OperationsManagement/*|Hantera lösningar på arbetsytor.|
|Microsoft.Resources/deployments/*|Skapa och hantera resursgruppsdistributioner.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Skapa och hantera resursgruppsdistributioner.|
|Microsoft.Storage/storageAccounts/listKeys/action|Lista lagringskontonycklar.|
|Microsoft.Support/*|Skapa och hantera supportbiljetter.|

### <a name="log-analytics-reader"></a>Log Analytics Reader

En Log Analytics Reader kan visa och söka efter alla övervakningsdata samt och visa övervakningsinställningar, inklusive visning av konfigurationen av Azure-diagnostik på alla Azure-resurser. I följande tabell visas de behörigheter som beviljats eller nekats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läs|Läs resurser av alla slag, utom hemligheter.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Hantera frågor i Azure Monitor-loggar.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök i Azure Monitor-loggdata.|
|Microsoft.Support/*|Skapa och hantera supportbiljetter.|
|**Åtgärder inte**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read Microsoft.OperationalInsights/workspaces/sharedKeys/read Microsoft.OperationalInsights/workspaces/sharedKeys/read Microsoft.|Det går inte att läsa de delade åtkomstnycklarna.|

### <a name="monitoring-contributor"></a>Övervakningsbidragsgivare

En övervakningsbidragsgivare kan läsa alla övervakningsdata och uppdatera övervakningsinställningar. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läs|Läs resurser av alla slag, utom hemligheter.|
|Microsoft.AlertsManagement/alerts/*|Hantera aviseringar.|
|Microsoft.AlertsManagement/alertsSummary/*|Hantera instrumentpanelen för avisering.|
|Microsoft.Insights/AlertRules/*|Hantera varningsregler.|
|Microsoft.Insights/komponenter/*|Hantera komponenter för application insights.|
|Microsoft.Insights/DiagnosticSettings/*|Hantera diagnostikinställningar.|
|Microsoft.Insights/eventtypes/*|Lista aktivitetslogghändelser (hanteringshändelser) i en prenumeration. Den här behörigheten gäller både programmatisk och portalåtkomst till aktivitetsloggen.|
|Microsoft.Insights/LogDefinitions/*|Den här behörigheten är nödvändig för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggkategorier i Aktivitetsloggen.|
|Microsoft.Insights/MetricDefinitions/*|Läs måttdefinitioner (lista över tillgängliga måtttyper för en resurs).|
|Microsoft.Insights/Metrics/*|Läs mått för en resurs.|
|Microsoft.Insights/Register/Åtgärd|Registrera Microsoft.Insights-leverantören.|
|Microsoft.Insights/webtests/*|Hantera webbtester för Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Hantera Azure Monitor-loggar lösningspaket.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Hantera sparade sökningar i Azure Monitor-loggar.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök logganalysarbetsytor.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lista nycklar för en Log Analytics-arbetsyta.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Hantera Azure Monitor loggar konfigurationer lagringsinsikt.|
|Microsoft.Support/*|Skapa och hantera supportbiljetter.|
|Microsoft.WorkloadMonitor/arbetsbelastningar/*|Hantera arbetsbelastningar.|

### <a name="monitoring-reader"></a>Övervakningsläsare

En övervakningsläsare kan läsa alla övervakningsdata. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läs|Läs resurser av alla slag, utom hemligheter.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök logganalysarbetsytor.|
|Microsoft.Support/*|Skapa och hantera supportbiljetter|

### <a name="user-access-administrator"></a>Administratör för användaråtkomst

En administratör för användaråtkomst kan hantera användaråtkomst till Azure-resurser. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läs|Läs alla resurser|
|Microsoft.Auktorisering/*|Hantera auktorisering|
|Microsoft.Support/*|Skapa och hantera supportbiljetter|

## <a name="onboarding"></a>Publicering

Följande tabeller visar de lägsta behörigheter som krävs för introduktion av virtuella datorer för lösningar för ändringsspårning eller uppdateringshantering.

### <a name="onboarding-from-a-virtual-machine"></a>Onboarding från en virtuell dator

|**Åtgärd**  |**Behörighet**  |**Minsta omfattning**  |
|---------|---------|---------|
|Skriv ny distribution      | Microsoft.Resources/deployments/*          |Prenumeration          |
|Skriv ny resursgrupp      | Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.        | Prenumeration          |
|Skapa ny standardarbetsyta      | Microsoft.OperationalInsights/workspaces/write         | Resursgrupp         |
|Skapa nytt konto      |  Microsoft.Automation/automationKonto/skrivning        |Resursgrupp         |
|Länk arbetsyta och konto      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationKonto/läsa|Arbetsyta</br>Automation-konto
|Skapa MMA-tillägg      | Microsoft.Compute/virtualMachines/write Microsoft.Compute/virtualMachines/write Microsoft.Compute/virtualMachines/write Microsoft.         | Virtuell dator         |
|Skapa sparad sökning      | Microsoft.OperationalInsights/workspaces/write          | Arbetsyta         |
|Skapa scope-konfiguration      | Microsoft.OperationalInsights/workspaces/write          | Arbetsyta         |
|Tillståndskontroll för introduktion - Läs arbetsyta      | Microsoft.OperationalInsights/workspaces/read Microsoft.OperationalInsights/workspaces/read Microsoft.OperationalInsights/workspaces/read Microsoft.         | Arbetsyta         |
|Tillståndskontroll för introduktionstillstånd - Läs kontots egendom för länkade arbetsytan     | Microsoft.Automation/automationKonto/läsa      | Automation-konto        |
|Onboarding tillståndskontroll - Läs lösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read Microsoft.OperationalInsights/workspaces/intelligencepacks/read Microsoft.OperationalInsights/workspaces/intelligencepacks/read Microsoft.          | Lösning         |
|Tillståndskontroll för introduktionstillstånd - Läs vm      | Microsoft.Compute/virtualMachines/read         | Virtuell dator         |
|Tillståndskontroll för introduktion - Läs konto      | Microsoft.Automation/automationKonto/läsa  |  Automation-konto   |
| Onboarding arbetsyta kontrollera för VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read Microsoft.OperationalInsights/workspaces/read Microsoft.OperationalInsights/workspaces/read Microsoft.         | Prenumeration         |
| Registrera logganalysleverantören |Microsoft.Insights/register/åtgärd | Prenumeration|

<sup>1</sup> Den här behörigheten behövs för att gå ombord via vm-portalupplevelsen.

### <a name="onboarding-from-automation-account"></a>Introduktion från Automation-konto

|**Åtgärd**  |**Behörighet** |**Minsta räckvidd**  |
|---------|---------|---------|
|Skapa ny distribution     | Microsoft.Resources/deployments/*        | Prenumeration         |
|Skapa ny resursgrupp     | Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.         | Prenumeration        |
|AutomationOnboarding blade - Skapa ny arbetsyta     |Microsoft.OperationalInsights/workspaces/write           | Resursgrupp        |
|AutomationOnboarding blad - läs länkad arbetsyta     | Microsoft.Automation/automationKonto/läsa        | Automation-konto       |
|AutomationOnboarding blad - läslösning     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read Microsoft.OperationalInsights/workspaces/intelligencepacks/read Microsoft.OperationalInsights/workspaces/intelligencepacks/read Microsoft.         | Lösning        |
|AutomationOnboarding blad - läs arbetsyta     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read Microsoft.OperationalInsights/workspaces/intelligencepacks/read Microsoft.OperationalInsights/workspaces/intelligencepacks/read Microsoft.        | Arbetsyta        |
|Skapa länk för arbetsyta och konto     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Skrivkonto för skokartong      | Microsoft.Automation/automationKonto/skrivning        | Konto        |
|Skapa/redigera sparad sökning     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Skapa/redigera scope config     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
| Registrera logganalysleverantören |Microsoft.Insights/register/åtgärd | Prenumeration|
|**Steg 2 - Flera virtuella datorer ombord**     |         |         |
|VMOnboarding blad - Skapa MMA förlängning     | Microsoft.Compute/virtualMachines/write Microsoft.Compute/virtualMachines/write Microsoft.Compute/virtualMachines/write Microsoft.           | Virtuell dator        |
|Skapa/redigera sparad sökning     | Microsoft.OperationalInsights/workspaces/write           | Arbetsyta        |
|Skapa/redigera scope config  | Microsoft.OperationalInsights/workspaces/write   | Arbetsyta|

## <a name="update-management"></a>Hantering av uppdateringar

Uppdateringshanteringen sträcker sig över flera tjänster för att tillhandahålla sin tjänst. I följande tabell visas de behörigheter som behövs för att hantera distributioner för uppdateringshantering:

|**Resurs**  |**Roll**  |**Omfång**  |
|---------|---------|---------|
|Automation-konto     | Log Analytics Contributor       | Automation-konto        |
|Automation-konto    | Virtuell datordeltagare        | Resursgrupp för kontot        |
|Log Analytics-arbetsyta     | Log Analytics Contributor| Log Analytics-arbetsyta        |
|Log Analytics-arbetsyta |Log Analytics Reader| Prenumeration|
|Lösning     |Log Analytics Contributor         | Lösning|
|Virtuell dator     | Virtuell datordeltagare        | Virtuell dator        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurera RBAC för ditt Automation-konto

I följande avsnitt visas hur du konfigurerar RBAC på ditt Automation-konto via [portalen](#configure-rbac-using-the-azure-portal) och [PowerShell](#configure-rbac-using-powershell).

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurera RBAC med Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com/) och öppna Automation-kontot från sidan Automation-konton.
2. Klicka på **iam-kontrollen (Access Control)** längst upp till vänster för att öppna sidan Access control (IAM). Du kan använda den här sidan för att lägga till nya användare, grupper och program för att hantera ditt Automation-konto och visa befintliga roller som kan konfigureras för Automation-kontot.
3. Klicka på fliken **Rolltilldelningar**.

   ![Knappen Åtkomst](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Lägga till en ny användare och tilldela en roll

1. På sidan Åtkomstkontroll (IAM) klickar du på **+ Lägg till rolltilldelning**. Den här åtgärden öppnar sidan Lägg till rolltilldelning där du kan lägga till en användare, grupp eller ett program och tilldela en motsvarande roll.

2. Välj en roll i listan över tillgängliga roller. Du kan välja någon av de inbyggda roller som ett Automation-konto stöder eller en anpassad roll som du kanske har definierat.

3. Skriv användarnamnet för den användare som du vill ge behörighet till i fältet **Välj.** Välj användaren i listan och klicka på **Spara**.

   ![Lägga till användare](media/automation-role-based-access-control/automation-04-add-users.png)

   Nu bör du se användaren läggas till på sidan Användare, med den valda rollen tilldelad.

   ![Visa användare](media/automation-role-based-access-control/automation-05-list-users.png)

   Du kan också tilldela en roll till användaren från sidan Roller.
4. Klicka på **Roller** från sidan Åtkomstkontroll (IAM) för att öppna sidan Roller. Du kan visa namnet på rollen och antalet användare och grupper som tilldelats den rollen.

    ![Tilldela en roll från sidan Användare](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Du kan bara ange rollbaserad åtkomstkontroll i Automation-kontots omfattning och inte på någon resurs under Automation-kontot.

#### <a name="remove-a-user"></a>Ta bort en användare

Du kan ta bort åtkomstbehörigheten för en användare som inte hanterar Automation-kontot eller som inte längre arbetar för organisationen. Nedan följer stegen för att ta bort en användare:

1. På sidan IAM (Access Control) väljer du den användare som ska tas bort och klickar på **Ta bort**.
2. Klicka på knappen **Ta bort** på sidan med tilldelningsinformation.
3. Bekräfta borttagningen genom att klicka på **Ja**.

   ![Ta bort användare](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurera RBAC med PowerShell

Du kan också konfigurera rollbaserad åtkomst till ett Automation-konto med följande [Azure PowerShell-cmdlets:](../role-based-access-control/role-assignments-powershell.md)

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) listar alla RBAC-roller som är tillgängliga i Azure Active Directory. Du kan använda den här cmdleten med parametern *Name* för att lista alla åtgärder som en viss roll kan utföra.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Följande är exempelutdata:

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

[Get-AzureRmRoleAssignment listar](/previous-versions/azure/mt619413(v=azure.100)) Azure AD RBAC-rolltilldelningar i det angivna omfånget. Utan några parametrar returnerar den här cmdleten alla rolltilldelningar som gjorts under prenumerationen. Använd parametern *ExpandPrincipalGroups* för att lista åtkomsttilldelningar för den angivna användaren, samt de grupper som användaren tillhör.

**Exempel:** Använd följande cmdlet för att lista alla användare och deras roller i ett Automation-konto.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Följande är exempelutdata:

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

Använd [New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) för att tilldela åtkomst till användare, grupper och program till ett visst scope.
    
**Exempel:** Använd följande kommando för att tilldela rollen "Automationsoperatör" för en användare i scopet för Automation-konto.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Följande är exempelutdata:

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

Använd [Ta bort AzureRmRoleAstilldelning](/previous-versions/azure/mt603781(v=azure.100)) för att ta bort åtkomsten för en angiven användare, grupp eller ett program från ett visst scope.

**Exempel:** Använd följande kommando för att ta bort användaren från rollen "Automationsoperatör" i scopet för Automation-konto.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

I föregående exempel ersätter du "inloggnings-ID för en användare som du vill ta bort", "SubscriptionID", "Resursgruppnamn" och Automation-kontonamn med dina kontouppgifter. Välj **ja** när du uppmanas att bekräfta innan du fortsätter att ta bort användarrolltilldelningar.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Användarupplevelse för automationsoperatörsroll - Automation-konto

När en användare som tilldelats rollen Automation Operator i scopet Automation-konto visar automationskontot som han/hon har tilldelats, kan användaren bara visa listan över runbooks,runbook-jobb och scheman som skapats i Automation-kontot. Den här användaren kan inte visa definitionerna av dessa objekt. Användaren kan starta, stoppa, pausa, återuppta eller schemalägga körningsjobbet. Användaren har dock inte åtkomst till andra Automation-resurser, till exempel konfigurationer, hybridarbetsgrupper eller DSC-noder.

![Ingen åtkomst till resurser](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurera RBAC för runbooks

Med Azure Automation kan du tilldela RBAC till specifika runbooks. Det gör du genom att köra följande skript för att lägga till en användare i en viss runbook. En administratör för ett automationskonto eller en klientadministratör kan köra det här skriptet.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

När skriptet har körts ska användaren logga in på Azure-portalen och visa **alla resurser**. I listan kan användaren se den runbook som han/hon har lagts till som automationskörningsoperator för.

![Runbook RBAC i portalen](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Användarupplevelse för automationsoperatörsrollen - Runbook

När en användare som tilldelats rollen Automation Operator i Runbook-scopet visar en tilldelad runbook kan användaren bara starta runbooken och visa runbook-jobben.

![Har endast tillgång till start](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar RBAC för Azure Automation finns i [hantera RBAC med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Mer information om hur du startar en runbook finns i [Starta en runbook](automation-starting-a-runbook.md).
* Information om runbook-typer finns i [Azure Automation-runbooktyper](automation-runbook-types.md).