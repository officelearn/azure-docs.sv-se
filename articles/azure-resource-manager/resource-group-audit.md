---
title: Visa Azures aktivitets loggar för att övervaka resurser | Microsoft Docs
description: Använd aktivitets loggarna om du vill granska användar åtgärder och fel. Visar Azure Portal PowerShell, Azure CLI och REST.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: aba06b07fef1cbc5d84d93cf38fec3bd936c1ac8
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286114"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Visa aktivitets loggar för att övervaka åtgärder på resurser

Via aktivitetsloggarna kan du fastställa:

* vilka åtgärder utfördes på resurserna i din prenumeration
* Vem startade åtgärden
* När åtgärden utfördes
* åtgärdens status
* värdena för andra egenskaper som kan hjälpa dig att undersöka åtgärden

Aktivitets loggen innehåller alla Skriv åtgärder (skicka, anslå och ta bort) för dina resurser. Det omfattar inte läsåtgärder (GET). En lista över resurs åtgärder finns i [Azure Resource Manager Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md). Du kan använda aktivitetsloggarna för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrat en resurs.

Aktivitetsloggar behålls i 90 dagar. Du kan fråga efter alla datumintervall förutsatt att startdatumet inte är mer än 90 dagar bakåt i tiden.

Du kan hämta information från aktivitets loggarna via portalen, PowerShell, Azure CLI, Insights REST API eller [Insights .net-biblioteket](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Azure portal

1. Om du vill visa aktivitets loggarna via portalen väljer du **övervaka**.

    ![Välj övervakare](./media/resource-group-audit/select-monitor.png)

1. Välj **aktivitets logg**.

    ![Välj aktivitets logg](./media/resource-group-audit/select-activity-log.png)

1. En sammanfattning av de senaste åtgärderna visas. En standard uppsättning filter används för åtgärderna. Observera att informationen i sammanfattningen innehåller vem som startade åtgärden och när den hände.

    ![Visa sammanfattning av de senaste åtgärderna](./media/resource-group-audit/audit-summary.png)

1. Om du snabbt vill köra en fördefinierad uppsättning filter väljer du **Snabbinsikter**.

    ![Välj Quick Insights](./media/resource-group-audit/select-quick-insights.png)

1. Välj ett av alternativen. Välj till exempel **misslyckade distributioner** om du vill visa fel från distributioner.

    ![Välj misslyckade distributioner](./media/resource-group-audit/select-failed-deployments.png)

1. Observera att filtren har ändrats för att fokusera på distributions fel under de senaste 24 timmarna. Endast åtgärder som matchar filtren visas.

    ![Vyfilter](./media/resource-group-audit/view-filters.png)

1. Om du vill fokusera på vissa åtgärder ändrar du filtren eller använder nya. Följande bild visar till exempel ett nytt värde för **TimeSpan** och **resurs typen** är inställd på lagrings konton.

    ![Ange filter alternativ](./media/resource-group-audit/set-filter.png)

1. Om du behöver köra frågan igen senare väljer du **Fäst aktuella filter**.

    ![Fästa filter](./media/resource-group-audit/pin-filters.png)

1. Ge filtret ett namn.

    ![Namn filter](./media/resource-group-audit/name-filters.png)

1. Filtret är tillgängligt på instrument panelen.

    ![Visa filter på instrument panelen](./media/resource-group-audit/show-dashboard.png)

1. Från portalen kan du Visa ändringar i en resurs. Gå tillbaka till standardvyn i övervakaren och välj en åtgärd som involverade ändringar av en resurs.

    ![Välj åtgärd](./media/resource-group-audit/select-operation.png)

1. Välj **ändrings historik (för hands version)** och välj en av de tillgängliga åtgärderna.

    ![Välj ändrings historik](./media/resource-group-audit/select-change-history.png)

1. Ändringarna i resursen visas.

    ![Visa ändringar](./media/resource-group-audit/show-changes.png)

Läs mer om ändrings historik i [Hämta resurs ändringar](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill hämta logg poster kör du kommandot **Get-AzLog** . Du kan ange ytterligare parametrar för att filtrera listan över poster. Om du inte anger en start-och slut tid returneras poster för de senaste sju dagarna.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

I följande exempel visas hur du använder aktivitets loggen för att undersöka de åtgärder som vidtas under en viss tid. Start-och slutdatum anges i datum format.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Du kan också använda datum funktioner för att ange datum intervallet, till exempel de senaste 14 dagarna.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Du kan söka efter de åtgärder som vidtagits av en viss användare.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Du kan filtrera efter misslyckade åtgärder.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Du kan fokusera på ett fel genom att titta på status meddelandet för den posten.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Du kan välja vissa värden om du vill begränsa de data som returneras.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Beroende på den start tid som du anger kan tidigare kommandon returnera en lång lista med åtgärder för resurs gruppen. Du kan filtrera resultaten för det du söker genom att tillhandahålla Sök villkor. Du kan till exempel filtrera efter typ av åtgärd.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Du kan använda resurs diagram för att Visa ändrings historiken för en resurs. Mer information finns i [Hämta resurs ändringar](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Azure CLI

Om du vill hämta logg poster kör du kommandot [AZ Monitor Activity-Log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) med en förskjutning som visar tidsintervallet.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

I följande exempel visas hur du använder aktivitets loggen för att undersöka de åtgärder som vidtas under en viss tid. Start-och slutdatum anges i datum format.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Du kan söka efter de åtgärder som vidtas av en viss användare, även för en resurs grupp som inte längre finns.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Du kan filtrera efter misslyckade åtgärder.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Du kan fokusera på ett fel genom att titta på status meddelandet för den posten.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Du kan välja vissa värden om du vill begränsa de data som returneras.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Beroende på den start tid som du anger kan tidigare kommandon returnera en lång lista med åtgärder för resurs gruppen. Du kan filtrera resultaten för det du söker genom att tillhandahålla Sök villkor. Du kan till exempel filtrera efter typ av åtgärd.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Du kan använda resurs diagram för att Visa ändrings historiken för en resurs. Mer information finns i [Hämta resurs ändringar](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>REST-API

REST-åtgärderna för att arbeta med aktivitets loggen ingår i [insikter REST API](/rest/api/monitor/). Information om hur du hämtar aktivitets logg händelser finns i [Visa en lista över hanterings händelser i en prenumeration](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Nästa steg

* Azure aktivitets loggar kan användas med Power BI för att få bättre insikter om åtgärder i din prenumeration. Se [Visa och analysera Azure aktivitets loggar i Power BI med mera](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Information om hur du ställer in säkerhets principer finns i [Azure Role-based Access Control](../role-based-access-control/role-assignments-portal.md).
* Om du vill visa mer information om ändringarna i dina program från infrastruktur lagret hela vägen till program distributionen, se [använda program ändrings analys i Azure Monitor] (.. /Azure-Monitor/app/Change-Analysis. MD).
* Mer information om kommandon för att Visa distributions åtgärder finns i [Visa distributions åtgärder](resource-manager-deployment-operations.md).
* Information om hur du förhindrar borttagningar av en resurs för alla användare finns i [låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).
* Om du vill se en lista över tillgängliga åtgärder för varje Microsoft Azure Resource Manager-Provider, se [Azure Resource Manager Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md)
