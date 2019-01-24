---
title: Diagnostikloggar för Azure Service Bus | Microsoft Docs
description: Lär dig hur du ställer in diagnostikloggar för Service Bus i Azure.
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
ms.openlocfilehash: 6ac36fed8f9d3220e6caf6eba94a62d1ec8372c7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846667"
---
# <a name="service-bus-diagnostic-logs"></a>Service Bus-diagnostikloggar

Du kan visa två typer av loggar för Azure Service Bus:
* **[Aktivitetsloggar](../azure-monitor/platform/activity-logs-overview.md)**. Dessa loggar innehåller information om åtgärder som utförs på ett jobb. Loggarna är alltid aktiverat.
* **[Diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md)**. Du kan konfigurera diagnostikloggar mer omfattande information om allt som händer i ett jobb. Diagnostikloggar cover aktiviteter från den tidpunkt då jobbet skapades tills jobbet tas bort, inklusive uppdateringar och aktiviteter som inträffar när jobbet körs.

## <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är inaktiverade som standard. Utför följande steg om du vill aktivera diagnostikloggar:

1.  I den [Azure-portalen](https://portal.azure.com)under **övervakning + hantering**, klickar du på **diagnostikloggar**.

    ![bladnavigering till diagnostikloggar](./media/service-bus-diagnostic-logs/image1.png)

2. Klicka på den resurs som du vill övervaka.  

3.  Klicka på **Slå på diagnostik**.

    ![Aktivera diagnostikloggar](./media/service-bus-diagnostic-logs/image2.png)

4.  För **Status**, klickar du på **på**.

    ![Ändra status för diagnostikloggar](./media/service-bus-diagnostic-logs/image3.png)

5.  Ange arkivet målet som du vill. ett lagringskonto, en event hub eller Azure Log Analytics.

6.  Spara de nya diagnostikinställningarna för.

Nya inställningarna träder i kraft i cirka 10 minuter. Efter det loggarna visas i den konfigurerade mål för arkivering, på den **diagnostikloggar** bladet.

Mer information om hur du konfigurerar diagnostik finns i den [översikt över Azure diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Diagnostikloggar schema

Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post innehåller strängfält som använder formatet som beskrivs i följande avsnitt.

## <a name="operational-logs-schema"></a>Driftloggar schema

Loggar in på **OperationalLogs** kategori avbilda vad som händer under Service Bus-åtgärder. Mer specifikt avbilda loggarna åtgärdstypen, inklusive kön har skapats, resurser som används och status för åtgärden.

Arbetsloggen JSON-strängar är uppgifter som anges i tabellen nedan:

Namn | Beskrivning
------- | -------
Aktivitets-ID | Internt ID som användes för spårning
EventName | Åtgärdsnamn           
resourceId | Azure Resource Manager-resurs-ID
SubscriptionId | Prenumerations-ID:t
EventTimeString | Åtgärden tid
EventProperties | Åtgärden egenskaper
Status | Åtgärdsstatus
Anropare | Anroparen av åtgärden (Azure-portalen eller hantering klient)
category | OperationalLogs

Här är ett exempel på en arbetsloggen JSON-sträng:

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

Se följande länkar för att lära dig mer om Service Bus:

* [Introduktion till Service Bus](service-bus-messaging-overview.md)
* [Kom igång med Service Bus](service-bus-dotnet-get-started-with-queues.md)
