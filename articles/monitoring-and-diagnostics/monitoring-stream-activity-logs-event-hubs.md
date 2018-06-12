---
title: Dataströmmen Azure aktivitetsloggen i Händelsehubbar
description: Lär dig mer om att strömma Azure-aktivitetsloggen i Händelsehubbar.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/02/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 1f1a131d4e0cf900d04acc9730b04e1375f396a6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264307"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Dataströmmen Azure aktivitetsloggen i Händelsehubbar
Du kan strömma den [Azure-aktivitetsloggen](monitoring-overview-activity-logs.md) i nära realtid för alla program genom att antingen:

* Använda inbyggda **exportera** alternativet i portalen
* Aktivera Azure Service Bus regel-ID i en logg profil via Azure PowerShell-cmdlets eller Azure CLI

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Vad du kan göra med aktivitetsloggen och Händelsehubbar
Här följer två sätt som du kan använda den strömmande kapaciteten för aktivitetsloggen:

* **Dataström som loggning och telemetri tredjepartssystem**: över tiden, Azure Event Hubs strömning och kommer att bli en mekanism för att skicka din aktivitetsloggen till Siem från tredje part och logga Analyslösningar.
* **Skapa en anpassad telemetri och loggning plattform**: Om du redan har en specialbyggt telemetri plattform eller funderar om hur du skapar en mycket skalbar publicera och prenumerera uppbyggnad Event Hubs gör att du kan flexibelt infognings-aktivitetsloggen. Mer information finns i [Dan Rosanova video om hur du använder Händelsehubbar i en global skala telemetri platform](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Strömning av aktivitetsloggen
Du kan aktivera strömning av aktivitetsloggen programmässigt eller via portalen. Oavsett hur du väljer ett Händelsehubbar namnområde och en princip för delad åtkomst för det namnområdet. En händelsehubb med namnet insikter-loggar-operationallogs skapas i namnutrymmet när den första nya aktivitetsloggen händelsen inträffar. 

Om du inte har ett namnområde för Händelsehubbar, måste du först skapa en. Om du tidigare strömmas aktivitetsloggen händelser till Händelsehubbar namnområdet, kan händelsehubben som skapades tidigare återanvändas. 

Princip för delad åtkomst definierar vilka behörigheter som har mekanismen för strömning. Idag strömning till Händelsehubbar kräver **hantera**, **skicka**, och **lyssna** behörigheter. Du kan skapa eller ändra principer för delad åtkomst för namnområdet Händelsehubbar i Azure-portalen under den **konfigurera** för namnområdet Händelsehubbar. 

Om du vill uppdatera aktivitetsloggen loggen profilen om du vill inkludera strömning måste den användare som att ändra ha behörigheten ListKey på den Event Hubs auktoriseringsregeln. Namnområdet Händelsehubbar behöver inte finnas i samma prenumeration som den prenumeration som avger loggar, så länge som den användare som konfigurerar inställningen har lämplig RBAC åtkomst till båda prenumerationer.

### <a name="via-the-azure-portal"></a>Via Azure portal
1. Bläddra till den **aktivitetsloggen** avsnitt med hjälp av den **alla tjänster** sökning på vänster sida av portalen.
   
   ![Att välja aktivitetsloggen från listan över tjänster i portalen](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Välj den **exportera** längst upp på loggen.
   
   ![Exportera-knappen i portalen](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Observera att du har använt när du visar aktivitetsloggen i den föregående vyn filterinställningar har ingen inverkan på dina exportinställningar. Det är bara för att filtrera vad som visas när du bläddrar igenom din aktivitetsloggen i portalen.
3. I avsnittet som visas, väljer **alla regioner**. Välj inte särskilda regioner.
   
   ![Exportera avsnitt](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Om du väljer något annat än **alla regioner**, ska du missa viktiga händelser som du förväntar dig att ta emot. Aktivitetsloggen är en global logg (icke-nationella inställningar), så att de flesta händelser inte har en region som är kopplade till dem. 
   >

4. Välj **spara** dessa inställningar ska sparas. Inställningarna tillämpas omedelbart till din prenumeration.
5. Om du har flera prenumerationer Upprepa den här åtgärden och skicka alla data till samma händelsehubben.

### <a name="via-powershell-cmdlets"></a>Via PowerShell-cmdlets
Om det finns redan en logg-profil, måste du först ta bort den befintliga profilen för loggen och sedan skapa en ny profil i loggen.

1. Använd `Get-AzureRmLogProfile` att identifiera om det finns en logg-profil.  Om det finns en logg-profil, leta upp den *namn* egenskapen.
2. Använd `Remove-AzureRmLogProfile` att ta bort profilen logg med värdet från den *namn* egenskapen.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. Använd `Add-AzureRmLogProfile` att skapa en ny profil i loggen:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespaceName/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Via Azure CLI
Om det finns redan en logg-profil, måste du först ta bort den befintliga profilen för loggen och sedan skapa en ny profil i loggen.

1. Använd `az monitor log-profiles list` att identifiera om det finns en logg-profil.
2. Använd `az monitor log-profiles delete --name "<log profile name>` att ta bort profilen logg med värdet från den *namn* egenskapen.
3. Använd `az monitor log-profiles create` att skapa en ny profil i loggen:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Använda loggdata från Event Hubs
Schemat för aktivitetsloggen finns i [övervaka prenumeration aktivitet med Azure-aktivitetsloggen](monitoring-overview-activity-logs.md). Varje händelse är i en matris av JSON-blobbar som kallas *poster*.

## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitetsloggen till ett lagringskonto](monitoring-archive-activity-log.md)
* [Läs översikt över Azure-aktivitetsloggen](monitoring-overview-activity-logs.md)
* [Konfigurera en avisering baserat på en aktivitet händelselogg](insights-auditlog-to-webhook-email.md)

