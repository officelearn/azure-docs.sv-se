---
title: Visa Azure-aktivitetsloggar för att övervaka resurser
description: Använd aktivitetsloggarna för att granska användaråtgärder och fel. Visar Azure-portalen PowerShell, Azure CLI och REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 9511090099894fad6708843f106570ed029f0c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478141"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Visa aktivitetsloggar för att övervaka åtgärder på resurser

Via aktivitetsloggarna kan du fastställa:

* vilka åtgärder som togs på resurserna i din prenumeration
* som startade operationen
* när operationen inträffade
* status för operationen
* värdena för andra egenskaper som kan hjälpa dig att undersöka

Aktivitetsloggen innehåller alla skrivåtgärder (PUT, POST, DELETE) för dina resurser. Det omfattar inte läsåtgärder (GET). En lista över resursåtgärder finns i [Azure Resource Manager Resource Provider- åtgärder](../../role-based-access-control/resource-provider-operations.md). Du kan använda aktivitetsloggarna för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrat en resurs.

Aktivitetsloggar behålls i 90 dagar. Du kan fråga efter alla datumintervall förutsatt att startdatumet inte är mer än 90 dagar bakåt i tiden.

Du kan hämta information från aktivitetsloggarna via portalen, PowerShell, Azure CLI, Insights REST API eller [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Azure Portal

Så här visar du aktivitetsloggarna via portalen:

1. På Azure-portalmenyn väljer du **Övervaka**eller söker efter och väljer **Övervaka** på valfri sida.

    ![Välj bildskärm](./media/view-activity-logs/select-monitor-from-menu.png)

1. Välj **Aktivitetslogg**.

    ![Välj aktivitetslogg](./media/view-activity-logs/select-activity-log.png)

1. Du ser en sammanfattning av de senaste åtgärderna. En standarduppsättning filter tillämpas på åtgärderna. Lägg märke till informationen i sammanfattningen som innehåller vem som startade åtgärden och när den inträffade.

    ![Visa sammanfattning av de senaste åtgärderna](./media/view-activity-logs/audit-summary.png)

1. Om du snabbt vill köra en fördefinierad uppsättning filter väljer du **Snabbstatistik**.

    ![Välj snabba insikter](./media/view-activity-logs/select-quick-insights.png)

1. Välj ett av alternativen. Välj till exempel **Misslyckade distributioner** om du vill se fel från distributioner.

    ![Välj misslyckade distributioner](./media/view-activity-logs/select-failed-deployments.png)

1. Observera att filtren har ändrats för att fokusera på distributionsfel under de senaste 24 timmarna. Endast åtgärder som matchar filtren visas.

    ![Vyfilter](./media/view-activity-logs/view-filters.png)

1. Om du vill fokusera på specifika åtgärder ändrar du filtren eller använder nya. Följande bild visar till exempel ett nytt värde för **typen Tidsspan och** **Resurs** är inställd på lagringskonton.

    ![Ange filteralternativ](./media/view-activity-logs/set-filter.png)

1. Om du behöver köra frågan igen senare väljer du **Fäst aktuella filter**.

    ![Fästa filter](./media/view-activity-logs/pin-filters.png)

1. Ge filtret ett namn.

    ![Namnfilter](./media/view-activity-logs/name-filters.png)

1. Filtret är tillgängligt på instrumentpanelen. I menyn i Azure-portalen väljer du **Instrumentpanel**.

    ![Visa filter på instrumentpanelen](./media/view-activity-logs/activity-log-on-dashboard.png)

1. Från portalen kan du visa ändringar i en resurs. Gå tillbaka till standardvyn i Monitor och välj en åtgärd som innebar att ändra en resurs.

    ![Välj åtgärd](./media/view-activity-logs/select-operation.png)

1. Välj **Ändringshistorik (Förhandsgranskning)** och välj en av de tillgängliga åtgärderna.

    ![Välj ändringshistorik](./media/view-activity-logs/select-change-history.png)

1. Ändringarna i resursen visas.

    ![Visa ändringar](./media/view-activity-logs/show-changes.png)

Mer information om ändringshistorik finns i [Hämta resursändringar](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill hämta loggposter kör du kommandot **Get-AzLog.** Du anger ytterligare parametrar för att filtrera listan över poster. Om du inte anger någon start- och sluttid returneras transaktioner för de senaste sju dagarna.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

I följande exempel visas hur du använder aktivitetsloggen för att undersöka åtgärder som vidtas under en angiven tid. Start- och slutdatumen anges i ett datumformat.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Du kan också använda datumfunktioner för att ange datumintervallet, till exempel de senaste 14 dagarna.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Du kan slå upp de åtgärder som vidtas av en viss användare.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Du kan filtrera efter misslyckade åtgärder.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Du kan fokusera på ett fel genom att titta på statusmeddelandet för den posten.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Du kan välja specifika värden för att begränsa de data som returneras.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Beroende på vilken starttid du anger kan de tidigare kommandona returnera en lång lista med åtgärder för resursgruppen. Du kan filtrera resultaten efter det du söker genom att ange sökvillkor. Du kan till exempel filtrera efter typ av åtgärd.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Du kan använda Resursdiagram för att se ändringshistoriken för en resurs. Mer information finns i [Hämta resursändringar](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Azure CLI

Om du vill hämta loggposter kör du kommandot [för aktivitetsloggen az-övervakare](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) med en förskjutning för att ange tidsintervallet.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

I följande exempel visas hur du använder aktivitetsloggen för att undersöka åtgärder som vidtas under en angiven tid. Start- och slutdatumen anges i ett datumformat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Du kan slå upp de åtgärder som vidtas av en viss användare, även för en resursgrupp som inte längre finns.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Du kan filtrera efter misslyckade åtgärder.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Du kan fokusera på ett fel genom att titta på statusmeddelandet för den posten.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Du kan välja specifika värden för att begränsa de data som returneras.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Beroende på vilken starttid du anger kan de tidigare kommandona returnera en lång lista med åtgärder för resursgruppen. Du kan filtrera resultaten efter det du söker genom att ange sökvillkor. Du kan till exempel filtrera efter typ av åtgärd.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Du kan använda Resursdiagram för att se ändringshistoriken för en resurs. Mer information finns i [Hämta resursändringar](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>REST API

REST-åtgärderna för att arbeta med aktivitetsloggen är en del av [Insights REST API](/rest/api/monitor/). Information om hur du hämtar aktivitetslogghändelser finns [i Lista hanteringshändelser i en prenumeration](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Nästa steg

* Azure Activity-loggar kan användas med Power BI för att få större insikter om åtgärderna i din prenumeration. Se [Visa och analysera Azure Activity Logs i Power BI med mera](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Mer information om hur du anger säkerhetsprinciper finns i [Azure Role-based Access Control](../../role-based-access-control/role-assignments-portal.md).
* Mer information om ändringar i dina program från infrastrukturlagret hela vägen till programdistribution finns [i Använda programändringsanalys i Azure Monitor](../../azure-monitor/app/change-analysis.md).
* Mer information om kommandona för visning av distributionsåtgärder finns i [Visa distributionsåtgärder](../templates/deployment-history.md).
* Mer information om hur du förhindrar borttagningar på en resurs för alla användare finns i [Lås resurser med Azure Resource Manager](lock-resources.md).
* Information om hur du visar listan över åtgärder som är tillgängliga för varje Microsoft Azure Resource Manager-provider finns i [Azure Resource Manager Resource Provider-åtgärder](../../role-based-access-control/resource-provider-operations.md)
