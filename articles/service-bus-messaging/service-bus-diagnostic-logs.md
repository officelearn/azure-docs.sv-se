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
ms.openlocfilehash: 6443cb727573645792a4e6c929b80c3406d72025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261810"
---
# <a name="service-bus-diagnostic-logs"></a>Service Bus diagnostikloggar

Du kan visa två typer av loggar för Azure Service Bus:
* **[Aktivitets loggar](../azure-monitor/platform/activity-logs-overview.md)** . Dessa loggar innehåller information om åtgärder som utförts i ett jobb. Loggarna är alltid aktiverat.
* **[Diagnostikloggar](../azure-monitor/platform/resource-logs-overview.md)** . Du kan konfigurera diagnostikloggar för mer information om allt som händer i ett jobb. Diagnostikloggar cover aktiviteter från den tidpunkt då jobbet skapades tills jobbet tas bort, inklusive uppdateringar och aktiviteter som inträffar när jobbet körs.

## <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är inaktiverade som standard. Utför följande steg för att aktivera diagnostikloggar:

1.  I den [Azure-portalen](https://portal.azure.com)under **övervakning + hantering**, klickar du på **diagnostikloggar**.

    ![blad navigering till diagnostikloggar](./media/service-bus-diagnostic-logs/image1.png)

2. Klicka på den resurs som du vill övervaka.  

3.  Klicka på **Slå på diagnostik**.

    ![Aktivera diagnostikloggar](./media/service-bus-diagnostic-logs/image2.png)

4.  För **Status**, klickar du på **på**.

    ![ändra status diagnostikloggar](./media/service-bus-diagnostic-logs/image3.png)

5.  Ange det mål för arkivet som du vill använda. till exempel ett lagrings konto, en Event Hub-eller Azure Monitor-loggar.

6.  Spara de nya diagnostikinställningarna för.

Nya inställningarna träder i kraft i cirka 10 minuter. Därefter visas loggar i det konfigurerade lagrings målet på bladet diagnostik- **loggar** .

Mer information om hur du konfigurerar diagnostik finns i den [översikt över Azure diagnostikloggar](../azure-monitor/platform/resource-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Diagnostikloggar schema

Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post har sträng fält som använder det format som beskrivs i följande avsnitt.

## <a name="operational-logs-schema"></a>Driftloggar schema

Loggar i **OperationalLogs** kategori fångar vad som händer under Service Bus åtgärder. Mer specifikt fångar dessa loggar åtgärds typen, inklusive skapande av kö, använda resurser och status för åtgärden.

Arbetsloggen JSON-strängar är uppgifter som anges i tabellen nedan:

Namn | Beskrivning
------- | -------
Aktivitets-ID | Internt ID som används för spårning
EventName | Åtgärdens namn           
resourceId | Resurs-ID för Azure Resource Manager
SubscriptionId | Prenumerations-ID:t
EventTimeString | Åtgärds tid
EventProperties | Egenskaper för åtgärd
State | Åtgärdsstatus
Anropare | Uppringnings åtgärd (Azure Portal-eller hanterings klient)
category | OperationalLogs

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

## <a name="next-steps"></a>Nästa steg

Se följande länkar om du vill veta mer om Service Bus:

* [Introduktion till Service Bus](service-bus-messaging-overview.md)
* [Kom igång med Service Bus](service-bus-dotnet-get-started-with-queues.md)
