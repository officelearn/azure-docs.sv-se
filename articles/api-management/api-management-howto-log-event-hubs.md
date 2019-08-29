---
title: Logga händelser till Azure Event Hubs i Azure API Management | Microsoft Docs
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
ms.openlocfilehash: 646d9206ec82d5f35ccab9365e76276ff779d225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073491"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Logga händelser till Azure Event Hubs i Azure API Management
Händelsehubbar i Azure är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som dina anslutna enheter och program producerar. Event Hubs fungerar som "Front dörren" för händelse pipelinen och när data samlas in i en händelsehubben kan den transformeras och lagras med hjälp av en analys av real tids analys eller batch-/minnes kort. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser så att händelsekonsumenterna kan komma åt dem på sitt eget schema.

Den här artikeln är kopplad till [integrera Azure-API Management med Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) video och beskriver hur du loggar API Management händelser med Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Skapa en Azure-händelsehubb

Detaljerade anvisningar om hur du skapar en Event Hub och hämtar anslutnings strängar som du behöver för att skicka och ta emot händelser till och från Händelsehubben finns i [skapa ett Event Hubs-namnområde och en Event Hub med hjälp av Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Skapa en API Management-loggare
Nu när du har en Event Hub är nästa steg att konfigurera en loggare [](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) i API Management-tjänsten så att den kan logga händelser till händelsehubben.

API Management-loggar konfigureras med hjälp av [API Management REST API](https://aka.ms/smapi). Innan du börjar använda REST API för första gången bör du granska [kraven](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) och se till att du har [aktiverat åtkomst till REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Om du vill skapa en loggare gör du en HTTP-begäran med följande URL-mall:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Ersätt `{your service}` med namnet på din API Management-tjänstinstans.
* Ersätt `{new logger name}` med det önskade namnet för din nya logg. Du refererar till det här namnet när du konfigurerar [logg-till-eventhub-](/azure/api-management/api-management-advanced-policies#log-to-eventhub) principen

Lägg till följande rubriker i begäran:

* Innehålls typ: Application/JSON
* Auktoriseringsregeln SharedAccessSignature 58...
  * Instruktioner om hur du `SharedAccessSignature` genererar autentiseringen se Azure- [API Management REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Ange begär ande texten med hjälp av följande mall:

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

* `loggerType`måste anges till `AzureEventHub`.
* `description`innehåller en valfri beskrivning av loggaren och kan vara en sträng med nollängd om du vill.
* `credentials``name` innehåller och `connectionString` för din Azure Event Hub.

Om loggaren skapas returneras en status kod som `201 Created` returneras när du gör en begäran. Ett exempel svar baserat på ovanstående exempel förfrågan visas nedan.

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
> Andra möjliga retur koder och deras orsaker finns i [skapa en loggare](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Information om hur du utför andra åtgärder, till exempel lista, uppdatera och ta bort, finns i dokumentationen till entiteten för [loggning](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) .
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurera logg-till-eventhubs-principer

När din logg har kon figurer ATS i API Management kan du konfigurera dina eventhubs-principer för att logga de önskade händelserna. Du kan använda logg-till-eventhubs-principen i avsnittet för inkommande eller utgående principer.

1. Bläddra till APIM-instansen.
2. Välj fliken API.
3. Välj det API som du vill lägga till principen i. I det här exemplet lägger vi till en princip i **eko-API: et** i den **obegränsade** produkten.
4. Välj **Alla åtgärder**.
5. Överst på skärmen väljer du fliken Design.
6. I fönstret inkommande eller utgående bearbetning klickar du på triangeln (bredvid blyertspennan).
7. Välj kod redigeraren. Mer information finns i [så här ställer du in eller redigerar principer](set-edit-policies.md).
8. Placera markören i `inbound` avsnittet eller `outbound` principen.
9. I fönstret till höger väljer du **avancerade principer** > **Logga till EventHub**. Detta infogar `log-to-eventhub` princip instruktions mal len.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Ersätt `logger-id` med värdet som du använde för `{new logger name}` i URL: en för att skapa loggaren i föregående steg.

Du kan använda alla uttryck som returnerar en sträng som värde för `log-to-eventhub` elementet. I det här exemplet loggas en sträng som innehåller datum och tid, tjänst namn, begärande-ID, begär ande-IP-adress och åtgärds namn.

Klicka på **Spara** för att spara den uppdaterade princip konfigurationen. Så snart den har sparats är principen aktiv och händelserna loggas i den angivna Händelsehubben.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Event Hubs
  * [Kom igång med Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ta emot meddelanden med EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Läs mer om API Management och Event Hubs-integrering
  * [Referens för entiteten loggning](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [logg-till-eventhub-princip referens](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Övervaka dina API: er med Azure API Management, Event Hubs och Moesif](api-management-log-to-eventhub-sample.md)  
* Lär dig mer om [integrering med Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
