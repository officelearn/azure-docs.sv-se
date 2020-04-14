---
title: Hantera log analytics-arbetsytor i Azure Monitor | Microsoft-dokument
description: Du kan hantera åtkomst till data som lagras på en Log Analytics-arbetsyta i Azure Monitor med hjälp av behörigheter på resurs,arbetsyta eller tabellnivå. I den här artikeln beskrivs hur du fyller i.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: b8d7f995997b828c2323b3e6934b97354c2f8c8b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255251"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Hantera åtkomst till loggdata och arbetsytor i Azure Monitor

Azure Monitor lagrar [loggdata](data-platform-logs.md) på en Log Analytics-arbetsyta. En arbetsyta är en behållare som innehåller data- och konfigurationsinformation. Om du vill hantera åtkomsten till loggdata utför du olika administrativa uppgifter som är relaterade till arbetsytan.

I den här artikeln beskrivs hur du hanterar åtkomst till loggar och administrerar de arbetsytor som innehåller dem, inklusive hur du beviljar åtkomst till: 

* Arbetsytan med arbetsytebehörigheter.
* Användare som behöver åtkomst till loggdata från specifika resurser med hjälp av Azure rollbaserad åtkomstkontroll (RBAC).
* Användare som behöver åtkomst till loggdata i en viss tabell på arbetsytan med Hjälp av Azure RBAC.

## <a name="configure-access-control-mode"></a>Konfigurera åtkomstkontrollläge

Du kan visa [åtkomstkontrollläget](design-logs-deployment.md) som konfigurerats på en arbetsyta från Azure-portalen eller med Azure PowerShell.  Du kan ändra den här inställningen med någon av följande metoder som stöds:

* Azure Portal

* Azure PowerShell

* Azure Resource Manager-mall

### <a name="from-the-azure-portal"></a>Från Azure-portalen

Du kan visa det aktuella kontrollläget för arbetsytan på sidan **Översikt** för arbetsytan på **menyn Logganalysarbetsyta.**

![Visa kontrollläge för arbetsyteåtkomst](media/manage-access/view-access-control-mode.png)

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
1. I Azure-portalen väljer du Log Analytics-arbetsytor > arbetsytan.

Du kan ändra den här inställningen från sidan **Egenskaper** på arbetsytan. Om du ändrar inställningen inaktiveras du om du inte har behörighet att konfigurera arbetsytan.

