---
title: Hantera Log Analytics arbets ytor i Azure Monitor | Microsoft Docs
description: Du kan hantera åtkomst till data som lagras i en Log Analytics arbets yta i Azure Monitor med hjälp av behörigheter för resurs, arbets yta eller tabell nivå. Den här artikeln beskriver hur du slutför.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/22/2019
ms.openlocfilehash: 3a75efc8c73c96bfff0ba94ca3e9753ea536fd53
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289126"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Hantera åtkomst till logg data och arbets ytor i Azure Monitor

Azure Monitor lagrar [loggdata](data-platform-logs.md) i en Log Analytics arbets yta. En arbets yta är en behållare som innehåller data och konfigurations information. Om du vill hantera åtkomst till loggdata utför du olika administrativa uppgifter relaterade till din arbets yta.

Den här artikeln beskriver hur du hanterar åtkomst till loggar och administrerar de arbets ytor som innehåller dem, inklusive hur du beviljar åtkomst till: 

* Arbets ytan med hjälp av arbets ytans behörigheter.
* Användare som behöver åtkomst till loggdata från vissa resurser med hjälp av rollbaserad åtkomst kontroll (RBAC) i Azure.
* Användare som behöver åtkomst till loggdata i en speciell tabell i arbets ytan med hjälp av Azure RBAC.

## <a name="configure-access-control-mode"></a>Konfigurera åtkomst kontrol läge

Du kan visa åtkomst kontrol läget som kon figurer ATS på en arbets yta från Azure Portal eller med Azure PowerShell.  Du kan ändra den här inställningen med någon av följande metoder som stöds:

* Azure portal

* Azure PowerShell

* Azure Resource Manager-mall

### <a name="from-the-azure-portal"></a>Från Azure-portalen

Du kan visa den aktuella arbets ytans åtkomst kontrol läge på sidan **Översikt** för arbets ytan på menyn **Log Analytics arbets yta** .

![Visa åtkomst kontrol läge för arbets yta](media/manage-access/view-access-control-mode.png)

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
1. I Azure Portal väljer du Log Analytics arbets ytor > din arbets yta.

Du kan ändra den här inställningen från sidan **Egenskaper** i arbets ytan. Att ändra inställningen kommer att inaktive ras om du inte har behörighet att konfigurera arbets ytan.

