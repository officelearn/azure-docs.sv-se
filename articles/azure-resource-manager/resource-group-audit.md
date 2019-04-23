---
title: Visa Azure-aktivitetsloggar att övervaka resurser | Microsoft Docs
description: Använd aktivitetsloggar till användaråtgärder för granskning och fel. Visar Azure Portal PowerShell, Azure CLI och REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: tomfitz
ms.openlocfilehash: 8348099d778a9ec65e907bb3d21ae995041b9fb6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786107"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Visa aktivitetsloggar för att granska åtgärder på resurser

Via aktivitetsloggarna kan du fastställa:

* vilka åtgärder som utförts på resurser i din prenumeration
* vem som startade åtgärden
* När åtgärden utfördes
* Status för åtgärden
* Värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden

Aktivitetsloggen innehåller alla skrivåtgärder (PUT, POST, ta bort) utförs på dina resurser. De omfattar inte läsåtgärder (GET). En lista över resource åtgärder finns i [Azure Resource Manager Resource Provider operations](../role-based-access-control/resource-provider-operations.md). Du kan använda granskningsloggarna för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrat en resurs.

Aktivitetsloggar behålls i 90 dagar. Du kan fråga efter alla datumintervall, förutsatt att startdatumet är inte mer än 90 dagar tidigare.

Du kan hämta information från aktivitetsloggar via portalen, PowerShell, Azure CLI, Insights REST API eller [Insights-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="the-azure-portal"></a>Azure Portal

1. Om du vill visa aktivitetsloggar via portalen, Välj **övervakaren**.

    ![Välj Övervakare](./media/resource-group-audit/select-monitor.png)

1. Välj **aktivitetsloggen**.

    ![Välj aktivitetsloggen](./media/resource-group-audit/select-activity-log.png)

1. Du ser en sammanfattning av de senaste åtgärder. En standarduppsättning med filter tillämpas på åtgärderna.

    ![Visa en sammanfattning av de senaste åtgärder](./media/resource-group-audit/audit-summary.png)

1. För att snabbt köra en fördefinierad uppsättning filter, Välj **Quick Insights** och välja något av alternativen.

    ![Välj fråga](./media/resource-group-audit/quick-insights.png)

1. Ändra filtren för att fokusera på specifika åtgärder, eller använda nya. Följande bild visar exempelvis ett nytt värde för den **Timespan** och **resurstyp** är inställd på storage-konton. 

    ![Anger filteralternativ](./media/resource-group-audit/set-filter.png)

1. Om du vill köra frågan igen senare, Välj **PIN-kod aktuella filtren**.

    ![Fäst filter](./media/resource-group-audit/pin-filters.png)

1. Ge filtret ett namn.

    ![Namn på filter](./media/resource-group-audit/name-filters.png)

1. Filtret är tillgängliga i instrumentpanelen.

    ![Visa filter på instrumentpanelen](./media/resource-group-audit/show-dashboard.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Kör för att hämta poster i **Get-AzLog** kommando. Du kan ange ytterligare parametrar för att filtrera listan med poster. Om du inte anger en start- och -tid, returneras poster för de senaste sju dagarna.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup
  ```

    I följande exempel visar hur du använder aktivitetsloggen till research-åtgärder som vidtagits under en angiven tid. Start- och slutdatumen har angetts i ett datumformat.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-01-09T06:00 -EndTime 2019-01-15T06:00
  ```

    Eller så kan du använda datumfunktioner för att ange datumintervall, till exempel de senaste 14 dagarna.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

* Du kan söka efter de åtgärder som vidtas av en viss användare, även för en resursgrupp som inte längre finns.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

* Du kan filtrera efter misslyckade åtgärder.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -Status Failed
  ```

* Du kan fokusera på ett fel genom att titta på statusmeddelanden för den posten.

  ```azurepowershell-interactive
  ((Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties[0].Content.statusMessage | ConvertFrom-Json).error
  ```

* Du kan välja specifika värden för att begränsa de data som returneras.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
  ```

* Beroende på den starttid som du anger kan de tidigare kommandona returnera en lång lista med åtgärder för resursgruppen. Du kan filtrera resultaten för vad du letar efter genom att ange sökvillkor. Du kan till exempel filtrera efter typ av åtgärd.

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
  ```

## <a name="azure-cli"></a>Azure CLI

* Kör för att hämta poster i [az monitor-aktivitetsloggen lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) med en förskjutning som anger tidsintervallet.

  ```azurecli-interactive
  az monitor activity-log list --resource-group ExampleGroup --offset 7d
  ```

  I följande exempel visar hur du använder aktivitetsloggen till research-åtgärder som vidtagits under en angiven tid. Start- och slutdatumen har angetts i ett datumformat.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --start-time 2019-01-01 --end-time 2019-01-15
  ```

* Du kan söka efter de åtgärder som vidtas av en viss användare, även för en resursgrupp som inte längre finns.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
  ```

* Du kan filtrera efter misslyckade åtgärder.

  ```azurecli-interactive
  az monitor activity-log list -g demoRG --status Failed --offset 1d
  ```

* Du kan fokusera på ett fel genom att titta på statusmeddelanden för den posten.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
  ```

* Du kan välja specifika värden för att begränsa de data som returneras.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
  ```

* Beroende på den starttid som du anger kan de tidigare kommandona returnera en lång lista med åtgärder för resursgruppen. Du kan filtrera resultaten för vad du letar efter genom att ange sökvillkor. Du kan till exempel filtrera efter typ av åtgärd.

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
  ```

## <a name="rest-api"></a>REST-API

REST-åtgärder för att arbeta med aktivitetsloggen är en del av den [Insights REST API](/rest/api/monitor/). Om du vill hämta händelser i aktivitetsloggen, se [management-händelser i en prenumeration](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Nästa steg

* Azure-aktivitetsloggar kan användas med Power BI för att få bättre kunskaper om åtgärderna i din prenumeration. Se [visa och analysera Azure-aktivitetsloggar i Power BI och mycket annat](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Läs om hur du ställer in säkerhetsprinciper i [Azure rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md).
* Läs om kommandona för att visa distributionsåtgärder i [visa distributionsåtgärder](resource-manager-deployment-operations.md).
* Läs hur du förhindrar borttagningar på en resurs för alla användare i [låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).
* Listan över åtgärder som är tillgängliga för varje Microsoft Azure Resource Manager-providern finns i [Resursprovidern för Azure Resource Manager-åtgärder](../role-based-access-control/resource-provider-operations.md)
