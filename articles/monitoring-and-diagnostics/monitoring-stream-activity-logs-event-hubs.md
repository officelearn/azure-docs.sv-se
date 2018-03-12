---
title: "Strömma Azure aktivitetsloggen i Händelsehubbar | Microsoft Docs"
description: "Lär dig mer om att strömma Azure-aktivitetsloggen i Händelsehubbar."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: johnkem
ms.openlocfilehash: 4b2d9866839f943f65beb271d44bc691441b0fb3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
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
Om det finns redan en logg-profil, måste du först ta bort den här profilen.

1. Använd `Get-AzureRmLogProfile` att identifiera om det finns en logg-profil.
2. I så fall använder `Remove-AzureRmLogProfile` att ta bort den.
3. Använd `Set-AzureRmLogProfile` att skapa en profil:

   ```powershell

   Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

   ```

Regel-ID för Service Bus är en sträng med formatet: `{service bus resource ID}/authorizationrules/{key name}`. 

### <a name="via-azure-cli"></a>Via Azure CLI
Om det finns redan en logg-profil, måste du först ta bort den här profilen.

1. Använd `azure insights logprofile list` att identifiera om det finns en logg-profil.
2. I så fall använder `azure insights logprofile delete` att ta bort den.
3. Använd `azure insights logprofile add` att skapa en profil:

   ```azurecli-interactive
   azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
   ```

Regel-ID för Service Bus är en sträng med formatet: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="consume-the-log-data-from-event-hubs"></a>Använda loggdata från Event Hubs
Schemat för aktivitetsloggen finns i [övervaka prenumeration aktivitet med Azure-aktivitetsloggen](monitoring-overview-activity-logs.md). Varje händelse är i en matris av JSON-blobbar som kallas *poster*.

## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitetsloggen till ett lagringskonto](monitoring-archive-activity-log.md)
* [Läs översikt över Azure-aktivitetsloggen](monitoring-overview-activity-logs.md)
* [Konfigurera en avisering baserat på en aktivitet händelselogg](insights-auditlog-to-webhook-email.md)

