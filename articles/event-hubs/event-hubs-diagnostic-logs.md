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
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509807"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Konfigurera diagnostikloggar för en Azure-händelsehubb

Du kan visa två typer av loggar för Azure Event Hubs:

* **[Aktivitets loggar](../azure-monitor/platform/platform-logs-overview.md)**: loggarna innehåller information om åtgärder som utförs i ett jobb. Loggarna är alltid aktiverade. Du kan se aktivitets logg poster genom att välja **aktivitets logg** i det vänstra fönstret för ditt Event Hub-namnområde i Azure Portal. Exempel: "skapa eller uppdatera namn område", "skapa eller uppdatera Event Hub".

    ![Aktivitets logg för ett Event Hubs-namnområde](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md)**: du kan konfigurera diagnostikloggar för en mer omfattande vy av allt som händer med ett jobb. Diagnostikloggar behandlar aktiviteter från den tidpunkt då jobbet skapas tills jobbet tas bort, inklusive uppdateringar och aktiviteter som inträffar när jobbet körs.

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

- **Arkiv loggar**: loggar som är relaterade till Event Hubs Arkiv, särskilt loggar som rör Arkiv fel.
- **Drift loggar**: information om vad som händer under Event Hubs åtgärder, närmare bestämt åtgärds typen, inklusive skapande av händelsehubben, använda resurser och status för åtgärden.
- **Automatiska skalnings loggar**: information om automatiska skalnings åtgärder som utförs på ett Event Hubs-namnområde. 
- **Kafka-koordinator loggar** – information om Kafka koordinator åtgärder relaterade till Event Hubs. 
- **Kafka-användar loggar**: information om Kafka användar åtgärder som är relaterade till Event Hubs. 
- **Anslutnings händelse för Event Hubs virtuellt nätverk (VNet)**: information om Event Hubs virtuella nätverks anslutnings händelser. 
- **Kund hanterade nyckel användar loggar**: information om åtgärder relaterade till kundhanterad nyckel. 


    Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post innehåller sträng fält som använder det format som beskrivs i följande avsnitt.

## <a name="archive-logs-schema"></a>Schema för Arkiv loggar

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

## <a name="operational-logs-schema"></a>Schema för operativa loggar

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

## <a name="autoscale-logs-schema"></a>Schema för autoskalning av loggar
Autoskalning log JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---- | ----------- | 
| trackingId | Internt ID, som används för spårnings syfte |
| resourceId | Internt ID, som innehåller ID för Azure-prenumeration och namn område |
| meddelande | Informations meddelande som innehåller information om åtgärder för automatisk ökning. Meddelandet innehåller det tidigare och aktuella värdet för data flödes enheten för en specifik namnrymd och vad som utlöste data flödes enheter. |

## <a name="kafka-coordinator-logs-schema"></a>Kafka-koordinator loggar schema
Kafka Coordinator log JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---- | ----------- | 
| requestId | ID för begäran som används för spårnings syfte |
| resourceId | Internt ID, som innehåller ID för Azure-prenumeration och namn område |
| operationName | Namnet på åtgärden som utförs under grupp samordningen |
| ClientID | Klientorganisations-ID |
| namespaceName | Namn på namnområde | 
| subscriptionId | ID för Azure-prenumeration |
| meddelande | Informations meddelande som innehåller information om åtgärder som utförs under samordningen av konsument grupper. |

## <a name="kafka-user-error-logs-schema"></a>Schema för Kafka-användar fel loggar
Kafka user error log JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---- | ----------- |
| trackingId | spårnings-ID, som används för spårnings syfte. |
| namespaceName | Namn på namnområde |
| eventhub | Namn på händelsehubb |
| Partition | Partitions-ID |
| groupId | Grupp-ID |
| ClientId | Klientorganisations-ID |
| resourceId | Internt ID, som innehåller ID för Azure-prenumeration och namn område |
| meddelande | Informations meddelande som innehåller information om ett fel |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Händelse schema för Event Hubs virtuell nätverks anslutning

Event Hubs virtuellt nätverk (VNet) Connection Event JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---  | ----------- | 
| subscriptionId | ID för Azure-prenumeration |
| namespaceName | Namn på namnområde |
| Adresser | IP-adress för en klient som ansluter till Event Hubs tjänsten |
| åtgärd | Åtgärd som utförs av den Event Hubs tjänsten vid utvärdering av anslutnings begär Anden. De åtgärder som stöds är **AcceptConnection** och **RejectConnection**. |
| orsak | Innehåller en orsak till varför åtgärden utfördes |
| count | Antal förekomster för den aktuella åtgärden |
| resourceId | Internt resurs-ID som innehåller prenumerations-ID och namn områdes namn. |

## <a name="customer-managed-key-user-logs"></a>Kund hanterade nyckel användar loggar
Kund hanterad nyckel användar logg JSON innehåller element som anges i följande tabell:

| Name | Beskrivning |
| ---- | ----------- | 
| category | Typ av kategori för ett meddelande. Det är ett av följande värden: **fel** och **information** |
| resourceId | Internt resurs-ID, som innehåller ID för Azure-prenumeration och namn område |
| keyVault | Namnet på Key Vault resursen |
| key | Namnet på den Key Vault nyckeln. |
| version | Key Vault nyckelns version |
| reparation | Namnet på en åtgärd som utförs för att betjäna förfrågningar |
| kod | Statuskod |
| meddelande | Meddelande, som innehåller information om ett fel eller informations meddelande |



## <a name="next-steps"></a>Nästa steg
- [Introduktion till Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs API-översikt](event-hubs-api-overview.md)
- Kom igång med händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