![Ändra åtkomstläge för arbetsyta](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Använda PowerShell

Använd följande kommando för att undersöka åtkomstkontrollläget för alla arbetsytor i prenumerationen:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

Utdata bör likna följande:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Värdet innebär `False` att arbetsytan är konfigurerad med åtkomstläget för arbetsytans kontext.  Värdet innebär `True` att arbetsytan är konfigurerad med åtkomstläget för resurskontext.

> [!NOTE]
> Om en arbetsyta returneras utan ett booleskt värde och är `False` tom matchar detta även resultatet av ett värde.
>

Använd följande skript för att ange åtkomstkontrollläge för en viss arbetsyta till resurskontextbehörighet:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Använd följande skript för att ange åtkomstkontrollläge för alla arbetsytor i prenumerationen till resurskontextbehörigheten:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Använda en Resource Manager-mall

Om du vill konfigurera åtkomstläget i en Azure Resource Manager-mall anger du flaggan **enableLogAccessUsingOnlyResourcePermissionss** på arbetsytan till ett av följande värden.

* **false**: Ange arbetsytan till arbetsytekontextbehörigheter. Det här är standardinställningen om flaggan inte är inställd.
* **sant**: Ange arbetsytan till resurskontextbehörigheter.

## <a name="manage-access-using-workspace-permissions"></a>Hantera åtkomst med hjälp av arbetsytebehörigheter

Varje arbetsyta kan ha flera konton kopplade till sig och varje konto kan ha åtkomst till flera arbetsytor. Åtkomst hanteras med hjälp av [Azure-rollbaserad åtkomst](../../role-based-access-control/role-assignments-portal.md).

Följande aktiviteter kräver även Azure-behörigheter:

|Åtgärd |Azure-behörigheter krävs |Anteckningar |
|-------|-------------------------|------|
| Lägga till och ta bort övervakningslösningar | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Behörigheterna måste beviljas på resursgrupp- eller prenumerationsnivå. |
| Ändra prisnivån | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visa data i lösningspanelerna *säkerhetskopiering* och *Site Recovery* | Administratör/medadministratör | Åtkomst till resurser som distribueras med den klassiska distributionsmodellen |
| Skapa en arbetsyta i Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Visa grundläggande egenskaper för arbetsytan och ange arbetsytans blad i portalen | `Microsoft.OperationalInsights/workspaces/read` ||
| Frågeloggar med vilket gränssnitt som helst | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Komma åt alla loggtyper med frågor | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Komma åt en specifik loggtabell | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Läs arbetsytans nycklar för att tillåta att skicka loggar till den här arbetsytan | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Hantera åtkomst med Azure-behörigheter

Om du vill bevilja åtkomst till Log Analytics-arbetsytan med Azure-behörigheter följer du stegen i [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../../role-based-access-control/role-assignments-portal.md). Till exempel anpassade roller, se [Exempel på anpassade roller](#custom-role-examples)

Azure har två inbyggda användarroller för Log Analytics-arbetsytor:

* Log Analytics Reader
* Log Analytics Contributor

Medlemmar av *Log Analytics Reader*-rollen kan:

* Visa och söka i alla övervakningsdata
* Visa övervakningsinställningar, även konfiguration av Azure Diagnostics för alla Azure-resurser.

Rollen Log Analytics Reader innehåller följande Azure-åtgärder:

| Typ    | Behörighet | Beskrivning |
| ------- | ---------- | ----------- |
| Åtgärd | `*/read`   | Möjlighet att visa alla Azure-resurser och resurskonfiguration. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure Diagnostics för resurser <br> Alla egenskaper och inställningar för alla resurser. <br> För arbetsytor tillåter det fullständiga obegränsade behörigheter för att läsa arbetsytans inställningar och utföra frågor på data. Se fler detaljerade alternativ ovan. |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Föråldrad, inget behov av att tilldela dem till användare. |
| Åtgärd | `Microsoft.OperationalInsights/workspaces/search/action` | Föråldrad, inget behov av att tilldela dem till användare. |
| Åtgärd | `Microsoft.Support/*` | Kan öppna supportärenden |
|Ingen åtgärd | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Förhindrar läsning av arbetsytenyckel som krävs för att använda DATAINSAMLINGS-API:et och för att installera agenter. Detta hindrar användaren från att lägga till nya resurser på arbetsytan |

Medlemmar av *Log Analytics Contributor*-rollen kan:

* Innehåller alla privilegier för *log Analytics Reader-rollen*, så att användaren kan läsa alla övervakningsdata
* Skapa och konfigurera Automation-konton
* Lägga till och ta bort hanteringslösningar

    > [!NOTE]
    > För att kunna utföra de två sista åtgärderna måste den här behörigheten beviljas på resursgrupps- eller prenumerationsnivå.

* Läs lagringskontonycklar
* Konfigurera insamlingen av loggar från Azure Storage
* Redigera övervakningsinställningar för Azure-resurser, bland annat
  * Lägga till tillägg för virtuell dator i virtuella datorer
  * Konfigurera Azure Diagnostics på alla Azure-resurser

> [!NOTE]
> Du kan använda möjligheten att lägga till ett virtuellt datortillägg i en virtuell dator för att få fullständig kontroll över datorn.

Rollen Log Analytics Contributor innehåller följande Azure-åtgärder:

| Behörighet | Beskrivning |
| ---------- | ----------- |
| `*/read`     | Kan visa alla resurser och resurskonfigurationer. Detta omfattar visning av: <br> Status för tillägg för virtuell dator <br> Konfiguration av Azure Diagnostics för resurser <br> Alla egenskaper och inställningar för alla resurser. <br> För arbetsytor tillåter det fullständiga obegränsade behörigheter för att läsa arbetsytans inställning och utföra frågor på data. Se fler detaljerade alternativ ovan. |
| `Microsoft.Automation/automationAccounts/*` | Kan skapa och konfigurera Azure Automation-konton, inklusive lägga till och redigera runbookflöden |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Lägga till, uppdatera och ta bort virtuella datortillägg, även Microsoft Monitoring Agent-tillägget och OMS Agent for Linux-tillägget |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Visa lagringskontonyckeln. Krävs för att ställa in Log Analytics för läsning av loggar från Azure-lagringskonton |
| `Microsoft.Insights/alertRules/*` | Lägga till, uppdatera och ta bort aviseringsregler |
| `Microsoft.Insights/diagnosticSettings/*` | Lägga till, uppdatera och ta bort diagnostikinställningar på Azure-resurser |
| `Microsoft.OperationalInsights/*` | Lägg till, uppdatera och ta bort konfiguration för Log Analytics-arbetsytor. Om du vill redigera avancerade `Microsoft.OperationalInsights/workspaces/write`inställningar för arbetsytan behöver användaren . |
| `Microsoft.OperationsManagement/*` | Lägga till och ta bort hanteringslösningar |
| `Microsoft.Resources/deployments/*` | Skapa och ta bort distributioner. Krävs för att lägga till och ta bort lösningar, arbetsytor och Automation-konton |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Skapa och ta bort distributioner. Krävs för att lägga till och ta bort lösningar, arbetsytor och Automation-konton |

För att kunna lägga till och ta bort användare i en användarroll måste du ha behörigheten `Microsoft.Authorization/*/Delete` och `Microsoft.Authorization/*/Write`.

Använd de här rollerna för att ge användare åtkomst med olika omfång:

* Prenumeration: åtkomst till alla arbetsytor i prenumerationen
* Resursgrupp: åtkomst till alla arbetsytor i resursgruppen
* Resurs: endast åtkomst till en angiven arbetsyta

Vi rekommenderar att du utför tilldelningar på resursnivå (arbetsyta) för att säkerställa korrekt åtkomstkontroll. Använd [anpassade roller](../../role-based-access-control/custom-roles.md) för att skapa roller med specifik behörighet.

### <a name="resource-permissions"></a>Resursbehörigheter

När användare frågar loggar från en arbetsyta med hjälp av resurskontextåtkomst har de följande behörigheter för resursen:

| Behörighet | Beskrivning |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exempel:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Möjlighet att visa alla loggdata för resursen.  |
| `Microsoft.Insights/diagnosticSettings/write` | Möjlighet att konfigurera diagnostikinställningen för att tillåta inställning av loggar för den här resursen. |

`/read`behörighet beviljas vanligtvis från en roll som _\*_ innehåller _ \*/read eller_ behörigheter som de inbyggda [rollerna Läsare](../../role-based-access-control/built-in-roles.md#reader) och [Deltagare.](../../role-based-access-control/built-in-roles.md#contributor) Anpassade roller som innehåller specifika åtgärder eller dedikerade inbyggda roller kanske inte innehåller den här behörigheten.

Se [Definiera åtkomstkontroll per tabell](#table-level-rbac) nedan om du vill skapa olika åtkomstkontroll för olika tabeller.

## <a name="custom-role-examples"></a>Exempel på anpassad roll

1. Så här ger du en användare åtkomst till loggdata från sina resurser:

    * Konfigurera kontrollläget för arbetsyteåtkomstkontroll för att **använda arbetsytan eller resursbehörigheterna**

    * Bevilja `*/read` användare `Microsoft.Insights/logs/*/read` eller behörigheter till deras resurser. Om de redan har tilldelats rollen [Log Analytics Reader](../../role-based-access-control/built-in-roles.md#reader) på arbetsytan är det tillräckligt.

2. Om du vill ge en användare åtkomst till loggdata från sina resurser och konfigurera deras resurser för att skicka loggar till arbetsytan gör du följande:

    * Konfigurera kontrollläget för arbetsyteåtkomstkontroll för att **använda arbetsytan eller resursbehörigheterna**

    * Ge användarna följande behörigheter på `Microsoft.OperationalInsights/workspaces/read` arbetsytan: och `Microsoft.OperationalInsights/workspaces/sharedKeys/action`. Med dessa behörigheter kan användare inte utföra några frågor på arbetsytasnivå. De kan bara räkna upp arbetsytan och använda den som mål för diagnostikinställningar eller agentkonfiguration.

    * Ge användarna följande behörigheter till `Microsoft.Insights/logs/*/read` `Microsoft.Insights/diagnosticSettings/write`sina resurser: och . Om de redan har tilldelats rollen [Log Analytics Contributor,](../../role-based-access-control/built-in-roles.md#contributor) `*/read` tilldelat rollen Läsare eller beviljade behörigheter för den här resursen är det tillräckligt.

3. Om du vill ge en användare åtkomst till loggdata från sina resurser utan att kunna läsa säkerhetshändelser och skicka data utför du följande:

    * Konfigurera kontrollläget för arbetsyteåtkomstkontroll för att **använda arbetsytan eller resursbehörigheterna**

    * Ge användarna följande behörigheter till `Microsoft.Insights/logs/*/read`sina resurser: .

    * Lägg till följande NonAction för att blockera användare `Microsoft.Insights/logs/SecurityEvent/read`från att läsa typen SecurityEvent: . NonAction ska ha samma anpassade roll som den åtgärd`Microsoft.Insights/logs/*/read`som ger läsbehörighet ( ). Om användaren ingår den läsåtgärd från en annan roll som tilldelas den här resursen eller prenumerationen eller resursgruppen kan han eller hon läsa alla loggtyper. Detta gäller även om `*/read`de ärver , som finns till exempel med rollen Läsare eller Deltagare.

4. Om du vill ge en användare åtkomst till loggdata från sina resurser och läsa alla Azure AD-inloggnings- och läsa lösningsloggdata för uppdateringshantering från arbetsytan gör du följande:

    * Konfigurera kontrollläget för arbetsyteåtkomstkontroll för att **använda arbetsytan eller resursbehörigheterna**

    * Ge användarna följande behörigheter på arbetsytan: 

        * `Microsoft.OperationalInsights/workspaces/read`– krävs så att användningen kan räkna upp arbetsytan och öppna arbetsytan blad i Azure-portalen
        * `Microsoft.OperationalInsights/workspaces/query/read`– krävs för varje användare som kan köra frågor
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`– för att kunna läsa Azure AD-inloggningsloggar
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`– för att kunna läsa lösningsloggar för Uppdateringshantering
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`– för att kunna läsa lösningsloggar för Uppdateringshantering
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`– för att kunna läsa uppdateringshanteringsloggar
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`– krävs för att kunna använda Lösning för uppdateringshantering
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`– krävs för att kunna använda Lösning för uppdateringshantering

    * Ge användarna följande behörigheter till `*/read`sina resurser: , tilldelad rollen Läsare eller `Microsoft.Insights/logs/*/read`. 

## <a name="table-level-rbac"></a>RBAC på tabellnivå

**Med RBAC på tabellnivå** kan du definiera mer detaljerad kontroll till data på en Log Analytics-arbetsyta utöver de andra behörigheterna. Med den här kontrollen kan du definiera specifika datatyper som endast är tillgängliga för en viss uppsättning användare.

Du implementerar tabellåtkomstkontroll med [anpassade Azure-roller](../../role-based-access-control/custom-roles.md) för att antingen bevilja åtkomst till specifika [tabeller](../log-query/logs-structure.md) på arbetsytan. Dessa roller tillämpas på arbetsytor med antingen arbetsytekontext eller [åtkomstkontrollläge för](design-logs-deployment.md#access-control-mode) resurskontext oavsett användarens [åtkomstläge](design-logs-deployment.md#access-mode).

Skapa en [anpassad roll](../../role-based-access-control/custom-roles.md) med följande åtgärder för att definiera åtkomst till registeråtkomstkontroll.

* Om du vill bevilja åtkomst till en tabell tar du med den i avsnittet **Åtgärder** i rolldefinitionen. Om du vill subtrahera åtkomst från tillåtna **åtgärder**tar du med den i avsnittet **NotActions.**
* Använd Microsoft.OperationalInsights/workspaces/query/* för att ange alla tabeller.

Om du till exempel vill skapa en roll med åtkomst till tabellerna _Heartbeat_ och _AzureActivity_ skapar du en anpassad roll med hjälp av följande åtgärder:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Om du vill skapa en roll med åtkomst till endast _tabellen SecurityBaseline_ skapar du en anpassad roll med hjälp av följande åtgärder:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>Anpassade loggar

 Anpassade loggar skapas från datakällor som anpassade loggar och HTTP Data Collector API. Det enklaste sättet att identifiera typen av logg är genom att kontrollera tabellerna som anges under [Anpassade loggar i loggschemat](../log-query/get-started-portal.md#understand-the-schema).

 Du kan inte bevilja åtkomst till enskilda anpassade loggar, men du kan bevilja åtkomst till alla anpassade loggar. Skapa en anpassad roll med hjälp av följande åtgärder om du vill skapa en roll med hjälp av följande:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
En alternativ metod för att hantera åtkomst till anpassade loggar är att tilldela dem till en Azure-resurs och hantera åtkomst med hjälp av resurskontextparadigmet. Om du vill använda den här metoden måste du inkludera resurs-ID:t genom att ange det i [x-ms-AzureResourceId-huvudet](data-collector-api.md#request-headers) när data används för att logga Analytics via [HTTP Data Collector API](data-collector-api.md). Resurs-ID:t måste vara giltigt och ha åtkomstregler tillämpade på det. När loggarna har förtärs är de tillgängliga för dem med läsåtkomst till resursen, som förklaras här.

Ibland kommer anpassade loggar från källor som inte är direkt kopplade till en viss resurs. Skapa i så fall en resursgrupp bara för att hantera åtkomsten till dessa loggar. Resursgruppen medför ingen kostnad, men ger dig ett giltigt resurs-ID för att styra åtkomsten till de anpassade loggarna. Om en viss brandvägg till exempel skickar anpassade loggar skapar du en resursgrupp med namnet "MyFireWallLogs" och ser till att API-begäranden innehåller resurs-ID:t för "MyFireWallLogs". Brandväggsloggposterna är sedan endast tillgängliga för användare som har beviljats åtkomst till antingen MyFireWallLogs eller de med fullständig åtkomst till arbetsytan.          

### <a name="considerations"></a>Överväganden

* Om en användare beviljas global läsbehörighet med standardrollerna Läsare eller Deltagare som innehåller _ \*/read-åtgärden_ åsidosätter den åtkomstkontrollen per tabell och ger dem åtkomst till alla loggdata.
* Om en användare beviljas åtkomst per tabell men inga andra behörigheter kan de komma åt loggdata från API:et men inte från Azure-portalen. Om du vill ge åtkomst från Azure-portalen använder du Log Analytics Reader som basroll.
* Administratörer av prenumerationen har åtkomst till alla datatyper oavsett andra behörighetsinställningar.
* Arbetsplatsägare behandlas som alla andra användare för åtkomstkontroll per tabell.
* Vi rekommenderar att du tilldelar roller till säkerhetsgrupper i stället för enskilda användare för att minska antalet tilldelningar. Detta hjälper dig också att använda befintliga verktyg för grupphantering för att konfigurera och verifiera åtkomst.

## <a name="next-steps"></a>Nästa steg

* Se [Log Analytics-agentöversikt](../../azure-monitor/platform/log-analytics-agent.md) för att samla in data från datorer i ditt datacenter eller annan molnmiljö.

* Se [Samla in data om virtuella Azure-datorer](../../azure-monitor/learn/quick-collect-azurevm.md) för att konfigurera datainsamling från virtuella Azure-datorer.
