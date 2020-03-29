---
title: Visa aktivitetsloggar för Azure RBAC-ändringar
description: Visa aktivitetsloggar för Azure-rollbaserade åtkomstkontroll (Azure RBAC) ändringar i Azure-resurser för de senaste 90 dagarna.
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
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161808"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Visa aktivitetsloggar för Azure RBAC-ändringar

Ibland behöver du information om Azure rollbaserade åtkomstkontroll (Azure RBAC) ändringar, till exempel för granskning eller felsökning. När någon gör ändringar i rolltilldelningar eller rolldefinitioner i dina prenumerationer loggas ändringarna i [Azure Activity Log](../azure-monitor/platform/platform-logs-overview.md). Du kan visa aktivitetsloggarna för att se alla Azure RBAC-ändringar under de senaste 90 dagarna.

## <a name="operations-that-are-logged"></a>Åtgärder som loggas

Här är de Azure RBAC-relaterade åtgärder som är inloggade i aktivitetsloggen:

- Skapa rolltilldelning
- Ta bort rolltilldelning
- Skapa eller uppdatera anpassad rolldefinition
- Ta bort anpassad rolldefinition

## <a name="azure-portal"></a>Azure Portal

Det enklaste sättet att komma igång på är att visa aktivitetsloggarna i Azure Portal. Följande skärmbild visar ett exempel på rolltilldelningsåtgärder i aktivitetsloggen. Den innehåller också en möjlighet att ladda ner loggarna som en CSV-fil.

![Aktivitetsloggar med hjälp av portalen - skärmdump](./media/change-history-report/activity-log-portal.png)

Aktivitetsloggen i portalen har flera filter. Här är Azure RBAC-relaterade filter:

| Filter | Värde |
| --------- | --------- |
| Händelsekategori | <ul><li>Administrativ</li></ul> |
| Åtgärd | <ul><li>Skapa rolltilldelning</li><li>Ta bort rolltilldelning</li><li>Skapa eller uppdatera anpassad rolldefinition</li><li>Ta bort anpassad rolldefinition</li></ul> |

Mer information om aktivitetsloggar finns i [Visa aktivitetsloggar för att övervaka åtgärder på resurser](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Om du vill visa aktivitetsloggar med Azure PowerShell använder du kommandot [Get-AzLog.](/powershell/module/Az.Monitor/Get-AzLog)

Det här kommandot visar alla rolltilldelningsändringar i en prenumeration de senaste sju dagarna:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Det här kommandot visar alla rolldefinitionsändringar i en resursgrupp under de senaste sju dagarna:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Det här kommandot visar alla rolltilldelnings- och rolldefinitionsändringar i en prenumeration under de senaste sju dagarna och visar resultaten i en lista:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Om du vill visa aktivitetsloggar med Azure CLI använder du kommandot [az monitor activity-log list.](/cli/azure/monitor/activity-log#az-monitor-activity-log-list)

Det här kommandot listar aktivitetsloggarna i en resursgrupp från den 27 februari och ser framåt sju dagar:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Det här kommandot listar aktivitetsloggarna för resursleverantören Auktorisering från och med den 27 februari och ser fram emot sju dagar:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) är ett annat verktyg som du kan använda för att samla in och analysera Azure RBAC-ändringar för alla dina Azure-resurser. Azure Monitor-loggar har följande fördelar:

- Skriv komplexa frågor och logik
- Integrera med aviseringar, Power BI och andra verktyg
- Spara data för längre lagringsperioder
- Korsreferens med andra loggar som säkerhet, virtuell dator och anpassad

Här är de grundläggande stegen för att komma igång:

1. [Skapa en log analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md).

1. [Konfigurera lösningen för Aktivitetslogganalys](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) för din arbetsyta.

1. [Visa aktivitetsloggarna](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Ett snabbt sätt att navigera till sidan Översikt över aktivitetslogganalyslösning är att klicka på alternativet **Loggar.**

   ![Alternativet Azure Monitor-loggar i portalen](./media/change-history-report/azure-log-analytics-option.png)

1. Använd även [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) för att fråga och visa loggarna. Mer information finns i [Komma igång med Azure Monitor-loggfrågor](../azure-monitor/log-query/get-started-queries.md).

Här är en fråga som returnerar nya rolltilldelningar ordnade efter målresursprovider:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Här är en fråga som returnerar rolltilldelningsändringar som visas i ett diagram:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Aktivitetsloggar med hjälp av Advanced Analytics-portalen - skärmdump](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Nästa steg
* [Visa händelser i aktivitetsloggen](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Övervaka prenumerationsaktivitet med Azure-aktivitetsloggen](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
