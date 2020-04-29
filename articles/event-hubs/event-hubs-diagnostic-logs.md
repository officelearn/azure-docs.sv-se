---
title: Konfigurera diagnostikloggar – Azure Event Hub | Microsoft Docs
description: Lär dig hur du konfigurerar aktivitets loggar och diagnostikloggar för Event Hub i Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162318"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Konfigurera diagnostikloggar för en Azure-händelsehubb

Du kan visa två typer av loggar för Azure Event Hubs:

* **[Aktivitets loggar](../azure-monitor/platform/platform-logs-overview.md)**: dessa loggar innehåller information om åtgärder som utförts i ett jobb. Loggarna är alltid aktiverade.
* **[Diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md)**: du kan konfigurera diagnostikloggar för en mer omfattande vy av allt som händer med ett jobb. Diagnostikloggar behandlar aktiviteter från den tidpunkt då jobbet skapas tills jobbet tas bort, inklusive uppdateringar och aktiviteter som inträffar när jobbet körs.

## <a name="enable-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är inaktiverade som standard. Följ dessa steg om du vill aktivera diagnostikloggar:

1.  Klicka **på diagnostikloggar under** **övervakning och hantering**i [Azure Portal](https://portal.azure.com).

    ![Fönster navigering till diagnostikloggar](./media/event-hubs-diagnostic-logs/image1.png)

2.  Klicka på den resurs som du vill övervaka.

3.  Klicka på **Slå på diagnostik**.

    ![Aktivera diagnostikloggar](./media/event-hubs-diagnostic-logs/image2.png)

4.  För **status**klickar du på **på**.

    ![Ändra status för diagnostikloggar](./media/event-hubs-diagnostic-logs/image3.png)

5.  Ange det mål för arkivet som du vill använda. till exempel ett lagrings konto, en Event Hub-eller Azure Monitor-loggar.

6.  Spara de nya diagnostikinställningar.

Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade lagrings målet i fönstret **diagnostikloggar** .

Mer information om hur du konfigurerar diagnostik finns i [översikten över Azure Diagnostic-loggar](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Diagnostiska loggnings kategorier

Event Hubs fångar diagnostikloggar för två kategorier:

* **Arkiv loggar**: loggar som är relaterade till Event Hubs Arkiv, särskilt loggar som rör Arkiv fel.
* **Drift loggar**: information om vad som händer under Event Hubs åtgärder, närmare bestämt åtgärds typen, inklusive skapande av händelsehubben, använda resurser och status för åtgärden.

## <a name="diagnostic-logs-schema"></a>Schema för diagnostiska loggar

Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post innehåller sträng fält som använder det format som beskrivs i följande avsnitt.

### <a name="archive-logs-schema"></a>Schema för Arkiv loggar

Arkiv loggens JSON-strängar innehåller element som anges i följande tabell:

Name | Beskrivning
------- | -------
/TN | Beskrivning av uppgiften som misslyckades.
ActivityId | Internt ID som används för spårning.
trackingId | Internt ID som används för spårning.
resourceId | Azure Resource Manager resurs-ID.
eventHub | Event Hub fullständigt namn (innehåller namn rymds namn).
Partition | Den Event Hub-partition som skrivs till.
archiveStep | ArchiveFlushWriter
startTime | Start tid för startfel.
fel | Antal inträffade tids fel.
durationInSeconds | Varaktighet för felet.
meddelande | Fel meddelande.
category | ArchiveLogs

Följande kod är ett exempel på en logg-JSON-sträng för Arkiv logg:

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

### <a name="operational-logs-schema"></a>Schema för operativa loggar

I den operativa loggens JSON-strängar ingår element som anges i följande tabell:

Name | Beskrivning
------- | -------
ActivityId | Internt ID som används för att spåra syfte.
EventName | Åtgärdsnamn.  
resourceId | Azure Resource Manager resurs-ID.
SubscriptionId | Prenumerations-ID.
EventTimeString | Åtgärds tid.
EventProperties | Egenskaper för åtgärd.
Status | Åtgärds status.
Anropare | Uppringnings åtgärd (Azure Portal-eller hanterings klient).
category | OperationalLogs

Följande kod är ett exempel på en fungerande logg-JSON-sträng:

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
- [Introduktion till Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs API-översikt](event-hubs-api-overview.md)
- Kom igång med händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