![Ändra arbets ytans åtkomst läge](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Använda PowerShell

Använd följande kommando för att kontrol lera åtkomst kontrol läget för alla arbets ytor i prenumerationen:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

Utdata bör likna följande:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Värdet `False` innebär att arbets ytan konfigureras med arbets ytans kontext åtkomst läge.  Värdet `True` innebär att arbets ytan konfigureras med resurs kontextens åtkomst läge.

> [!NOTE]
> Om en arbets yta returneras utan ett booleskt värde och är tom, matchar detta även resultatet av ett `False` värde.
>

Använd följande skript för att ange åtkomst kontrol läget för en speciell arbets yta till resurs kontext behörighet:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Använd följande skript för att ställa in åtkomst kontrol läget för alla arbets ytor i prenumerationen på resurs kontextens behörighet:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="using-a-resource-manager-template"></a>Använda en Resource Manager-mall

Om du vill konfigurera åtkomst Läget i en Azure Resource Manager mall ställer du in funktions flaggan **enableLogAccessUsingOnlyResourcePermissions** på arbets ytan till något av följande värden.

* **false**: Ange arbets ytan till arbets ytans kontext behörigheter. Detta är standardinställningen om flaggan inte har angetts.
* **True**: Ange arbets ytan till resurs kontext behörigheter.

## <a name="manage-access-using-workspace-permissions"></a>Hantera åtkomst med hjälp av arbets ytans behörigheter

Varje arbetsyta kan ha flera associerade konton, och varje konto kan ha åtkomst till flera arbetsytor. Åtkomst hanteras med hjälp av [rollbaserad åtkomst i Azure](../../role-based-access-control/role-assignments-portal.md).

Följande aktiviteter kräver även Azure-behörigheter:

|Åtgärd |Azure-behörigheter krävs |Anteckningar |
|-------|-------------------------|------|
| Lägga till och ta bort övervaknings lösningar | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Behörigheterna måste beviljas på resursgrupp- eller prenumerationsnivå. |
| Ändra prisnivån | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visa data i lösningspanelerna *säkerhetskopiering* och *Site Recovery* | Administratör/medadministratör | Åtkomst till resurser som distribueras med den klassiska distributionsmodellen |
| Skapa en arbetsyta i Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Visa grundläggande egenskaper för arbets yta och ange bladet arbets yta i portalen | `Microsoft.OperationalInsights/workspaces/read` ||
| Fråga loggar med valfritt gränssnitt | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Åtkomst till alla logg typer med hjälp av frågor | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Åtkomst till en speciell logg tabell | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Läs arbets ytans nycklar för att tillåta sändning av loggar till den här arbets ytan | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Hantera åtkomst med Azure-behörigheter

Om du vill bevilja åtkomst till Log Analytics-arbetsytan med Azure-behörigheter följer du stegen i [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../../role-based-access-control/role-assignments-portal.md). Till exempel anpassade roller, se [exempel på anpassade roller](#custom-role-examples)

Azure har två inbyggda användar roller för Log Analytics arbets ytor:

* Log Analytics Reader
* Log Analytics Contributor

Medlemmar av *Log Analytics Reader*-rollen kan:

* Visa och söka i alla övervakningsdata
* Visa övervakningsinställningar, även konfiguration av Azure Diagnostics för alla Azure-resurser.

Läsarroll för Log Analytics innehåller följande Azure åtgärder:

| Typ    | Behörighet | Beskrivning |
| ------- | ---------- | ----------- |
| Åtgärd | `*/read`   | Möjlighet att visa alla Azure-resurser och resurskonfigurationen. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure Diagnostics för resurser <br> Alla egenskaper och inställningar för alla resurser. <br> För arbets ytor ger den fullständig obegränsad behörighet att läsa inställningarna för arbets ytan och köra frågor på data. Se fler detaljerade alternativ ovan. |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Föråldrad, du behöver inte tilldela dem till användarna. |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/search/action` | Föråldrad, du behöver inte tilldela dem till användarna. |
| Åtgärd | `Microsoft.Support/*` | Kan öppna supportärenden |
|Ingen åtgärd | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Förhindrar läsning av arbetsytenyckeln nyckel som krävs för att använda datasamlings-API och installera agenter. Detta hindrar användaren från att lägga till nya resurser till arbetsytan |

Medlemmar av *Log Analytics Contributor*-rollen kan:

* Innehåller alla behörigheter för *rollen Log Analytics läsare*, så att användaren kan läsa alla övervaknings data
* Skapa och konfigurera Automation-konton
* Lägga till och ta bort hanteringslösningar

    > [!NOTE]
    > För att kunna genomföra de sista två åtgärderna, måste den här behörigheten beviljas resource group eller på prenumerationsnivån.

* Läs lagrings konto nycklar
* Konfigurera samling av loggar från Azure Storage
* Redigera övervakningsinställningar för Azure-resurser, bland annat
  * Lägga till tillägg för virtuell dator i virtuella datorer
  * Konfigurera Azure Diagnostics på alla Azure-resurser

> [!NOTE]
> Du kan använda möjligheten att lägga till ett virtuellt datortillägg i en virtuell dator för att få fullständig kontroll över datorn.

Rollen Log Analytics Contributor innehåller följande Azure åtgärder:

| Behörighet | Beskrivning |
| ---------- | ----------- |
| `*/read`     | Kan visa alla resurser och resurskonfigurationer. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure Diagnostics för resurser <br> Alla egenskaper och inställningar för alla resurser. <br> För arbets ytor ger den fullständig obegränsad behörighet att läsa inställningen arbets yta och köra fråga på data. Se fler detaljerade alternativ ovan. |
| `Microsoft.Automation/automationAccounts/*` | Kan skapa och konfigurera Azure Automation-konton, inklusive lägga till och redigera runbookflöden |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Lägga till, uppdatera och ta bort virtuella datortillägg, även Microsoft Monitoring Agent-tillägget och OMS Agent for Linux-tillägget |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Visa lagringskontonyckeln. Krävs för att ställa in Log Analytics för läsning av loggar från Azure-lagringskonton |
| `Microsoft.Insights/alertRules/*` | Lägga till, uppdatera och ta bort aviseringsregler |
| `Microsoft.Insights/diagnosticSettings/*` | Lägga till, uppdatera och ta bort diagnostikinställningar på Azure-resurser |
| `Microsoft.OperationalInsights/*` | Lägg till, uppdatera och ta bort konfiguration för Log Analytics arbets ytor. Användaren behöver `Microsoft.OperationalInsights/workspaces/write`för att redigera avancerade inställningar för arbets ytan. |
| `Microsoft.OperationsManagement/*` | Lägga till och ta bort hanteringslösningar |
| `Microsoft.Resources/deployments/*` | Skapa och ta bort distributioner. Krävs för att lägga till och ta bort lösningar, arbetsytor och Automation-konton |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Skapa och ta bort distributioner. Krävs för att lägga till och ta bort lösningar, arbetsytor och Automation-konton |

För att kunna lägga till och ta bort användare i en användarroll måste du ha behörigheten `Microsoft.Authorization/*/Delete` och `Microsoft.Authorization/*/Write`.

Använd de här rollerna för att ge användare åtkomst med olika omfång:

* Prenumeration: åtkomst till alla arbetsytor i prenumerationen
* Resursgrupp: åtkomst till alla arbetsytor i resursgruppen
* Resurs: endast åtkomst till en angiven arbetsyta

Vi rekommenderar att du utför tilldelningar på resurs nivå (arbets yta) för att säkerställa en korrekt åtkomst kontroll. Använd [anpassade roller](../../role-based-access-control/custom-roles.md) för att skapa roller med specifik behörighet.

### <a name="resource-permissions"></a>Resurs behörigheter

När användarna frågar efter loggar från en arbets yta med resurs kontext åtkomst har de följande behörigheter för resursen:

| Behörighet | Beskrivning |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exempel:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Möjlighet att visa alla logg data för resursen.  |
| `Microsoft.Insights/diagnosticSettings/write` | Möjlighet att konfigurera diagnostikinställningar för att tillåta konfiguration av loggar för den här resursen. |

`/read` behörighet beviljas vanligt vis från en roll som innehåller _\*/Read eller_ _\*_ behörigheter som de inbyggda rollerna [läsare](../../role-based-access-control/built-in-roles.md#reader) och [deltagare](../../role-based-access-control/built-in-roles.md#contributor) . Anpassade roller som innehåller särskilda åtgärder eller dedikerade inbyggda roller kanske inte innehåller den här behörigheten.

Se [definiera åtkomst kontroll per tabell](#table-level-rbac) nedan om du vill skapa en annan åtkomst kontroll för olika tabeller.

## <a name="custom-role-examples"></a>Exempel på anpassade roller

1. Gör följande för att ge en användare åtkomst till loggdata från sina resurser:

    * Konfigurera arbets ytans åtkomst kontrol läge för att **använda arbets ytan eller resurs behörigheter**

    * Bevilja användare `*/read` eller `Microsoft.Insights/logs/*/read` behörigheter till sina resurser. Om de redan har tilldelats rollen [Log Analytics läsare](../../role-based-access-control/built-in-roles.md#reader) på arbets ytan räcker det.

2. Om du vill ge en användare åtkomst till loggdata från sina resurser och konfigurera deras resurser för att skicka loggar till arbets ytan gör du följande:

    * Konfigurera arbets ytans åtkomst kontrol läge för att **använda arbets ytan eller resurs behörigheter**

    * Ge användare följande behörigheter på arbets ytan: `Microsoft.OperationalInsights/workspaces/read` och `Microsoft.OperationalInsights/workspaces/sharedKeys/action`. Med dessa behörigheter kan användarna inte utföra några frågor på arbets ytans nivå. De kan bara räkna upp arbets ytan och använda den som mål för diagnostikinställningar eller agent konfiguration.

    * Ge användarna följande behörigheter för sina resurser: `Microsoft.Insights/logs/*/read` och `Microsoft.Insights/diagnosticSettings/write`. Om de redan har tilldelats rollen [Log Analytics Contributor](../../role-based-access-control/built-in-roles.md#contributor) , tilldelas rollen läsare eller beviljats `*/read` behörigheter för den här resursen, räcker det.

3. Om du vill ge en användare åtkomst till loggdata från sina resurser utan att kunna läsa säkerhets händelser och skicka data, gör du följande:

    * Konfigurera arbets ytans åtkomst kontrol läge för att **använda arbets ytan eller resurs behörigheter**

    * Ge användarna följande behörigheter för sina resurser: `Microsoft.Insights/logs/*/read`.

    * Lägg till följande ej åtgärd för att blockera användare från att läsa SecurityEvent-typen: `Microsoft.Insights/logs/SecurityEvent/read`. Den andra åtgärden skall vara i samma anpassade roll som den åtgärd som ger Läs behörighet (`Microsoft.Insights/logs/*/read`). Om användaren har tilldelat Läs åtgärden från en annan roll som är tilldelad till den här resursen eller till prenumerationen eller resurs gruppen kan de läsa alla typer av loggar. Detta gäller även om de ärver `*/read`, som finns till exempel med rollen läsare eller deltagare.

4. Om du vill ge en användare åtkomst till loggdata från sina resurser och läsa alla Azure AD-inloggningar och läsa Uppdateringshantering lösnings logg data från arbets ytan gör du följande:

    * Konfigurera arbets ytans åtkomst kontrol läge för att **använda arbets ytan eller resurs behörigheter**

    * Ge användare följande behörigheter på arbets ytan: 

        * `Microsoft.OperationalInsights/workspaces/read` – krävs för att användningen ska kunna räkna upp arbets ytan och öppna bladet arbets yta i Azure Portal
        * `Microsoft.OperationalInsights/workspaces/query/read` – krävs för alla användare som kan köra frågor
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` – för att kunna läsa inloggnings loggar för Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` – för att kunna läsa Uppdateringshantering lösnings loggar
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` – för att kunna läsa Uppdateringshantering lösnings loggar
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` – för att kunna läsa uppdaterings hanterings loggar
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` – krävs för att kunna använda Uppdateringshantering-lösning
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` – krävs för att kunna använda Uppdateringshantering-lösning

    * Ge användarna följande behörigheter till resurserna: `*/read`, tilldelas rollen läsare eller `Microsoft.Insights/logs/*/read`. 

## <a name="table-level-rbac"></a>RBAC för tabell nivå

Med **RBAC-tabellnivå** kan du definiera mer detaljerad kontroll för data i en Log Analytics arbets yta förutom de andra behörigheterna. Med den här kontrollen kan du definiera vissa data typer som endast är tillgängliga för en speciell uppsättning användare.

Du implementerar en tabell åtkomst kontroll med [Azures anpassade roller](../../role-based-access-control/custom-roles.md) för att bevilja åtkomst till vissa [tabeller](../log-query/logs-structure.md) i arbets ytan. Dessa roller tillämpas på arbets ytor med antingen arbets ytans kontext eller resurs kontextens [åtkomst kontroll lägen](design-logs-deployment.md#access-control-mode) oavsett användarens [åtkomst läge](design-logs-deployment.md#access-mode).

Skapa en [anpassad roll](../../role-based-access-control/custom-roles.md) med följande åtgärder för att definiera åtkomst till tabell åtkomst kontroll.

* Om du vill bevilja åtkomst till en tabell inkluderar du den i avsnittet **åtgärder** i roll definitionen. Ta bort åtkomsten från de tillåtna **åtgärderna**genom att ta med den i **NotActions** -avsnittet.
* Använd Microsoft. OperationalInsights/arbets ytor/fråga/* för att ange alla tabeller.

Om du till exempel vill skapa en roll med åtkomst till _pulsslags_ -och _AzureActivity_ -tabellerna skapar du en anpassad roll med följande åtgärder:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Om du vill skapa en roll med enbart åtkomst till tabellen _SecurityBaseline_ skapar du en anpassad roll med följande åtgärder:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>Anpassade loggar

 Anpassade loggar skapas från data källor som anpassade loggar och API för HTTP-datainsamling. Det enklaste sättet att identifiera logg typen är genom att kontrol lera tabellerna som visas under [anpassade loggar i logg schemat](../log-query/get-started-portal.md#understand-the-schema).

 Du kan för närvarande inte bevilja åtkomst till enskilda anpassade loggar, men du kan bevilja åtkomst till alla anpassade loggar. Skapa en roll med åtkomst till alla anpassade loggar genom att skapa en anpassad roll med följande åtgärder:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```

### <a name="considerations"></a>Överväganden

* Om en användare beviljas global Läs behörighet med standard läsare eller deltagar roller som innehåller åtgärden _\*/Read_ , åsidosätter den åtkomst kontrollen per tabell och ger dem åtkomst till alla loggdata.
* Om en användare beviljas åtkomst per tabell men inga andra behörigheter, skulle de kunna komma åt loggdata från API: et, men inte från Azure Portal. Om du vill ge åtkomst från Azure Portal använder du Log Analytics Reader som bas roll.
* Administratörer av prenumerationen kommer att ha åtkomst till alla data typer, oavsett andra behörighets inställningar.
* Arbets ytans ägare behandlas som alla andra användare för åtkomst kontroll per tabell.
* Vi rekommenderar att du tilldelar roller till säkerhets grupper i stället för enskilda användare för att minska antalet tilldelningar. Detta hjälper dig också att använda befintliga grupp hanterings verktyg för att konfigurera och verifiera åtkomst.

## <a name="next-steps"></a>Nästa steg

* Se [översikt över Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md) samla in data från datorer i ditt datacenter eller andra moln.

* Se [samla in data om virtuella Azure-datorer](../../azure-monitor/learn/quick-collect-azurevm.md) för att konfigurera data insamling från virtuella Azure-datorer.
