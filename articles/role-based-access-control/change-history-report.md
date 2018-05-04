---
title: Visa aktivitetsloggar för RBAC ändringar i Azure | Microsoft Docs
description: Visa aktivitetsloggar för rollbaserad åtkomst kontrollen ändras under de senaste 90 dagarna.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e09ccdc4942a39e54b760cb5ad78c035dbc05f8
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="view-activity-logs-for-role-based-access-control-changes"></a>Visa aktivitetsloggar för rollbaserad åtkomst kontrollen ändras

När någon försöker göra ändringar i rolldefinitioner eller rolltilldelningar inom dina prenumerationer ändringarna får loggas [Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) i den administrativa kategorin. Du kan visa aktivitetsloggar om du vill se alla ändringar för rollbaserad åtkomst-åtkomstkontroll (RBAC) under de senaste 90 dagarna.

## <a name="operations-that-are-logged"></a>Åtgärder som är inloggad

Här följer RBAC-relaterade åtgärder som loggas i aktivitetsloggen:

- Skapa eller uppdatera anpassad rolldefinition
- Ta bort anpassad rolldefinition
- Skapa rolltilldelning
- Ta bort rolltilldelning

## <a name="azure-portal"></a>Azure Portal

Det enklaste sättet att komma igång är att visa aktivitetsloggar med Azure-portalen. Följande skärmbild visar ett exempel på en aktivitetslogg som har filtrerats för att visa den **administrativa** kategori tillsammans med rolldefinition och tilldelning-rollens åtgärder. Den innehåller också en länk för att hämta loggarna som en CSV-fil.

![Aktivitetsloggar med hjälp av portalen – skärmbild](./media/change-history-report/activity-log-portal.png)

Mer information finns i [visa händelser i aktivitetsloggen](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

Om du vill visa aktivitetsloggar med Azure PowerShell, Använd den [Get-AzureRmLog](/powershell/module/azurerm.insights/get-azurermlog) kommando.

Detta kommando visar ändringarna för tilldelningen av alla roll i en prenumeration under de senaste sju dagarna:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Det här kommandot visar alla rollen definitionsändringar i en resursgrupp under de senaste sju dagarna:

```azurepowershell
Get-AzureRmLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Det här kommandot visar alla rolltilldelning och rollen definitionsändringar i en prenumeration under de senaste sju dagarna och visar resultatet i en lista:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
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
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast"}}

```

## <a name="azure-cli"></a>Azure CLI

Om du vill visa aktivitetsloggar med Azure CLI, använder den [az övervaka aktivitetsloggen listan](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) kommando.

Det här kommandot listar aktivitetsloggar i en resursgrupp eftersom starttiden:

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

Detta kommando visar aktivitetsloggarna för resursprovidern tillstånd eftersom starttiden:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Azure logganalys](../log-analytics/log-analytics-overview.md) är ett annat verktyg som du kan använda för att samla in och analysera rollbaserad åtkomst kontrollen ändras för alla dina Azure-resurser. Logganalys har följande fördelar:

- Skriva komplexa frågor och logik
- Integrera med aviseringar, Power BI och andra verktyg
- Spara data i längre kvarhållningsperioder
- Korsreferens med andra loggar, till exempel säkerhet, virtuell dator och anpassade

Här är de grundläggande stegen för att komma igång:

1. [Skapa en logganalys-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md).

1. [Konfigurera aktiviteten logganalys lösningen](../log-analytics/log-analytics-activity.md#configuration) för din arbetsyta.

1. [Visa aktivitetsloggarna](../log-analytics/log-analytics-activity.md#using-the-solution). Ett snabbt sätt att gå till sidan Översikt över aktivitet Log Analytics är att klicka på den **logganalys** alternativet.

   ![Log Analytics-alternativet i portalen](./media/change-history-report/azure-log-analytics-option.png)

1. Du kan också använda den [loggen Sök](../log-analytics/log-analytics-log-search.md) sidan eller [Advanced Analytics portal](https://docs.loganalytics.io/docs/Learn) att fråga efter och visa loggfilerna. Mer information om de här två alternativen finns [loggen söksidan eller Advanced Analytics-portalen](../log-analytics/log-analytics-log-search-portals.md).

Här är en fråga som returnerar nya rolltilldelningar ordnade efter resursprovidern för mål:

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

![Aktivitetsloggar med Advanced Analytics-portalen – skärmbild](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Nästa steg
* [Visa händelser i aktivitetsloggen](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Övervakaraktiviteten i prenumerationen med Azure-aktivitetsloggen](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
