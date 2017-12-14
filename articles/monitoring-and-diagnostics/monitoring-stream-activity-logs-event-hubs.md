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
ms.date: 6/06/2017
ms.author: johnkem
ms.openlocfilehash: f0e507cf2804edbcdd6c87f47b30defbc6a5eb94
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Dataströmmen Azure aktivitetsloggen i Händelsehubbar
Den [ **Azure-aktivitetsloggen** ](monitoring-overview-activity-logs.md) kan strömmas i nära realtid för alla program med alternativet ”Export” inbyggda i portalen eller genom att aktivera Service Bus regel-Id i en logg profil via Azure PowerShell Cmdlet: ar eller Azure CLI.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Vad du kan göra med aktivitetsloggen och Händelsehubbar
Här följer några olika sätt som du kan använda den strömmande kapaciteten för aktivitetsloggen:

* **Dataström som loggning och telemetri tredjepartssystem** – över tiden, Händelsehubbar strömning och kommer att bli en mekanism för att skicka din aktivitetsloggen till Siem från tredje part och logga Analyslösningar.
* **Skapa en anpassad telemetri och loggning plattform** – om du redan har ett specialbyggt telemetri plattform eller är bara om du tänker skapa en mycket skalbar natur att publicera och prenumerera i Händelsehubbar kan du flexibelt infognings-aktivitetsloggen. [Finns Dan Rosanova guide med Händelsehubbar i en global skala telemetri platform här.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Strömning av aktivitetsloggen
Du kan aktivera strömning av aktivitetsloggen programmässigt eller via portalen. Oavsett hur du väljer en Service Bus-Namespace och en princip för delad åtkomst för det namnområdet och en Händelsehubb skapas i namnutrymmet när den första nya aktivitetsloggen händelsen inträffar. Om du inte har en Service Bus-Namespace måste du först skapa en. Om du har tidigare strömmas aktivitetsloggen händelser till den här Service Bus-Namespace, kan Händelsehubben som skapades tidigare återanvändas. Princip för delad åtkomst definierar vilka behörigheter som har mekanismen för strömning. Idag strömning i en Händelsehubbar kräver **hantera**, **skicka**, och **lyssna** behörigheter. Du kan skapa eller ändra principer för Service Bus Namespace delad åtkomst i Azure-portalen på fliken ”Konfigurera” för ditt Service Bus-Namespace. Om du vill uppdatera aktivitetsloggen loggen profilen om du vill inkludera strömning måste användaren att ändra ha behörigheten ListKey på den Service Bus-auktoriseringsregeln.

Namnområdet för service bus eller händelse hubb behöver inte finnas i samma prenumeration som prenumerationen avger loggar så länge som den användare som konfigurerar inställningen har lämplig RBAC åtkomst till båda prenumerationer.

### <a name="via-azure-portal"></a>Via Azure-portalen
1. Navigera till den **aktivitetsloggen** blad med hjälp av menyn till vänster i portalen.
   
    ![Navigera till aktivitetsloggen i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Klicka på den **exportera** längst upp på bladet.
   
    ![Exportera-knappen i portalen](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Du kan välja de regioner där du vill dataströmmen händelser och Service Bus-Namespace som du vill att en Händelsehubb skapas för direktuppspelning av dessa händelser i bladet som visas.
   
    ![Exportera aktivitetsloggen bladet](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Klicka på **spara** dessa inställningar ska sparas. Inställningarna tillämpas omedelbart till din prenumeration.

### <a name="via-powershell-cmdlets"></a>Via PowerShell-Cmdlets
Om det finns redan en logg-profil, måste du först ta bort den här profilen.

1. Använd `Get-AzureRmLogProfile` att identifiera om det finns en logg-profil
2. I så fall använder `Remove-AzureRmLogProfile` att ta bort den.
3. Använd `Set-AzureRmLogProfile` att skapa en profil:

```
Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

Regel-ID för Service Bus är en sträng med formatet: {service bus-resurs-ID} /authorizationrules/ {namn}, till exempel 

### <a name="via-azure-cli"></a>Via Azure CLI
Om det finns redan en logg-profil, måste du först ta bort den här profilen.

1. Använd `azure insights logprofile list` att identifiera om det finns en logg-profil
2. I så fall använder `azure insights logprofile delete` att ta bort den.
3. Använd `azure insights logprofile add` att skapa en profil:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

Regel-ID för Service Bus är en sträng med formatet: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Hur jag för att använda loggdata från Event Hubs?
[Schemat för aktivitetsloggen finns här](monitoring-overview-activity-logs.md). Varje händelse är i en matris av JSON-blobbar som kallas ”poster”.

## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitetsloggen till ett lagringskonto](monitoring-archive-activity-log.md)
* [Läs översikt över Azure-aktivitetsloggen](monitoring-overview-activity-logs.md)
* [Konfigurera en avisering baserat på en aktivitet händelselogg](insights-auditlog-to-webhook-email.md)

