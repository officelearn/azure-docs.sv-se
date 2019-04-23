---
title: Visa aktivitetsloggar för RBAC ändras till Azure-resurser | Microsoft Docs
description: Visa aktivitetsloggar för rollbaserad åtkomstkontroll (RBAC) ändringar till Azure-resurser för de senaste 90 dagarna.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b808654baded5bbe721866441a8d1115eff7bcaa
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997911"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Visa aktivitetsloggar för RBAC ändringar till Azure-resurser

Ibland behöver du information om rollbaserad åtkomstkontroll (RBAC) ändringar till Azure-resurser, till exempel för granskning eller felsökning. Varje gång någon gör ändringar i rolltilldelningar eller rolldefinitioner dina prenumerationer, ändringarna loggas [Azure-aktivitetsloggen](../azure-monitor/platform/activity-logs-overview.md). Du kan visa aktivitetsloggar för att se alla ändringar som RBAC för de senaste 90 dagarna.

## <a name="operations-that-are-logged"></a>Åtgärder som är inloggad

Här följer de RBAC-relaterade åtgärder som ska loggas i aktivitetsloggen för:

- Skapa rolltilldelning
- Ta bort rolltilldelning
- Skapa eller uppdatera anpassad rolldefinition
- Ta bort anpassad rolldefinition

## <a name="azure-portal"></a>Azure Portal

Det enklaste sättet att komma igång är att visa aktivitetsloggar med Azure-portalen. Följande skärmbild visar ett exempel på en aktivitetslogg som har filtrerats för att visa rolltilldelning och åtgärder för definition av rollen. Den innehåller också en länk för att hämta loggarna som en CSV-fil.

![Aktivitetsloggar med hjälp av portalen – skärmbild](./media/change-history-report/activity-log-portal.png)

Aktivitetsloggen i portalen har flera filter. Här följer de RBAC-relaterade filter:

|Filter  |Value  |
|---------|---------|
|Händelsekategori     | <ul><li>Administrativ</li></ul>         |
|Åtgärd     | <ul><li>Skapa rolltilldelning</li> <li>Ta bort rolltilldelning</li> <li>Skapa eller uppdatera anpassad rolldefinition</li> <li>Ta bort anpassad rolldefinition</li></ul>      |


Läs mer om aktivitetsloggar [visa händelser i aktivitetsloggen](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Du kan visa aktivitetsloggar med Azure PowerShell på [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) kommando.

Det här kommandot visar alla tilldelningsändringarna för rollen i en prenumeration under de senaste sju dagarna:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Det här kommandot visar alla rollen definitionsändringar i en resursgrupp för de senaste sju dagarna:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Det här kommandot visar en lista över alla rolltilldelning och rollen definitionsändringar i en prenumeration under de senaste sju dagarna och visar resultatet i en lista:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Du kan visa aktivitetsloggar med Azure CLI i [az monitor-aktivitetsloggen lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) kommando.

Det här kommandot visar aktivitetsloggar i en resursgrupp sedan starttid:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Det här kommandot visar aktivitetsloggar för resursprovidern auktorisering sedan starttid:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) är ett annat verktyg som du kan använda för att samla in och analysera RBAC ändringar för alla dina Azure-resurser. Azure Monitor-loggar har följande fördelar:

- Skriva komplexa frågor och logik
- Integrera med aviseringar, Power BI och andra verktyg
- Spara data i längre kvarhållningsperioder
- Korsreferens med andra loggar som säkerhet, virtuell dator och anpassade

Här är de grundläggande stegen för att komma igång:

1. [Skapa en Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md).

1. [Konfigurera Activity Log Analytics-lösningen](../azure-monitor/platform/collect-activity-logs.md#configuration) för arbetsytan.

1. [Visa aktivitetsloggar](../azure-monitor/platform/collect-activity-logs.md#using-the-solution). Ett snabbt sätt att navigera till översiktssidan Activity Log Analytics-lösning är att klicka på den **Log Analytics** alternativet.

   ![Azure Monitor loggar alternativet i portalen](./media/change-history-report/azure-log-analytics-option.png)

1. Du kan också använda den [Loggsökning](../log-analytics/log-analytics-log-search.md) sidan eller [Advanced Analytics-portalen](../azure-monitor/log-query/get-started-portal.md) att fråga efter och visa loggarna. Mer information om de här två alternativen finns i [loggsökningssidan eller Advanced Analytics-portalen](../azure-monitor/log-query/portals.md).

Här är en fråga som returnerar nya rolltilldelningar ordnade efter mål-resursprovidern:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Här är en fråga som returnerar ändringarna för tilldelningen av rollen visas i ett diagram:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Aktivitetsloggar med hjälp av Advanced Analytics-portalen – skärmbild](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Nästa steg
* [Visa händelser i aktivitetsloggen](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Övervaka aktivitet om prenumeration med Azure-aktivitetsloggen](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
