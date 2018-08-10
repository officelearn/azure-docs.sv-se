---
title: Diagnostikloggar för Azure Event Hubs | Microsoft Docs
description: Lär dig hur du ställer in diagnostikloggar för händelsehubbar i Azure.
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
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: d4ed53cf1f0d21e488631434b60309602169b59a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004658"
---
# <a name="event-hubs-diagnostic-logs"></a>Event Hubs-diagnostikloggar

Du kan visa två typer av loggar för Azure Event Hubs:

* **[Aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**: de här loggarna har information om åtgärder som utförs på ett jobb. Loggarna är alltid aktiverat.
* **[Diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**: du kan konfigurera diagnostikloggar för en mer omfattande vy av allt som händer med ett jobb. Diagnostikloggar cover aktiviteter från den tidpunkt då jobbet skapades tills jobbet tas bort, inklusive uppdateringar och aktiviteter som inträffar när jobbet körs.

## <a name="enable-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är inaktiverade som standard. Följ dessa steg om du vill aktivera diagnostikloggar:

1.  I den [Azure-portalen](https://portal.azure.com)under **övervakning + hantering**, klickar du på **diagnostikloggar**.

    ![Fönstret navigering till diagnostikloggar](./media/event-hubs-diagnostic-logs/image1.png)

2.  Klicka på den resurs som du vill övervaka.

3.  Klicka på **Slå på diagnostik**.

    ![Aktivera diagnostikloggar](./media/event-hubs-diagnostic-logs/image2.png)

4.  För **Status**, klickar du på **på**.

    ![Ändra status för diagnostikloggar](./media/event-hubs-diagnostic-logs/image3.png)

5.  Ange arkivet målet som du vill. ett lagringskonto, en event hub eller Azure Log Analytics.

6.  Spara de nya diagnostikinställningarna för.

Nya inställningarna träder i kraft i cirka 10 minuter. Efter det loggar visas i den konfigurerade mål för arkivering, i den **diagnostikloggar** fönstret.

Mer information om hur du konfigurerar diagnostik finns i den [översikt över Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-categories"></a>Diagnostikloggar kategorier

Event Hubs samlar in diagnostikloggar för två kategorier:

* **Arkivera loggar**: relaterade till Event Hubs-Arkiv, mer specifikt, relaterade till Arkivera fel.
* **Driftloggar**: information om vad som händer under Event Hubs-åtgärder, mer specifikt åtgärdstypen, inklusive skapande av händelsehubb, resurser som används och status för åtgärden.

## <a name="diagnostic-logs-schema"></a>Diagnostikloggar schema

Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post innehåller strängfält som använder formatet som beskrivs i följande avsnitt.

### <a name="archive-logs-schema"></a>Arkivera loggar schema

Arkivera loggen JSON-strängar är uppgifter som anges i tabellen nedan:

Namn | Beskrivning
------- | -------
Aktivitetsnamn | Beskrivning av den aktivitet som misslyckats.
Aktivitets-ID | Internt ID som används för spårning.
spårnings-ID | Internt ID som används för spårning.
resourceId | Azure Resource Manager-resurs-ID.
eventHub | Händelsehubb fullständigt namn (inklusive namnområdesnamn).
Partitions-ID | Skrivs till Event Hubs-partitionen.
archiveStep | ArchiveFlushWriter
startTime | Starttid för fel.
fel | Antal gånger som ett fel inträffade vid.
durationInSeconds | Varaktighet för felet.
meddelande | Felmeddelande.
category | ArchiveLogs

Följande kod är ett exempel på en arkivera loggen JSON-sträng:

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

### <a name="operational-logs-schema"></a>Driftloggar schema

Arbetsloggen JSON-strängar är uppgifter som anges i tabellen nedan:

Namn | Beskrivning
------- | -------
Aktivitets-ID | Internt ID som används för att spåra användning.
EventName | Åtgärdens namn.  
resourceId | Azure Resource Manager-resurs-ID.
SubscriptionId | Prenumerations-ID.
EventTimeString | Tiden för åtgärden.
EventProperties | Åtgärden egenskaper.
Status | Åtgärdsstatus.
Anropare | Anroparen av åtgärden (Azure-portalen eller hantering klient).
category | OperationalLogs

Följande kod är ett exempel på en arbetsloggen JSON-sträng:

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
* [Introduktion till Event Hubs](event-hubs-what-is-event-hubs.md)
* [Event Hubs API-översikt](event-hubs-api-overview.md)
* [Kom igång med Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
