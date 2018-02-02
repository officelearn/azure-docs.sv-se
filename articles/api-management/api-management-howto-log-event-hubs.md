---
title: "Logga händelser i Händelsehubbar i Azure API Management | Microsoft Docs"
description: "Lär dig mer om att logga händelser till Händelsehubbar i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 77c3e41dd4b1fdf7e518de67b353f69fcb758c60
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Logga händelser i Händelsehubbar i Azure API Management
Händelsehubbar i Azure är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som dina anslutna enheter och program producerar. Händelsehubbar fungerar som ”ytterdörren” för en händelsepipeline, och när data har samlats in i en händelsehubb, det kan omvandlas och lagras med hjälp av en leverantör av realtidsanalys eller adaptrar för batchbearbetning/lagring. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser så att händelsekonsumenterna kan komma åt dem på sitt eget schema.

Den här artikeln är en medlem i den [integrera Azure API Management med Händelsehubbar](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) video och beskriver hur du loggar händelser för API Management med hjälp av Händelsehubbar i Azure.

## <a name="create-an-azure-event-hub"></a>Skapa ett Azure Event Hub

Detaljerade anvisningar om hur du skapar en händelsehubb och hämta anslutningssträngar som du behöver för att skicka och ta emot händelser till och från Event Hub finns [skapa ett namnområde för Händelsehubbar och en händelsehubb med hjälp av Azure portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Skapa en API Management-logg
Nu när du har en Händelsehubb nästa steg är att konfigurera en [loggaren](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) i API Management-tjänsten så att den kan logga händelser till Händelsehubben.

API Management loggare konfigureras med hjälp av den [API Management REST API](http://aka.ms/smapi). Innan du använder REST API för första gången, granska den [krav](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) och se till att du har [aktiverat åtkomst till REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Göra en HTTP PUT-begäran med hjälp av följande URL: en mall för att skapa en logg:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* Ersätt `{your service}` med namnet på din API Management service-instans.
* Ersätt `{new logger name}` med önskat namn för din nya meddelandeloggfiler. Du hänvisar till det här namnet när du konfigurerar den [loggen till eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) princip

Lägg till följande huvuden för begäran:

* Content-Type: application/json
* Auktorisering: SharedAccessSignature 58...
  * Anvisningar för att generera den `SharedAccessSignature` finns [Azure API Management REST API Authentication](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Ange text på begäran med hjälp av följande mall:

```json
{
  "loggertype" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggertype`måste anges till `AzureEventHub`.
* `description`ger en valfri beskrivning av loggaren och kan vara en tom sträng om så önskas.
* `credentials`innehåller den `name` och `connectionString` för din Azure-Händelsehubb.

När du gör begäran om loggaren skapas en statuskod för `201 Created` returneras.

> [!NOTE]
> Andra möjliga returkoder och skälen finns [skapa en loggaren](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Information om hur du utför andra åtgärder, till exempel listan-, update- och delete finns i [loggaren](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) dokumentationen för enheten.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurera principer för logg-eventhubs

Du kan konfigurera loggen till eventhubs principer till önskade logghändelser när du har konfigurerat din loggaren i API-hantering. Log-eventhubs principen kan användas i avsnittet princip för inkommande eller utgående princip-avsnittet.

1. Bläddra till APIM-instansen.
2. Välj fliken API.
3. Välj API: N som du vill lägga till principen. I det här exemplet lägger vi till en princip för att den **Echo API** i den **obegränsad** produkten.
4. Välj **alla åtgärder**.
5. Välj fliken Design överst på skärmen.
6. Klicka på triangel (bredvid penna) i fönstret inkommande eller utgående bearbetning.
7. Välj redigeraren. Mer information finns i [så att ange eller redigera principer](set-edit-policies.md).
8. Placera markören i den `inbound` eller `outbound` princip.
9. I fönstret till höger, Välj **avancerade principer** > **loggen till EventHub**. Detta infogar den `log-to-eventhub` Principmall för instruktionen.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Ersätt `logger-id` med namnet på API Management-logg som du konfigurerade i föregående steg.

Du kan använda ett uttryck som returnerar en sträng som värde för den `log-to-eventhub` element. En sträng som innehåller datum och tid, tjänstnamn, förfrågnings-id, ip-adressen för begäran och åtgärdsnamn loggas i det här exemplet.

Klicka på **spara** att spara den uppdaterade principkonfigurationen. Så snart den är i sparat principen är aktiv och händelser som loggas till avsedda Händelsehubben.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om Azure Event Hubs
  * [Kom igång med Händelsehubbar i Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ta emot meddelanden med EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Mer information om API-hantering och Händelsehubbar-integrering
  * [Loggaren enhetsreferens](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [principreferens för loggen till eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Övervaka dina API: er med Azure API Management och Händelsehubbar Runscope](api-management-log-to-eventhub-sample.md)    

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
