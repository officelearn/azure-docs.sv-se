---
title: Visa Azure-aktivitetsloggar att övervaka resurser | Microsoft Docs
description: Använd aktivitetsloggar till användaråtgärder för granskning och fel. Visar Azure Portal PowerShell, Azure CLI och REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 636e4d5216f87440463fbaecd7f6c7a5a25c7502
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359399"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Visa aktivitetsloggar för att granska åtgärder på resurser

Via aktivitetsloggarna kan du fastställa:

* vilka åtgärder som utförts på resurser i din prenumeration
* den som initierade åtgärden (även om åtgärder som initieras av en serverdelstjänst inte returnerar en användare som anroparen)
* När åtgärden utfördes
* Status för åtgärden
* Värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden

Aktivitetsloggen innehåller alla skrivåtgärder (PUT, POST, ta bort) utförs på dina resurser. Det inkluderar inte läsåtgärder (GET). En lista över resource åtgärder finns i [Azure Resource Manager Resource Provider operations](../role-based-access-control/resource-provider-operations.md). Du kan använda granskningsloggarna för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrat en resurs.

Aktivitetsloggar bibehålls i 90 dagar. Du kan fråga efter alla datumintervall förutsatt att startdatumet inte är mer än 90 dagar bakåt i tiden.

Du kan hämta information från aktivitetsloggar via portalen, PowerShell, Azure CLI, Insights REST API eller [Insights-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portalen

1. Om du vill visa aktivitetsloggar via portalen, Välj **övervakaren**.

    ![Välj aktivitetsloggar](./media/resource-group-audit/select-monitor.png)

   Eller, för att automatiskt filtrera aktivitetsloggen för en viss resurs eller resursgrupp, Välj **aktivitetsloggen**. Observera att aktivitetsloggen automatiskt filtreras efter den valda resursen.

    ![Filtrera efter resurs](./media/resource-group-audit/filtered-by-resource.png)
2. I den **aktivitetsloggen**, visas en sammanfattning av de senaste åtgärder.

    ![Visa åtgärder](./media/resource-group-audit/audit-summary.png)
3. Välj olika villkor för att begränsa antalet åtgärder som visas. Exempelvis följande bild visar den **Timespan** och **händelsen initieras av** fält ändras för att visa de åtgärder som vidtas av en viss användare eller ett program under den senaste månaden. Välj **tillämpa** att visa resultatet av frågan.

    ![Anger filteralternativ](./media/resource-group-audit/set-filter.png)

4. Om du vill köra frågan igen senare, Välj **PIN-kod aktuella filtren** och ge den ett namn.

    ![Spara fråga](./media/resource-group-audit/save-query.png)
5. För att snabbt köra en fråga, kan du välja något av de inbyggda frågorna, till exempel inte kunde distribueras.

    ![Välj fråga](./media/resource-group-audit/select-quick-query.png)

   Den valda frågan anger automatiskt filtervärdena som krävs.

    ![Visa distributionsfel](./media/resource-group-audit/view-failed-deployment.png)

6. Välj någon av åtgärderna för att se en sammanfattning av händelsen.

    ![Visa åtgärd](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell

1. Kör för att hämta poster i **Get-AzureRmLog** kommando. Du kan ange ytterligare parametrar för att filtrera listan med poster. Om du inte anger en start- och -tid, returneras poster för den senaste timmen. Till exempel kör för att hämta åtgärder för en resursgrupp under den senaste timmen:

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```

    I följande exempel visar hur du använder aktivitetsloggen till research-åtgärder som vidtagits under en angiven tid. Start- och slutdatumen har angetts i ett datumformat.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Eller så kan du använda datumfunktioner för att ange datumintervall, till exempel de senaste 14 dagarna.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. Beroende på den starttid som du anger kan de tidigare kommandona returnera en lång lista med åtgärder för resursgruppen. Du kan filtrera resultaten för vad du letar efter genom att ange sökvillkor. Du kan till exempel köra följande kommando om du vill undersöka hur en webbapp har stoppats:

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    Som i det här exemplet visar att en stop-åtgärd utfördes av someone@contoso.com.

  ```powershell
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. Du kan söka efter de åtgärder som vidtas av en viss användare, även för en resursgrupp som inte längre finns.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Du kan filtrera efter misslyckade åtgärder.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Du kan fokusera på ett fel genom att titta på statusmeddelanden för den posten.

  ```azurepowershell-interactive
  ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
  ```

    Som returnerar:

        code           message
        ----           -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP.

## <a name="azure-cli"></a>Azure CLI

Kör för att hämta poster i [az monitor-aktivitetsloggen lista](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) kommando.

  ```azurecli
  az monitor activity-log list --resource-group <group name>
  ```

## <a name="rest-api"></a>REST-API

REST-åtgärder för att arbeta med aktivitetsloggen är en del av den [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx). Om du vill hämta händelser i aktivitetsloggen, se [management-händelser i en prenumeration](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Nästa steg

* Azure-aktivitetsloggar kan användas med Power BI för att få bättre kunskaper om åtgärderna i din prenumeration. Se [visa och analysera Azure-aktivitetsloggar i Power BI och mycket annat](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Läs om hur du ställer in säkerhetsprinciper i [Azure rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md).
* Läs om kommandona för att visa distributionsåtgärder i [visa distributionsåtgärder](resource-manager-deployment-operations.md).
* Läs hur du förhindrar borttagningar på en resurs för alla användare i [låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).
* Listan över åtgärder som är tillgängliga för varje Microsoft Azure Resource Manager-providern finns i [Resursprovidern för Azure Resource Manager-åtgärder](../role-based-access-control/resource-provider-operations.md)
