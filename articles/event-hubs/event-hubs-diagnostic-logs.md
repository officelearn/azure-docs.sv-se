---
title: Konfigurera diagnostikloggar – Azure Event Hub | Microsoft-dokument
description: Lär dig hur du konfigurerar aktivitetsloggar och diagnostikloggar för händelsehubbar i Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 68aa62ad34f8db531d439a581ef024862da0f90c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162318"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Konfigurera diagnostikloggar för en Azure-händelsehubb

Du kan visa två typer av loggar för Azure Event Hubs:

* **[Aktivitetsloggar](../azure-monitor/platform/platform-logs-overview.md)**: Dessa loggar har information om åtgärder som utförs på ett jobb. Loggarna är alltid aktiverade.
* **[Diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md)**: Du kan konfigurera diagnostikloggar för en rikare vy över allt som händer med ett jobb. Diagnostikloggar täcker aktiviteter från det att jobbet skapas tills jobbet tas bort, inklusive uppdateringar och aktiviteter som inträffar medan jobbet körs.

## <a name="enable-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är inaktiverade som standard. Så här aktiverar du diagnostikloggar:

1.  Klicka på **Diagnostikloggar**under **Övervakning + hantering**i [Azure-portalen](https://portal.azure.com).

    ![Navigering i fönster till diagnostikloggar](./media/event-hubs-diagnostic-logs/image1.png)

2.  Klicka på den resurs som du vill övervaka.

3.  Klicka på **Slå på diagnostik**.

    ![Aktivera diagnostikloggar](./media/event-hubs-diagnostic-logs/image2.png)

4.  För **Status** **klickar**du på .

    ![Ändra status för diagnostikloggar](./media/event-hubs-diagnostic-logs/image3.png)

5.  Ange önskat arkivmål. till exempel ett lagringskonto, en händelsenav eller Azure Monitor-loggar.

6.  Spara de nya diagnostikinställningarna.

Nya inställningar träder i kraft om cirka 10 minuter. Därefter visas loggar i det konfigurerade arkiveringsmålet i fönstret **Diagnostikloggar.**

Mer information om hur du konfigurerar diagnostik finns i [översikten över Diagnostikloggar i Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Kategorier för diagnostikloggar

Event Hubs samlar in diagnostikloggar för två kategorier:

* **Arkivloggar:** loggar relaterade till Event Hubs arkiv, särskilt loggar relaterade till arkivfel.
* **Operativa loggar:** information om vad som händer under event hubs-åtgärder, särskilt operationstypen, inklusive skapande av händelsehubben, resurser som används och åtgärdens status.

## <a name="diagnostic-logs-schema"></a>Schema för diagnostikloggar

Alla loggar lagras i JSON-format (JavaScript Object Notation). Varje post har strängfält som använder det format som beskrivs i följande avsnitt.

### <a name="archive-logs-schema"></a>Schema för arkivloggar

JSON-strängar för arkivlogg innehåller element som anges i följande tabell:

Namn | Beskrivning
------- | -------
TaskName (3500 | Beskrivning av aktiviteten som misslyckades.
ActivityId (på)ActivityId) | Internt ID, som används för spårning.
trackingId (trackingId) | Internt ID, som används för spårning.
resourceId | Resurs-ID för Azure Resource Manager.
eventHub | Fullständigt namn på eventhubb (innehåller namnområdesnamn).
Partitionid | Händelsehubbens partition skrivs till.
arkivSteg | ArkivFlushWriter
startTime | Fel starttid.
Misslyckanden | Antalet misslyckade gånger inträffade.
durationInSeconds | Varaktighet av misslyckande.
meddelande | Felmeddelande.
category | Arkivbild

Följande kod är ett exempel på en JSON-sträng för arkivloggen:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Schema för driftloggar

JSON-strängar med driftloggen innehåller element som anges i följande tabell:

Namn | Beskrivning
------- | -------
ActivityId (på)ActivityId) | Internt ID, används för att spåra syfte.
EventName | Åtgärdsnamn.  
resourceId | Resurs-ID för Azure Resource Manager.
SubscriptionId | Prenumerations-ID.
EventTimeString | Drifttid.
EventProperties | Egenskaper för drift.
Status | Status för åtgärden.
Anropare | Anropare av åtgärden (Azure portal eller hanteringsklient).
category | OperationalLogs

Följande kod är ett exempel på en fungerande logg JSON-sträng:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Nästa steg
- [Introduktion till eventhubbar](event-hubs-what-is-event-hubs.md)
- [Event Hubs API-översikt](event-hubs-api-overview.md)
- Kom igång med händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
