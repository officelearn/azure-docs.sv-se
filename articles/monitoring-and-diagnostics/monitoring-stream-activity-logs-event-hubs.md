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
ms.openlocfilehash: 2cd3e2e471135242b52459abc231a0f3545e05e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Dataströmmen Azure aktivitetsloggen i Händelsehubbar
Den [ **Azure-aktivitetsloggen** ](monitoring-overview-activity-logs.md) kan strömmas i nära realtid för alla program med alternativet ”Export” inbyggda i portalen eller genom att aktivera Service Bus regel-ID i en logg profil via Azure PowerShell Cmdlet: ar eller Azure CLI.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Vad du kan göra med aktivitetsloggen och Händelsehubbar
Här följer några olika sätt som du kan använda den strömmande kapaciteten för aktivitetsloggen:

* **Dataström som loggning och telemetri tredjepartssystem** – över tiden, Händelsehubbar strömning och kommer att bli en mekanism för att skicka din aktivitetsloggen till Siem från tredje part och logga Analyslösningar.
* **Skapa en anpassad telemetri och loggning plattform** – om du redan har ett specialbyggt telemetri plattform eller är bara om du tänker skapa en mycket skalbar natur att publicera och prenumerera i Händelsehubbar kan du flexibelt infognings-aktivitetsloggen. [Finns Dan Rosanova guide med Händelsehubbar i en global skala telemetri platform här.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Strömning av aktivitetsloggen
Du kan aktivera strömning av aktivitetsloggen programmässigt eller via portalen. Oavsett hur du väljer ett Händelsehubbar namnområde och en princip för delad åtkomst för det namnområdet och en händelsehubb med det namnet 'insikter-loggar-operationallogs' har skapats i namnutrymmet när den första nya aktivitetsloggen händelsen inträffar. Om du inte har ett Händelsehubbar namnområde måste du först skapa en. Om du har tidigare strömmas aktivitetsloggen händelser till Händelsehubbar namnområdet, kan händelsehubben som skapades tidigare återanvändas. Princip för delad åtkomst definierar vilka behörigheter som har mekanismen för strömning. Idag strömning till en händelsehubb kräver **hantera**, **skicka**, och **lyssna** behörigheter. Du kan skapa eller ändra principer för Händelsehubbar namnområde delad åtkomst i Azure-portalen på fliken ”Konfigurera” för namnområdet. Om du vill uppdatera aktivitetsloggen loggen profilen om du vill inkludera strömning måste användaren att ändra ha behörigheten ListKey på den händelse hubb auktoriseringsregeln.

Namnområdet Händelsehubbar behöver inte finnas i samma prenumeration som prenumerationen avger loggar så länge som den användare som konfigurerar inställningen har lämplig RBAC åtkomst till båda prenumerationer.

### <a name="via-azure-portal"></a>Via Azure-portalen
1. Navigera till den **aktivitetsloggen** avsnitt som använder alla services search på vänster sida av portalen.
   
    ![Navigera till aktivitetsloggen i portalen](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Klicka på den **exportera** längst upp i aktivitetsloggen. Observera att du har använt när du visar aktivitetsloggen i den föregående vyn filterinställningar har ingen inverkan på dina exportinställningar – det är bara för att filtrera vad som visas när du bläddrar i aktivitetsloggen i portalen.
   
    ![Exportera-knappen i portalen](./media/monitoring-stream-activity-logs-event-hubs/export.png)
3. I avsnittet som visas, väljer **alla regioner**. Välj inte särskilda regioner.
   
    ![Exportera aktivitetslogg](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)
    
    > [!WARNING]
    > Välj bara ”alla regioner”. Annars kommer du missa viktiga händelser som du skulle ha Annars förväntas ta emot. Detta beror på att aktivitetsloggen är en global (icke-nationella inställningar) logg, så att de flesta händelser inte har en region som är kopplade till.
    >
    >
    
4. Klicka på **spara** dessa inställningar ska sparas. Inställningarna tillämpas omedelbart till din prenumeration.
5. Om du har flera prenumerationer måste du upprepa den här åtgärden och skicka alla data till samma händelsehubben.

### <a name="via-powershell-cmdlets"></a>Via PowerShell-Cmdlets
Om det finns redan en logg-profil, måste du först ta bort den här profilen.

1. Använd `Get-AzureRmLogProfile` att identifiera om det finns en logg-profil
2. I så fall använder `Remove-AzureRmLogProfile` att ta bort den.
3. Använd `Set-AzureRmLogProfile` att skapa en profil:

```powershell

Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

```

Regel-ID för Service Bus är en sträng med formatet: {service bus-resurs-ID} /authorizationrules/ {namn}, till exempel 

### <a name="via-azure-cli"></a>Via Azure CLI
Om det finns redan en logg-profil, måste du först ta bort den här profilen.

1. Använd `azure insights logprofile list` att identifiera om det finns en logg-profil
2. I så fall använder `azure insights logprofile delete` att ta bort den.
3. Använd `azure insights logprofile add` att skapa en profil:

```azurecli-interactive
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

Regel-ID för Service Bus är en sträng med formatet: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Hur jag för att använda loggdata från Event Hubs?
[Schemat för aktivitetsloggen finns här](monitoring-overview-activity-logs.md). Varje händelse är i en matris av JSON-blobbar som kallas ”poster”.

## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitetsloggen till ett lagringskonto](monitoring-archive-activity-log.md)
* [Läs översikt över Azure-aktivitetsloggen](monitoring-overview-activity-logs.md)
* [Konfigurera en avisering baserat på en aktivitet händelselogg](insights-auditlog-to-webhook-email.md)

