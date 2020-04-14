---
title: Så här loggar du händelser till Azure Event Hubs i Azure API Management | Microsoft-dokument
description: Lär dig hur du loggar händelser till Azure Event Hubs i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 2f67079938ddcf4a65e01ef50ab7e5cdf7078b73
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260946"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Så här loggar du händelser till Azure Event Hubs i Azure API Management
Händelsehubbar i Azure är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som dina anslutna enheter och program producerar. Event Hubs fungerar som "ytterdörren" för en händelsepipeline, och när data samlas in till en händelsenav kan den omvandlas och lagras med hjälp av alla analysleverantörer i realtid eller batch-/lagringsadaptrar. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser så att händelsekonsumenterna kan komma åt dem på sitt eget schema.

Den här artikeln är en följeslagare till [videofilmen Integrera Azure API Management med eventhubbar](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) och beskriver hur du loggar API Management-händelser med Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Skapa en Azure-händelsehubb

Detaljerade steg om hur du skapar en händelsehubb och hämtar anslutningssträngar som du behöver för att skicka och ta emot händelser till och från eventhubben finns i [Skapa ett namnområde för händelsehubben och ett händelsenav med Azure-portalen](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Skapa en API Management-logger
Nu när du har en eventhubb är nästa steg att konfigurera en [logger](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger) i din API Management-tjänst så att den kan logga händelser till eventhubben.

API Management loggers är konfigurerade med hjälp av [API Management REST API](https://aka.ms/apimapi). Exempel på detaljerad begäran finns i [hur du skapar Loggers](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger/createorupdate).

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurera principer för logg till eventhubs

När loggern har konfigurerats i API Management kan du konfigurera principer för logg till eventhubs för att logga önskade händelser. Principen log-to-eventhubs kan användas i antingen det inkommande principavsnittet eller avsnittet utgående princip.

1. Bläddra till APIM-instansen.
2. Välj fliken API.
3. Välj det API som du vill lägga till principen till. I det här exemplet lägger vi till en princip i **Echo-API:et** i **Unlimited-produkten.**
4. Välj **Alla operationer**.
5. Välj fliken Design högst upp på skärmen.
6. Klicka på triangeln (bredvid pennan) i fönstret Inkommande eller utgående bearbetning.
7. Välj kodredigeraren. Mer information finns i [Så här anger eller redigerar du principer](set-edit-policies.md).
8. Placera markören `inbound` i `outbound` avsnittet eller principen.
9. Välj **Avancerade principer** > **Logga till EventHub**i fönstret till höger . Då infogas `log-to-eventhub` principuttalandemallen.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Ersätt `logger-id` med det värde `{new logger name}` som du använde för i URL:en för att skapa loggern i föregående steg.

Du kan använda alla uttryck som returnerar `log-to-eventhub` en sträng som värde för elementet. I det här exemplet loggas en sträng som innehåller datum och tid, tjänstnamn, begärande-ID, IP-adress för begäran och operationsnamn.

Klicka på **Spara** om du vill spara den uppdaterade principkonfigurationen. Så snart den har sparats är principen aktiv och händelser loggas till den angivna händelsehubben.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Event Hubs
  * [Komma igång med Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ta emot meddelanden med EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Läs mer om integrering av API-hantering och händelsehubbar
  * [Referens för Logger-enhet](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger)
  * [referens för log-to-eventhub-princip](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Övervaka dina API:er med Azure API Management, Event Hubs och Moesif](api-management-log-to-eventhub-sample.md)  
* Läs mer om [integrering med Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
