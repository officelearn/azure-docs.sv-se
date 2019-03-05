---
title: Migrera Azure aviseringar för av hanteringshändelser till aktivitetsloggsaviseringar
description: Aviseringar för av hanteringshändelser tas bort den 1 oktober. Förbered genom att migrera befintliga aviseringar.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/14/2017
ms.author: johnkem
ms.subservice: alerts
ms.openlocfilehash: 4d82cc59eb1098451a263957aa028b66996bb072
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307198"
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migrera Azure aviseringar för av hanteringshändelser till aktivitetsloggsaviseringar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!WARNING]
> Aviseringar för av hanteringshändelser kommer att inaktiveras på eller efter 1 oktober. Använd anvisningarna nedan för att förstå om du har dessa aviseringar och migrera dem fall i så.

## <a name="what-is-changing"></a>Vad ändras

Azure Monitor (tidigare Azure Insights) erbjuds en funktion för att skapa en avisering som utlöses från hanteringshändelser och genereras aviseringar till en webhook-URL eller e-postadresser. Du kan ha skapat en av dessa aviseringar något av följande sätt:
* I Azure-portalen för vissa typer av resurser under övervakning -> aviseringar -> Lägg till avisering, där ”Avisera om” anges till ”händelser”
* Genom att köra cmdlet Add-AzLogAlertRule PowerShell
* Genom att använda direkt [avisering REST API](https://docs.microsoft.com/rest/api/monitor/alertrules) med OData.Type värdet = ”ManagementEventRuleCondition” och dataSource.odata.type = ”RuleManagementEventDataSource”
 
Följande PowerShell-skriptet returnerar en lista över alla aviseringar på management-händelser som du har i din prenumeration, samt villkoren på varje avisering.

```powershell
Connect-AzAccount
$alerts = $null
foreach ($rg in Get-AzResourceGroup ) {
  $alerts += Get-AzAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Om du har inga aviseringar om händelser för management PowerShell-cmdleten ovan mata ut en serie varningsmeddelanden som den här:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Dessa varningsmeddelanden kan ignoreras. Om du har aviseringar om hanteringshändelser, ser utdata från denna PowerShell-cmdlet ut så här:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Varje avisering avgränsas med en streckad linje och informationen innehåller resurs-ID för aviseringen och den specifika regeln som övervakas.

Den här funktionen har övergått till [Azure Monitor Aktivitetsloggaviseringar](../../azure-monitor/platform/activity-log-alerts.md). Dessa nya aviseringar kan du ange ett villkor för aktivitetslogghändelserna och får en avisering när en ny händelse matchar villkoret. De erbjuder även flera förbättringar från aviseringar för av hanteringshändelser:
* Du kan återanvända gruppen med meddelandemottagare (”åtgärder”) mellan många aviseringar med hjälp av [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md), vilket minskar komplexiteten med att ändra vem som ska få en avisering.
* Du kan ta emot ett meddelande direkt på din telefon med SMS med åtgärdsgrupper.
* Du kan [skapa aviseringar för aktivitetsloggar med Resource Manager-mallar](../../azure-monitor/platform/alerts-activity-log.md).
* Du kan skapa villkor med större flexibilitet och komplexiteten för att uppfylla dina specifika behov.
* Meddelanden levereras snabbare.
 
## <a name="how-to-migrate"></a>Så här migrerar du
 
Om du vill skapa en ny aktivitet Log avisering, kan du antingen:
* Följ [vår guide om hur du skapar en avisering i Azure portal](../../azure-monitor/platform/activity-log-alerts.md)
* Lär dig hur du [skapar en avisering med en Resource Manager-mall](../../azure-monitor/platform/alerts-activity-log.md)
 
Aviseringar för av hanteringshändelser som du tidigare har skapat flyttas inte automatiskt till Aktivitetsloggaviseringar. Du behöver använda föregående PowerShell-skriptet för att visa aviseringar för av hanteringshändelser att du har konfigurerat och manuellt återskapa dem som Aktivitetsloggaviseringar. Detta måste göras före 1 oktober efter aviseringar för av hanteringshändelser kommer inte längre att visas i din Azure-prenumeration. Andra typer av Azure-aviseringar, inklusive Azure Monitor måttaviseringar, Application Insights-aviseringar och Log Analytics-aviseringar påverkas inte av den här ändringen. Om du har några frågor kan du publicera i kommentarerna nedan.


## <a name="next-steps"></a>Nästa steg

* Läs mer om [aktivitetsloggen](../../azure-monitor/platform/activity-logs-overview.md)
* Konfigurera [Aktivitetsloggaviseringar via Azure-portalen](../../azure-monitor/platform/activity-log-alerts.md)
* Konfigurera [Aktivitetsloggaviseringar via Resource Manager](../../azure-monitor/platform/alerts-activity-log.md)
* Granska den [avisering webhook för aktivitetslogg](../../azure-monitor/platform/activity-log-alerts-webhook.md)
* Läs mer om [tjänstmeddelanden](../../azure-monitor/platform/service-notifications.md)
* Läs mer om [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md)

