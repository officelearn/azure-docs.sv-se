---
title: Diagnostikloggar för Azure Service Bus | Microsoft-dokument
description: Den här artikeln innehåller en översikt över alla drift- och diagnostikloggar som är tillgängliga för Azure Service Bus.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: c8eba538a7015648611e6054ce85b381dcfc9105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761006"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Aktivera diagnostikloggar för Service Bus

När du börjar använda namnområdet för Azure Service Bus kanske du vill övervaka hur och när namnområdet skapas, tas bort eller används. Den här artikeln innehåller en översikt över alla operativa och diagnostikloggar som är tillgängliga.

Azure Service Bus stöder för närvarande aktivitets- och driftloggar, som samlar *in hanteringsåtgärder* som utförs på Azure Service Bus-namnområdet. Dessa loggar samlar in åtgärdstypen, inklusive skapande av köer, använda resurser och åtgärdens status.

## <a name="operational-logs-schema"></a>Schema för driftloggar

Alla loggar lagras i JSON-format (JavaScript Object Notation) på följande två platser:

- **AzureActivity**: Visar loggar från åtgärder och åtgärder som utförs mot ditt namnområde i Azure-portalen eller via Azure Resource Manager-malldistributioner.
- **AzureDiagnostics**: Visar loggar från åtgärder och åtgärder som utförs mot ditt namnområde med hjälp av API: et eller genom hanteringsklienter på språket SDK.

JSON-strängar med driftloggen innehåller de element som anges i följande tabell:

| Namn | Beskrivning |
| ------- | ------- |
| ActivityId (på)ActivityId) | Internt ID, som används för att identifiera den angivna aktiviteten |
| EventName | Åtgärdsnamn |
| ResourceId | Resurs-ID för Azure Resource Manager |
| SubscriptionId | Prenumerations-ID:t |
| EventTimeString | Drifttid |
| EventProperties | Egenskaper för operation |
| Status | Åtgärdsstatus |
| Anropare | Anropare av verksamheten (Azure-portalen eller hanteringsklienten) |
| Kategori | OperationalLogs |

Här är ett exempel på en fungerande logg JSON sträng:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Händelser och åtgärder som fångas upp i driftloggar

Driftloggar samlar in alla hanteringsåtgärder som utförs på namnområdet Azure Service Bus. Dataåtgärder samlas inte in på grund av den stora mängden dataåtgärder som utförs på Azure Service Bus.

> [!NOTE]
> För att hjälpa dig att bättre spåra dataåtgärder rekommenderar vi att du använder spårning på klientsidan.

Följande hanteringsåtgärder fångas in i driftloggar: 

| Omfång | Åtgärd|
|-------| -------- |
| Namnområde | <ul> <li> Skapa namnområde</li> <li> Uppdatera namnområde </li> <li> Ta bort namnområde </li>  </ul> | 
| Kö | <ul> <li> Skapa kö</li> <li> Uppdatera kö</li> <li> Ta bort kö </li> </ul> | 
| Hjälpavsnitt | <ul> <li> Skapa ämne </li> <li> Uppdatera ämne </li> <li> Ta bort ämne </li> </ul> |
| Prenumeration | <ul> <li> Skapa en prenumeration </li> <li> Uppdatera prenumeration </li> <li> Ta bort prenumeration </li> </ul> |

> [!NOTE]
> För närvarande spåras inte *Läs-åtgärder* i driftloggarna.

## <a name="enable-operational-logs"></a>Aktivera driftloggar

Driftloggar är inaktiverade som standard. Så här aktiverar du diagnostikloggar:

1. Gå till namnområdet Azure Service Bus i [Azure-portalen](https://portal.azure.com)och välj sedan **Diagnostikinställningar**under **Övervakning**.

   ![Länken "Diagnostikinställningar"](./media/service-bus-diagnostic-logs/image1.png)

1. I fönstret **Inställningar för diagnostik** väljer du Lägg till **diagnostikinställning**.  

   ![Länken "Lägg till diagnostikinställning"](./media/service-bus-diagnostic-logs/image2.png)

1. Konfigurera diagnostikinställningarna genom att göra följande:

   a. Ange ett namn för diagnostikinställningarna i rutan **Namn.**  

   b. Välj en av följande tre mål för diagnostikloggarna:  
   - Om du väljer **Arkiv till ett lagringskonto**måste du konfigurera lagringskontot där diagnostikloggarna ska lagras.  
   - Om du väljer **Stream till en händelsehubb**måste du konfigurera händelsehubben som du vill strömma diagnostikloggarna till.
   - Om du väljer **Skicka till Logganalys**måste du ange vilken instans av Log Analytics diagnostiken ska skickas till.  

   c. Markera kryssrutan **OperationalLogs.**

    ![Fönstret "Diagnostikinställningar"](./media/service-bus-diagnostic-logs/image3.png)

1. Välj **Spara**.

De nya inställningarna börjar gälla om cirka 10 minuter. Loggarna visas i det konfigurerade arkiveringsmålet i fönstret **Diagnostikloggar.**

Mer information om hur du konfigurerar diagnostikinställningar finns i [översikten över Azure-diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus finns i:

* [Introduktion till servicebuss](service-bus-messaging-overview.md)
* [Kom igång med Service Bus](service-bus-dotnet-get-started-with-queues.md)
