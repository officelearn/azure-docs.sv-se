---
title: Visa aktivitets loggar för RBAC-ändringar till Azure-resurser | Microsoft Docs
description: Visa aktivitets loggar för RBAC-ändringar (rollbaserad åtkomst kontroll) för Azure-resurser under de senaste 90 dagarna.
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
ms.openlocfilehash: 753c626fe44193b83cbd992f225fe01c2ff67f89
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75744802"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Visa aktivitets loggar för RBAC-ändringar till Azure-resurser

Ibland behöver du information om RBAC-ändringar (rollbaserad åtkomst kontroll) till Azure-resurser, till exempel för gransknings-eller fel söknings syfte. När någon gör ändringar i roll tilldelningarna eller roll definitionerna i dina prenumerationer kommer ändringarna att loggas i [Azure aktivitets loggen](../azure-monitor/platform/platform-logs-overview.md). Du kan visa aktivitets loggarna om du vill se alla RBAC-ändringar för de senaste 90 dagarna.

## <a name="operations-that-are-logged"></a>Åtgärder som loggas

Här är de RBAC-relaterade åtgärder som är loggade i aktivitets loggen:

- Skapa rolltilldelning
- Ta bort rolltilldelning
- Skapa eller uppdatera anpassad roll definition
- Ta bort anpassad rolldefinition

## <a name="azure-portal"></a>Azure portal

Det enklaste sättet att komma igång på är att visa aktivitetsloggarna i Azure Portal. Följande skärm bild visar ett exempel på en aktivitets logg som har filtrerats för att Visa roll tilldelning och roll definitions åtgärder. Den innehåller också en länk för att ladda ned loggarna som en CSV-fil.

![Aktivitets loggar med portalen-skärm bild](./media/change-history-report/activity-log-portal.png)

Aktivitets loggen på portalen har flera filter. Här är de RBAC-relaterade filtren:

|Filtrera  |Värde  |
|---------|---------|
|Händelse kategori     | <ul><li>Administrativ</li></ul>         |
|Åtgärd     | <ul><li>Skapa rolltilldelning</li> <li>Ta bort rolltilldelning</li> <li>Skapa eller uppdatera anpassad roll definition</li> <li>Ta bort anpassad rolldefinition</li></ul>      |


Mer information om aktivitets loggar finns i [Visa händelser i aktivitets loggen](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Om du vill visa aktivitets loggar med Azure PowerShell använder du kommandot [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

Det här kommandot visar alla ändringar i roll tilldelningen i en prenumeration under de senaste sju dagarna:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Det här kommandot visar en lista över alla roll definitions ändringar i en resurs grupp under de senaste sju dagarna:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Det här kommandot visar alla ändringar av roll tilldelning och roll definition i en prenumeration under de senaste sju dagarna och visar resultatet i en lista:

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

Om du vill visa aktivitets loggar med Azure CLI använder du kommandot [AZ Monitor Activity-Log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) .

Det här kommandot visar aktivitets loggarna i en resurs grupp sedan start tiden:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Det här kommandot listar aktivitets loggarna för providern för auktorisering sedan start tiden:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor loggar](../log-analytics/log-analytics-overview.md) är ett annat verktyg som du kan använda för att samla in och analysera RBAC-ändringar för alla dina Azure-resurser. Azure Monitors loggar har följande fördelar:

- Skriv komplexa frågor och logik
- Integrera med aviseringar, Power BI och andra verktyg
- Spara data för längre Retentions perioder
- Kors referens med andra loggar som säkerhet, virtuell dator och anpassad

Här är de grundläggande stegen för att komma igång:

1. [Skapa en Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md).

1. [Konfigurera Aktivitetslogganalys-lösningen](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) för din arbets yta.

1. [Visa aktivitets loggarna](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Ett snabbt sätt att navigera till sidan Aktivitetslogganalys lösnings översikt är att klicka på alternativet **Log Analytics** .

   ![Alternativ för Azure Monitor-loggar i portalen](./media/change-history-report/azure-log-analytics-option.png)

1. Du kan också använda [loggs öknings](../log-analytics/log-analytics-log-search.md) sidan eller [Advanced Analytics-portalen](../azure-monitor/log-query/get-started-portal.md) för att fråga och visa loggarna. Mer information om dessa två alternativ finns i [loggs öknings sidan eller i Advanced Analytics-portalen](../azure-monitor/log-query/portals.md).

Här är en fråga som returnerar nya roll tilldelningar organiserade efter mål resurs leverantör:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Här är en fråga som returnerar roll tilldelnings ändringar som visas i ett diagram:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![Aktivitets loggar med hjälp av Advanced Analytics-portalen – skärm bild](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Nästa steg
* [Visa händelser i aktivitetsloggen](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Övervaka prenumerationsaktivitet med Azure-aktivitetsloggen](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
