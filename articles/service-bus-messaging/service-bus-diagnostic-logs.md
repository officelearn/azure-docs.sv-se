---
title: Azure Service Bus-diagnostikloggar | Microsoft Docs
description: Lär dig hur du ställer in diagnostikloggar för Service Bus i Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: banisadr
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: 4ce724adc9ca167634be9a0b7137b6a3d54211bf
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
ms.locfileid: "29122194"
---
# <a name="service-bus-diagnostic-logs"></a>Service Bus diagnostikloggar

Du kan visa två typer av loggar för Azure Service Bus:
* **[Aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Dessa loggar innehåller information om åtgärder som utförs på ett annat jobb. Loggarna är alltid aktiverat.
* **[Diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Du kan konfigurera diagnostiska loggar för bättre information om allt som händer i ett jobb. Diagnostikloggar omfattar aktiviteter från den tidpunkt då jobbet skapades tills jobbet tas bort, inklusive uppdateringar och aktiviteter som inträffar när jobbet körs.

## <a name="turn-on-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostik loggar är inaktiverade som standard. Utför följande steg om du vill aktivera diagnostikloggar:

1.  I den [Azure-portalen](https://portal.azure.com)under **övervakning + Management**, klickar du på **diagnostik loggar**.

    ![Bladet navigering till diagnostikloggar](./media/service-bus-diagnostic-logs/image1.png)

2. Klicka på resursen som du vill övervaka.  

3.  Klicka på **aktivera diagnostiken**.

    ![Aktivera diagnostikloggar](./media/service-bus-diagnostic-logs/image2.png)

4.  För **Status**, klickar du på **på**.

    ![Ändra status diagnostikloggar](./media/service-bus-diagnostic-logs/image3.png)

5.  Ange Arkiv-mål som du vill. ett lagringskonto, en händelsehubb eller Azure logganalys.

6.  Spara de nya diagnostikinställningarna för.

Nya inställningar börjar gälla i cirka 10 minuter. Efter det loggarna visas i det konfigurerade arkivering målet på den **diagnostik loggar** bladet.

Mer information om hur du konfigurerar diagnostik finns i [översikt över Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Diagnostikloggar schema

Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post innehåller strängfält som använder det format som beskrivs i följande avsnitt.

## <a name="operational-logs-schema"></a>Operativa loggar schema

Loggar in på **OperationalLogs** kategori avbilda vad som händer under Service Bus-åtgärder. Dessa loggar avbilda specifikt typ av åtgärd, inklusive kön skapas, resurser och status för åtgärden.

Arbetsloggen JSON strängar innehålla element som visas i följande tabell:

Namn | Beskrivning
------- | -------
ActivityId | Internt ID som används för spårning
EventName | Åtgärdsnamn           
resourceId | Azure Resource Manager-resurs-ID
SubscriptionId | Prenumerations-ID:t
EventTimeString | Åtgärden tid
EventProperties | Åtgärden egenskaper
Status | Åtgärdsstatus
Anropare | Anroparen av åtgärden (Azure portal eller management-klienten)
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
