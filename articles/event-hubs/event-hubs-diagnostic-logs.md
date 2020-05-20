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
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: b9dcf35b3ea178894a0387e650b6814c0f920926
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649794"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Konfigurera diagnostikloggar för en Azure-händelsehubb

Du kan visa två typer av loggar för Azure Event Hubs:

* **[Aktivitets loggar](../azure-monitor/platform/platform-logs-overview.md)**: loggarna innehåller information om åtgärder som utförs i ett jobb. Loggarna är alltid aktiverade. Du kan se aktivitets logg poster genom att välja **aktivitets logg** i det vänstra fönstret för ditt Event Hub-namnområde i Azure Portal. Exempel: "skapa eller uppdatera namn område", "skapa eller uppdatera Event Hub".

    ![Aktivitets logg för ett Event Hubs-namnområde](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md)**: diagnostikloggar ger mer utförlig information om åtgärder och åtgärder som utförs mot ditt namn område med hjälp av API: et, eller via hanterings klienter på språk-SDK: n. 
    
    I följande avsnitt visas hur du aktiverar diagnostikloggar för ett Event Hubs namn område.

## <a name="enable-diagnostic-logs"></a>Aktivera diagnostikloggar
Diagnostikloggar är inaktiverade som standard. Följ dessa steg om du vill aktivera diagnostikloggar:

