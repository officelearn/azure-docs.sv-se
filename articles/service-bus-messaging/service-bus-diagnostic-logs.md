---
title: Azure Service Bus diagnostikloggar | Microsoft Docs
description: Lär dig hur du konfigurerar diagnostikloggar för Service Bus i Azure.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592426"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Aktivera diagnostikloggar för Service Bus

När du börjar använda Azure Service Bus namn rymden kanske du vill övervaka hur och när ditt namn område skapas, tas bort eller nås. Den här artikeln innehåller en översikt över alla operativa/diagnostiska loggar som är tillgängliga.

Azure Service Bus stöder för närvarande aktivitets-/drift loggar som utför avbildnings **hanterings åtgärder** i Azure Service Bus-namnrymden. Mer specifikt fångar dessa loggar åtgärds typen, inklusive skapande av kö, använda resurser och status för åtgärden.

## <a name="operational-logs-schema"></a>Schema för operativa loggar

Alla loggar lagras i JavaScript Object Notation (JSON)-format på följande 2-platser.

- **AzureActivity** – visar loggar från åtgärder/åtgärder som utförs mot ditt namn område på portalen eller genom Azure Resource Manager mallar distributioner.
- **AzureDiagnostics** – visar loggar från åtgärder/åtgärder som utförs mot ditt namn område med hjälp av API eller via hanterings klienter på språk-SDK: n.

I den operativa loggens JSON-strängar ingår element som anges i följande tabell:

| Namn | Beskrivning |
| ------- | ------- |
| ActivityId | Internt ID som används för att identifiera den angivna aktiviteten |
| eventName | Åtgärds namn |
| ResourceId | Resurs-ID för Azure Resource Manager |
| SubscriptionId | Prenumerations-ID |
| EventTimeString | Åtgärds tid |
| EventProperties | Egenskaper för åtgärd |
| Status | Åtgärdsstatus |
| Anropare | Uppringnings åtgärd (Azure Portal-eller hanterings klient) |
| Kategori | OperationalLogs |

Här är ett exempel på en fungerande logg-JSON-sträng:

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

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>Vilka händelser/åtgärder registreras i drifts loggar?

I åtgärds loggar samlas alla hanterings åtgärder som utförs på Azure Service Bus namn området. Data åtgärder samlas inte in på grund av den stora mängden data åtgärder som utförs på Azure Service Bus.

> [!NOTE]
> För att bättre spåra data åtgärder rekommenderar vi att du använder spårning på klient sidan.

Nedanstående hanterings åtgärder samlas in i drift loggar – 

| Omfång | Åtgärd|
|-------| -------- |
| Namnområde | <ul> <li> Skapa namnrymd</li> <li> Uppdatera namnrymd </li> <li> Ta bort namnrymd </li>  </ul> | 
| Kö | <ul> <li> Skapa kö</li> <li> Uppdatera kö</li> <li> Ta bort kö </li> </ul> | 
| Ämne | <ul> <li> Skapa ämne </li> <li> Uppdatera ämne </li> <li> Ta bort ämne </li> </ul> |
| Prenumeration | <ul> <li> Skapa en prenumeration </li> <li> Uppdatera prenumeration </li> <li> Ta bort prenumeration </li> </ul> |

> [!NOTE]
> För närvarande spåras inte **Läs** åtgärder i drifts loggarna.

## <a name="how-to-enable-operational-logs"></a>Hur aktiverar jag drift loggar?

Drift loggar är inaktiverade som standard. Utför följande steg för att aktivera diagnostikloggar:

1. I [Azure Portal](https://portal.azure.com)navigerar du till Azure Service Bus namn området och under **övervakning**klickar du på **Inställningar för diagnostik**.

   ![blad navigering till diagnostikloggar](./media/service-bus-diagnostic-logs/image1.png)

2. Konfigurera diagnostikinställningar genom att klicka på **Lägg till diagnostisk inställning** .  

   ![Aktivera diagnostikloggar](./media/service-bus-diagnostic-logs/image2.png)

3. Konfigurera diagnostikinställningar
   1. Ange ett **namn** för att identifiera diagnostikinställningar.
   2. Välj ett mål för diagnostiken.
      - Om du väljer **lagrings konto**måste du konfigurera det lagrings konto där diagnostiken ska lagras.
      - Om du väljer **Event Hub**måste du konfigurera lämplig händelsehubben där diagnostikinställningar ska strömmas till.
      - Om du väljer **Log Analytics**måste du ange vilken instans av Log Analytics diagnostiken ska skickas.
    3. Kontrol lera **OperationalLogs**.

       ![ändra status diagnostikloggar](./media/service-bus-diagnostic-logs/image3.png)

4. Klicka på **Save** (Spara).


Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade lagrings målet på bladet diagnostik- **loggar** .

Mer information om hur du konfigurerar diagnostik finns i [översikten över Azure Diagnostic-loggar](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Nästa steg

Se följande länkar om du vill veta mer om Service Bus:

* [Introduktion till Service Bus](service-bus-messaging-overview.md)
* [Kom igång med Service Bus](service-bus-dotnet-get-started-with-queues.md)
