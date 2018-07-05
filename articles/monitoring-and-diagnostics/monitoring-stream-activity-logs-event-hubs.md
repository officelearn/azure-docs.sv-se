---
title: Stream Azure-aktivitetsloggen till Event Hubs
description: Lär dig mer om att strömma Azure-aktivitetsloggen till Event Hubs.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/02/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 45352c1cf4aca9043c23bbe12e94ba770a38c01b
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436713"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream Azure-aktivitetsloggen till Event Hubs
Du kan strömma den [Azure-aktivitetsloggen](monitoring-overview-activity-logs.md) i nära realtid för program genom att antingen:

* Med hjälp av inbyggt **exportera** alternativet i portalen
* Aktivera Azure Service Bus-regel-ID i en loggprofil för via Azure PowerShell-cmdlets eller Azure CLI

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Vad du kan göra med aktivitetsloggen och Event Hubs
Här finns två sätt som du kan använda den strömmande kapaciteten för aktivitetsloggen:

* **Stream att loggning och telemetri tredjepartssystem**: över tid, Azure Event Hubs för direktuppspelning och kommer att bli en mekanism för att skicka vidare din aktivitetslogg till tredje parts SIEMs och logga Analyslösningar.
* **Skapa en anpassad telemetri och loggning**: Om du redan har en specialbyggda telemetri plattform eller funderar på att skapa en mycket skalbar publicerings-/ natur Händelsehubbar kan du flexibelt kan mata in aktivitetsloggen. Mer information finns i [Dan Rosanova video om hur du använder Event Hubs i en global skala telemetri platform](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Aktivera strömning av aktivitetsloggen
Du kan aktivera strömning av aktivitetsloggen programmässigt eller via portalen. Antingen på så sätt kan du välja ett Event Hubs-namnområde och en princip för delad åtkomst för det namnområdet. En händelsehubb med namnet insights-logs-operationallogs skapas i det namnområdet när den första nya aktivitetslogghändelsen inträffar. 

Om du inte har ett namnområde för Event Hubs, måste du först skapa ett. Om du strömmat tidigare händelser i aktivitetsloggen till Event Hubs-namnområdet, kan händelsehubben som skapats tidigare återanvändas. 

Princip för delad åtkomst definierar de behörigheter som har mekanismen för direktuppspelning. Idag, strömma till Event Hubs kräver **hantera**, **skicka**, och **lyssna** behörigheter. Du kan skapa eller ändra principer för delad åtkomst för Event Hubs-namnområde i Azure-portalen under den **konfigurera** flik för Event Hubs-namnområdet. 

Om du vill uppdatera aktivitetsloggen log profilen om du vill inkludera streaming, måste den användare som ansvarar för att göra ändringen ha behörigheten ListKey på den Event Hubs-auktoriseringsregeln. Event Hubs-namnområdet behöver inte vara i samma prenumeration som den prenumeration som genererar loggar, så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.

### <a name="via-the-azure-portal"></a>Via Azure portal
1. Bläddra till den **aktivitetsloggen** avsnittet med hjälp av den **alla tjänster** sökning på vänster sida av portalen.
   
   ![Att välja aktivitetsloggen i listan över tjänster i portalen](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Välj den **exportera** längst upp i loggen.
   
   ![Exportera-knappen i portalen](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Observera att inställningarna i filtret som du har tillämpat när du visar aktivitetsloggen i den föregående vyn har ingen inverkan på dina exportinställningar. Här är bara för att filtrera vad som visas när de bläddrar igenom din aktivitetsloggen i portalen.
3. I avsnittet som visas, väljer **alla regioner**. Markera inte specifika regioner.
   
   ![Exportera avsnittet](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Om du väljer något annat än **alla regioner**, du kommer att sakna viktiga händelser som du förväntar dig att ta emot. Aktivitetsloggen är en global (icke-regionalt)-loggen, så de flesta händelser inte har en region som är associerade med dem. 
   >

4. Välj **spara** dessa inställningar ska sparas. Inställningarna tillämpas omedelbart på din prenumeration.
5. Om du har flera prenumerationer kan upprepa den här åtgärden och skicka alla data till samma event hub.

### <a name="via-powershell-cmdlets"></a>Via PowerShell-cmdletar
Om det finns redan en loggprofil, måste du först ta bort den befintliga log-profilen och sedan skapa en ny loggprofil.

1. Använd `Get-AzureRmLogProfile` att identifiera om det finns en loggprofil.  Om det finns en loggprofil, leta upp den *namn* egenskapen.
2. Använd `Remove-AzureRmLogProfile` att ta bort log-profilen med värdet från den *namn* egenskapen.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. Använd `Add-AzureRmLogProfile` att skapa en ny loggprofil:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Via Azure CLI
Om det finns redan en loggprofil, måste du först ta bort den befintliga log-profilen och sedan skapa en ny loggprofil.

1. Använd `az monitor log-profiles list` att identifiera om det finns en loggprofil.
2. Använd `az monitor log-profiles delete --name "<log profile name>` att ta bort log-profilen med värdet från den *namn* egenskapen.
3. Använd `az monitor log-profiles create` att skapa en ny loggprofil:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Använda loggdata från Event Hubs
Schemat för aktivitetsloggen finns i [övervaka prenumeration med Azure-aktivitetsloggen](monitoring-overview-activity-logs.md). Varje händelse är i en matris av JSON-blobar som kallas *poster*.

## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitetsloggen till ett lagringskonto](monitoring-archive-activity-log.md)
* [Läs en översikt över Azure-aktivitetsloggen](monitoring-overview-activity-logs.md)
* [Konfigurera en avisering baserat på en händelse i aktivitetsloggen](insights-auditlog-to-webhook-email.md)