1.  I [Azure Portal](https://portal.azure.com)navigerar du till Event Hubs namn området. 
2. Välj **diagnostikinställningar** under **övervakning** i den vänstra rutan och välj sedan **+ Lägg till diagnostisk inställning**. 

    ![Sidan diagnostikinställningar – Lägg till diagnostisk inställning](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. I avsnittet **kategori Detaljer** väljer du de **typer av diagnostikloggar** som du vill aktivera. Du hittar mer information om dessa kategorier längre fram i den här artikeln. 
5. I avsnittet **mål information** anger du det mål (mål) som du vill använda. till exempel ett lagrings konto, en Event Hub eller en Log Analytics-arbetsyta.

    ![Sidan Lägg till diagnostikinställningar](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Spara inställningarna för diagnostik genom att välja **Spara** i verktygsfältet.

    Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade lagrings målet i fönstret **diagnostikloggar** .

    Mer information om hur du konfigurerar diagnostik finns i [översikten över Azure Diagnostic-loggar](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Diagnostiska loggnings kategorier

Event Hubs fångar diagnostikloggar för följande kategorier:

| Kategori | Description | 
| -------- | ----------- | 
| Arkiv loggar | Samlar in information om [Event Hubs avbildnings](event-hubs-capture-overview.md) åtgärder, särskilt loggar som rör avbildnings fel. |
| Drift loggar | Avbilda alla hanterings åtgärder som utförs på Azure Event Hubs-namnrymden. Data åtgärder samlas inte in på grund av den stora mängden data åtgärder som utförs på Azure Event Hubs. |
| Automatisk skalnings loggar | Fångar upp automatiska öknings åtgärder som utförs på ett Event Hubs-namnområde. |
| Kafka koordinator loggar | Samlar in Kafka Coordinator-åtgärder relaterade till Event Hubs. |
| Kafka användar fel loggar | Samlar in information om Kafka-API: er som anropas på Event Hubs. |
| Anslutnings händelse för Event Hubs virtuellt nätverk (VNet) | Samlar in information om IP-adresser och virtuella nätverk som skickar trafik till Event Hubs. |
| Kund hanterade nyckel användar loggar | Fångar åtgärder relaterade till kundhanterad nyckel. |


Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post innehåller sträng fält som använder det format som beskrivs i följande avsnitt.

## <a name="archive-logs-schema"></a>Schema för Arkiv loggar

Arkiv loggens JSON-strängar innehåller element som anges i följande tabell:

Name | Beskrivning
------- | -------
/TN | Beskrivning av uppgiften som misslyckades
ActivityId | Internt ID som används för spårning
trackingId | Internt ID som används för spårning
resourceId | Resurs-ID för Azure Resource Manager
eventHub | Event Hub fullständigt namn (innehåller namn rymds namn)
Partition | Event Hub-partition som skrivs till
archiveStep | möjliga värden: ArchiveFlushWriter, DestinationInit
startTime | Start tid för startfel
fel | Antal gånger som felet inträffade
durationInSeconds | Varaktighet för felet
meddelande | Felmeddelande
category | ArchiveLogs

Följande kod är ett exempel på en logg-JSON-sträng för Arkiv logg:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Schema för operativa loggar

I den operativa loggens JSON-strängar ingår element som anges i följande tabell:

Name | Beskrivning
------- | -------
ActivityId | Internt ID, används i spårnings syfte |
EventName | Åtgärdsnamn |
resourceId | Resurs-ID för Azure Resource Manager |
SubscriptionId | Prenumerations-ID:t |
EventTimeString | Åtgärds tid |
EventProperties | Egenskaper för åtgärd |
Status | Åtgärdsstatus |
Anropare | Uppringnings åtgärd (Azure Portal-eller hanterings klient) |
Kategori | OperationalLogs |

Följande kod är ett exempel på en fungerande logg-JSON-sträng:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Schema för autoskalning av loggar
Autoskalning log JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---- | ----------- | 
| TrackingId | Internt ID, som används för spårnings syfte |
| ResourceId | Azure Resource Manager resurs-ID. |
| Meddelande | Informations meddelande som innehåller information om åtgärder för automatisk ökning. Meddelandet innehåller det tidigare och aktuella värdet för data flödes enheten för en specifik namnrymd och vad som utlöste data flödes enheter. |

Här är ett exempel på en autoskalning-händelse: 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

## <a name="kafka-coordinator-logs-schema"></a>Kafka-koordinator loggar schema
Kafka Coordinator log JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---- | ----------- | 
| RequestId | ID för begäran som används för spårnings syfte |
| ResourceId | Resurs-ID för Azure Resource Manager |
| Åtgärd | Namnet på åtgärden som utförs under grupp samordningen |
| ClientId | Klientorganisations-ID |
| NamespaceName | Namn på namnområde | 
| SubscriptionId | ID för Azure-prenumeration |
| Meddelande | Informations-eller varnings meddelande som innehåller information om åtgärder som utförs under grupp samordningen. |

### <a name="example"></a>Exempel

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Schema för Kafka-användar fel loggar
Kafka user error log JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---- | ----------- |
| TrackingId | Spårnings-ID, som används för spårnings syfte. |
| NamespaceName | Namn på namnområde |
| Eventhub | Namn på händelsehubb |
| Partition | Partitions-ID |
| GroupId | Grupp-ID |
| ClientId | Klientorganisations-ID |
| ResourceId | Azure Resource Manager resurs-ID. |
| Meddelande | Informations meddelande som innehåller information om ett fel |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Händelse schema för Event Hubs virtuell nätverks anslutning

Event Hubs virtuellt nätverk (VNet) Connection Event JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---  | ----------- | 
| SubscriptionId | ID för Azure-prenumeration |
| NamespaceName | Namn på namnområde |
| IP-adress | IP-adress för en klient som ansluter till Event Hubs tjänsten |
| Åtgärd | Åtgärd som utförs av den Event Hubs tjänsten vid utvärdering av anslutnings begär Anden. Åtgärder som stöds **accepterar anslutning** och **neka anslutning**. |
| Anledning | Innehåller en orsak till varför åtgärden utfördes |
| Antal | Antal förekomster för den aktuella åtgärden |
| ResourceId | Azure Resource Manager resurs-ID. |

### <a name="example"></a>Exempel

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Kund hanterade nyckel användar loggar
Kund hanterad nyckel användar logg JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---- | ----------- | 
| Kategori | Typ av kategori för ett meddelande. Det är ett av följande värden: **fel** och **information** |
| ResourceId | Internt resurs-ID, som innehåller ID för Azure-prenumeration och namn område |
| KeyVault | Namnet på Key Vault resursen |
| Nyckel | Namnet på den Key Vault nyckeln. |
| Version | Key Vault nyckelns version |
| Åtgärd | Namnet på en åtgärd som utförs för att betjäna förfrågningar |
| Kod | Statuskod |
| Meddelande | Meddelande, som innehåller information om ett fel eller informations meddelande |



## <a name="next-steps"></a>Nästa steg
- [Introduktion till Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs API-översikt](event-hubs-api-overview.md)
- Kom igång med händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
