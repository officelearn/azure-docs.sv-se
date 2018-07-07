---
title: Logga händelser till Azure Event Hubs i Azure API Management | Microsoft Docs
description: Lär dig mer om att logga händelser till Azure Event Hubs i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 496928697af069f773e47974129bb7d3de3e1cbc
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37886979"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Logga händelser till Azure Event Hubs i Azure API Management
Händelsehubbar i Azure är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som dina anslutna enheter och program producerar. Händelsehubbar fungerar som ”ytterdörren” för en händelsepipeline, och när data har samlats in i en händelsehubb, det kan omvandlas och lagras med valfri leverantör av realtidsanalys eller batchbearbetnings-/ lagringsadapter. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser så att händelsekonsumenterna kan komma åt dem på sitt eget schema.

Den här artikeln är avsett för den [integrera Azure API Management med Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) video och beskriver hur API Management-händelser med Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Skapa en Azure-händelsehubb

Detaljerade anvisningar om hur du skapar en event hub och få anslutningssträngar som du behöver för att skicka och ta emot händelser till och från Händelsehubben i [skapa ett Event Hubs-namnområde och en event hub med Azure-portalen](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Skapa en API Management-loggare
Nu när du har en Event Hub nästa steg är att konfigurera en [loggaren](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) i API Management-tjänsten så att den kan logga händelser till Händelsehubben.

API Management tangenttryckningar konfigureras med hjälp av den [API Management REST API](http://aka.ms/smapi). Innan du använder REST API för första gången, granska de [krav](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) och kontrollera att du har [har aktiverat åtkomst till REST-API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Göra en HTTP PUT-begäran med hjälp av följande URL: en mall för att skapa en loggare:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Ersätt `{your service}` med namnet på din API Management-tjänstinstans.
* Ersätt `{new logger name}` med önskat namn för din nya loggaren. Du hänvisar till det här namnet när du konfigurerar den [log till eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) princip

Lägg till följande rubriker i begäran:

* Content-Type: application/json
* Auktorisering: SharedAccessSignature 58...
  * Mer information om att generera den `SharedAccessSignature` Se [Azure API Management REST API-autentisering](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Ange begärandetexten Använd följande mall:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType` måste anges till `AzureEventHub`.
* `description` ger en valfri beskrivning av loggaren och kan vara en tom sträng om du vill.
* `credentials` innehåller den `name` och `connectionString` för din Azure-Händelsehubb.

När du gör begäran, om loggaren har skapats, statuskod `201 Created` returneras. En exempel-respons baserat på ovanstående exempelförfrågan visas nedan.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> Andra möjliga returkoder och skälen finns i [skapa en loggare](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Information om hur du utför andra åtgärder, till exempel listan, uppdatera och ta bort, i den [loggaren](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) entity-dokumentation.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurera log-eventhubs-principer

När din loggaren har konfigurerats i API Management kan konfigurera du log-eventhubs-principer för att logga händelserna som önskade. Log-eventhubs-principen kan användas i principavsnittet för inkommande eller utgående principavdelningen.

1. Bläddra till APIM-instansen.
2. Välj fliken API.
3. Välj det API som du vill lägga till principen. I det här exemplet lägger vi till en princip för att den **Echo API** i den **obegränsad** produkten.
4. Välj **Alla åtgärder**.
5. Välj fliken Design överst på skärmen.
6. Klicka på triangeln (bredvid blyertspennan) i fönstret inkommande eller utgående bearbetning.
7. Välj Kodredigeraren. Mer information finns i [ange eller redigera principer](set-edit-policies.md).
8. Placera markören i den `inbound` eller `outbound` princip.
9. I fönstret till höger väljer **avancerade principer** > **Log-to-EventHub**. Detta infogar den `log-to-eventhub` Principmall för instruktionen.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Ersätt `logger-id` med värdet som du använde för `{new logger name}` i URL: en för att skapa loggaren i föregående steg.

Du kan använda valfritt uttryck som returnerar en sträng som värde för den `log-to-eventhub` element. I det här exemplet är loggas en sträng som innehåller datum och tid, tjänstnamn, begärande-id, ip-adressen för begäran och Åtgärdsnamnet.

Klicka på **spara** att spara den uppdaterade principkonfigurationen. När det sparats principen är aktiv och händelser som loggas till avsedda Händelsehubben.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Event Hubs
  * [Kom igång med Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ta emot meddelanden med EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Mer information om API Management och Event Hubs-integrering
  * [Loggaren enhetsreferens](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [Referens för loggen till eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Övervaka dina API: er med Azure API Management, Event Hubs och Runscope](api-management-log-to-eventhub-sample.md)  
* Läs mer om [integrering med Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
