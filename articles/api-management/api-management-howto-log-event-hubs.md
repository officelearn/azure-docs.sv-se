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
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 1aba03102dcd96753ef4db57edce889a43e4e3fc
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Logga händelser i Händelsehubbar i Azure API Management
Händelsehubbar i Azure är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som dina anslutna enheter och program producerar. Händelsehubbar fungerar som ”ytterdörren” för en händelsepipeline, och när data har samlats in i en händelsehubb, det kan omvandlas och lagras med hjälp av en leverantör av realtidsanalys eller adaptrar för batchbearbetning/lagring. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser så att händelsekonsumenterna kan komma åt dem på sitt eget schema.

Den här artikeln är en medlem i den [integrera Azure API Management med Händelsehubbar](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) video och beskriver hur du loggar händelser för API Management med hjälp av Händelsehubbar i Azure.

## <a name="create-an-azure-event-hub"></a>Skapa ett Azure Event Hub
Om du vill skapa en ny Händelsehubb, logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com) och på **ny**->**Apptjänster**->**Service Bus**  -> **Händelsehubb**->**Snabbregistrering**. Ange namnet på en Händelsehubb region, väljer en prenumeration och ett namnområde. Om du inte har skapat ett namnområde kan du skapa en genom att skriva ett namn i den **Namespace** textruta. När alla egenskaper har konfigurerats, klickar du på **skapa en ny Händelsehubb** att skapa Händelsehubben.

![Skapa händelsehubb][create-event-hub]

Gå sedan till den **konfigurera** för din nya Event Hub och skapa två **delade åtkomstprinciper**. Namn på det första **sändande** och ge den **skicka** behörigheter.

![Skicka princip][sending-policy]

Namn på det andra **tar emot**, ger det **lyssna** behörigheter och klickar på **spara**.

![Emot princip][receiving-policy]

Varje princip för delad åtkomst gör att program kan skicka och ta emot händelser till och från Event Hub. Om du vill komma åt anslutningssträngar för dessa principer, navigera till den **instrumentpanelen** Event Hub och klicka på fliken **anslutningsinformationen**.

![Anslutningssträng][event-hub-dashboard]

Den **sändande** anslutningssträngen som används när du loggar händelser, och **tar emot** anslutningssträngen som används när du hämtar händelser från Event Hub.

![Anslutningssträng][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Skapa en API Management-logg
Nu när du har en Händelsehubb nästa steg är att konfigurera en [loggaren](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) i API Management-tjänsten så att den kan logga händelser till Händelsehubben.

API Management loggare konfigureras med hjälp av den [API Management REST API](http://aka.ms/smapi). Innan du använder REST API för första gången, granska den [krav](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) och se till att du har [aktiverat åtkomst till REST API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Gör en HTTP PUT-begäran med hjälp av följande URL: en mall för att skapa en logg.

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* Ersätt `{your service}` med namnet på din API Management service-instans.
* Ersätt `{new logger name}` med önskat namn för din nya meddelandeloggfiler. Du sedan hänvisar till det här namnet när du konfigurerar den [loggen till eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) princip

Lägg till följande huvuden i begäran.

* Content-Type: application/json
* Auktorisering: SharedAccessSignature 58...
  * Anvisningar för att generera den `SharedAccessSignature` finns [Azure API Management REST API Authentication](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Ange text på begäran med hjälp av följande mall.

```json
{
  "type" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `type`måste anges till `AzureEventHub`.
* `description`ger en valfri beskrivning av loggaren och kan vara en tom sträng om så önskas.
* `credentials`innehåller den `name` och `connectionString` för din Azure-Händelsehubb.

När du gör begäran om loggaren skapas en statuskod för `201 Created` returneras.

> [!NOTE]
> Andra möjliga returkoder och skälen finns [skapa en loggaren](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Att se hur utföra andra åtgärder, till exempel listan, uppdatera och ta bort, finns det [loggaren](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) dokumentationen för enheten.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurera principer för logg-eventhubs
Du kan konfigurera loggen till eventhubs principer till önskade logghändelser när du har konfigurerat din loggaren i API-hantering. Log-eventhubs principen kan användas i avsnittet princip för inkommande eller utgående princip-avsnittet.

För att konfigurera principer, logga in på den [Azure-portalen](https://portal.azure.com), navigera till din API Management-tjänst och klicka på **Publisher portal** åt publisher-portalen.

![Utgivarportalen][publisher-portal]

Klicka på **principer** Markera önskad produkt och API i API Management-menyn till vänster och klicka på **Lägg till princip**. I det här exemplet lägger vi till en princip för att den **Echo API** i den **obegränsad** produkten.

![Lägg till princip][add-policy]

Placera markören i den `inbound` princip för och klicka på den **loggen till EventHub** princip för att infoga den `log-to-eventhub` Principmall för instruktionen.

![Principredigerare][event-hub-policy]

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
  * [Loggaren enhetsreferens](https://docs.microsoft.com/rest/api/apimanagement/loggers)
  * [principreferens för loggen till eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Övervaka dina API: er med Azure API Management, Händelsehubbar och Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Titta på en videogenomgång
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
>
>

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
