---
title: Migrera Azure aviseringar på management-händelser till aktivitetsloggen aviseringar
description: Aviseringar om management-händelser tas bort på 1 oktober. Förbereda genom att migrera befintliga aviseringar.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/14/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 9e4302b780d0c08afbc791a0aec6bfd806aba161
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263712"
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migrera Azure aviseringar på management-händelser till aktivitetsloggen aviseringar


> [!WARNING]
> Aviseringar om händelser för management inaktiveras från och med 1 oktober. Använd anvisningarna nedan för att förstå om du har dessa aviseringar och migrera dem i så fall.
>
> 

## <a name="what-is-changing"></a>Vad ändras

Azure-Monitor (tidigare Azure insikter) erbjuder en möjlighet att skapa en avisering som aktiveras från management händelser och genererade aviseringar till en webhook URL eller e-postadresser. Du har skapat en av dessa aviseringar något av följande sätt:
* I Azure-portalen för vissa typer av resurser under övervakning -> aviseringar -> Lägg till varning, där ”Varna om” är inställt på ”händelser”
* Genom att köra cmdlet Add-AzureRmLogAlertRule PowerShell
* Med hjälp av direkt [avisering REST API](http://docs.microsoft.com/rest/api/monitor/alertrules) med odata.type = ”ManagementEventRuleCondition” och dataSource.odata.type = ”RuleManagementEventDataSource”
 
Följande PowerShell-skript returnerar en lista över alla aviseringar för av hanteringshändelser som du har i din prenumeration, samt de villkor som anges på varje avisering.

```powershell
Connect-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
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

Om du har inga aviseringar om händelser för management PowerShell-cmdleten ovan kommer att skrivas ut en serie varningsmeddelanden som detta:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Dessa varningar kan ignoreras. Om du har aviseringar om hanteringshändelser, ser resultatet av denna PowerShell-cmdlet ut så här:

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

Varje avisering avgränsas med en streckad linje och innehåller resurs-ID för aviseringen och den specifika regeln som övervakas.

Den här funktionen har gått över till [Azure övervaka aktiviteten loggen aviseringar](monitoring-activity-log-alerts.md). Dessa nya aviseringar kan du ange ett villkor på aktivitetsloggen händelser och ett meddelande när en ny händelse matchar villkoret. De erbjuder också flera förbättringar från aviseringar om hanteringshändelser:
* Du kan återanvända din grupp meddelandemottagare (”åtgärder”) över flera aviseringar via [åtgärdsgrupper](monitoring-action-groups.md), minska komplexiteten vid ändring av vem som ska få en avisering.
* Du kan få ett meddelande direkt på telefonen med hjälp av SMS med åtgärdsgrupper.
* Du kan [Skapa aktivitet Logga varningar med Resource Manager-mallar](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* Du kan skapa villkor med större flexibilitet och komplexitet för att uppfylla dina specifika behov.
* Meddelanden levereras snabbare.
 
## <a name="how-to-migrate"></a>Hur du migrerar
 
Om du vill skapa en ny aktivitet loggen avisering, kan du antingen:
* Följ [vår vägledning om hur du skapar en avisering i Azure-portalen](monitoring-activity-log-alerts.md)
* Lär dig hur du [skapar en avisering med en Resource Manager-mall](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
 
Aviseringar på management-händelser som du tidigare har skapat flyttas inte automatiskt till aktiviteten loggen aviseringar. Du måste använda föregående PowerShell-skript för att visa aviseringar för av hanteringshändelser att du har konfigurerat och manuellt återskapa dem som aktiviteten loggen aviseringar. Detta måste göras innan 1 oktober efter aviseringar om händelser för management kommer inte längre att synas i din Azure-prenumeration. Andra typer av Azure aviseringar, inklusive Azure övervaka mått aviseringar, Programinsikter aviseringar och logganalys aviseringar påverkas inte av den här ändringen. Om du har några frågor efter i kommentarerna nedan.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [aktivitetsloggen](monitoring-overview-activity-logs.md)
* Konfigurera [aktivitet loggen aviseringar via Azure-portalen](monitoring-activity-log-alerts.md)
* Konfigurera [aktivitet loggen aviseringar via Hanteraren för filserverresurser](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Granska de [avisering webhook för aktivitetslogg](monitoring-activity-log-alerts-webhook.md)
* Lär dig mer om [tjänstmeddelanden](monitoring-service-notifications.md)
* Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md)
