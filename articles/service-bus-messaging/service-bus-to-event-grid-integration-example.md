---
title: Integrationsexempel på Azure Service Bus till Event Grid | Microsoft Docs
description: Den här artikeln visar exempel på Service Bus-meddelanden och Event Grid-integration.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: spelluru
ms.openlocfilehash: ca4a6bfcc0f1041e76c07b3b240d7c641ef8f587
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699884"
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Exempel på integration av Azure Service Bus till Azure Event Grid

Den här artikeln visar hur du konfigurerar en Azure-funktion och en logikapp som båda tar emot meddelanden baserat på när en händelse från Azure Event Grid tas emot. Du får:
 
* Skapa en enkel [Azure-testfunktion](#test-function-setup) för felsökning och för att visa det första flödet av händelser från Event Grid. Utför det här steget oavsett om du utför de andra stegen eller inte.
* Skapa en [Azure-funktion som tar emot och bearbetar Azure Service Bus-meddelanden](#receive-messages-using-azure-function) baserat på Event Grid-händelser.
* Använd [Logic Apps-funktionen i Azure App Service](#receive-messages-using-azure-logic-app).

I exemplet som du skapar förutsätter vi att Service Bus-ämnet har två prenumerationer. Vi förutsätter också att Event Grid-prenumerationen har skapats för att skicka händelser för endast en Service Bus-prenumeration. 

I det här exemplet skickar du meddelanden till Service Bus-ämnet och kontrollerar sedan att händelsen har genererats för den här Service Bus-prenumerationen. Funktionen eller logikappen tar emot meddelandena från Service Bus-prenumerationen och slutför sedan.

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar måste du ha slutfört stegen i följande två avsnitt.

### <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus

Skapa ett Service Bus Premium-namnområde och skapa ett Service Bus-ämne som har två prenumerationer.

### <a name="send-a-message-to-the-service-bus-topic"></a>Skicka ett meddelande till Service Bus-ämnet

Du kan använda valfri metod för att skicka ett meddelande till ditt Service Bus-ämne. I exempelkoden i slutet av den här proceduren förutsätter vi att du använder Visual Studio 2017.

1. Klona [GitHub-lagringsplatsen azure-service-bus](https://github.com/Azure/azure-service-bus/).

1. Gå till mappen *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* i Visual Studio och öppna filen *SBEventGridIntegration.sln*.

1. Gå till projektet **MessageSender** och välj sedan **Program.cs**.

   ![8][]

1. Fyll i avsnittets namn och anslutningssträng och kör sedan följande konsolprogramkod:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Skapa en testfunktion

Innan du går igenom hela scenariot skapar du minst en liten testfunktion, som du kan använda för att felsöka och se vilka händelser som finns i flödet.

1. Skapa ett nytt Azure Functions-program i Azure Portal. Om du vill lära dig grunderna i Azure Functions kan du läsa [dokumentationen för Azure Functions](https://docs.microsoft.com/azure/azure-functions/).

1. Välj plustecknet (+) i din nya funktion om du vill lägga till en HTTP-utlösare:

    ![2][]
    
    Fönstret **Kom igång snabbt med en fördefinierad funktion** öppnas.

    ![3][]

1. Välj knappen **Webhook + API**, välj **CSharp** och välj sedan **Skapa den här funktionen**.
 
1. Klistra in följande kod i funktionen:

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
    IEnumerable<string> headerValues;
    if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
    {
    var validationHeaderValue = headerValues.FirstOrDefault();
    if(validationHeaderValue == "SubscriptionValidation")
    {
    var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
         var code = events[0].Data["validationCode"];
         return req.CreateResponse(HttpStatusCode.OK,
         new { validationResponse = code });
    }
    }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```

1. Välj **Spara och kör**.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Anslut funktionen och namnområdet via Event Grid

Nästa steg är att koppla ihop funktionen och Service Bus-namnområdet. I det här exemplet använder du Azure-portalen. Läs om hur du utför den här proceduren med PowerShell och Azure CLI i [Översikt över integration av Azure Service Bus till Azure Event Grid](service-bus-to-event-grid-integration-concept.md).

Så här skapar du en Azure Event Grid-prenumeration:
1. Gå till ditt namnområde i Azure Portal och välj **Event Grid** i fönstret till vänster.  
    Ditt namnområdesfönster öppnas med två Event Grid-prenumerationer i fönstret till höger.

    ![20][]

1. Välj **Händelseprenumeration**.  
    Fönstret **Händelseprenumeration** öppnas. På följande bild visas ett formulär för att prenumerera på en Azure-funktion eller en webhook utan att använda filter.

    ![21][]

1. Fyll i formuläret som på bilden och kom ihåg att ange relevant filter i rutan **Suffixfilter**.

1. Välj **Skapa**.

1. Skicka ett meddelande till ditt Service Bus-ämne enligt avsnittet Nödvändiga komponenter och kontrollera att händelser flödar in via övervakningsfunktionen i Azure Functions.

Nästa steg är att koppla ihop funktionen och Service Bus-namnområdet. I det här exemplet använder du Azure-portalen. Läs om hur du utför det här steget med PowerShell och Azure CLI i [Översikt över integration av Azure Service Bus till Azure Event Grid](service-bus-to-event-grid-integration-concept.md).

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Ta emot meddelanden med Azure Functions

I det föregående avsnittet visade vi ett enkelt test och felsökningsscenario där du kunde se att händelser flödar in. 

I det här avsnittet lär du dig hur du tar emot och bearbetar meddelanden när du har fått en händelse.

Du lägger till en Azure-funktion (se följande exempel) eftersom Service Bus-funktionerna i Azure Functions inte har inbyggt stöd för den nya Event Grid-integrationen ännu.

1. Välj **ReceiveMessagesOnEvent.cs** i samma Visual Studio-lösning som du öppnade under avsnittet Nödvändiga komponenter. 

    ![10][]

1. Ange din anslutningssträng i följande kod:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

1. I Azure Portal laddar du ned publiceringsprofilen för den Azure-funktion som du skapade i avsnittet Skapa en testfunktion.

    ![11][]

1. Högerklicka sedan på **SBEventGridIntegration** i Visual Studio och välj sedan **Publicera**. 

1. I fönstret **Publicera** för den publiceringsprofil som du laddade ned tidigare väljer du **Importera profil** och sedan **Publicera**.

    ![12][]

1. När du har publicerat den nya Azure-funktionen skapar du en ny Azure Event Grid-prenumeration som pekar på den nya Azure-funktionen.  
    Kontrollera att du använder rätt filter i rutan **Slutar med**, vilket ska vara ditt Service Bus-prenumerationsnamn.

1. Skicka ett meddelande till det Azure Service Bus-ämne som du skapade tidigare. Kontrollera sedan i Azure Functions-loggen i Azure Portal att händelser flödar in och att meddelanden tas emot.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Ta emot meddelanden med Logic Apps

Anslut en logikapp med Azure Service Bus och Azure Event Grid på följande sätt:

1. Skapa en ny logikapp i Azure Portal och välj **Event Grid** som startåtgärd.

    ![13][]

    Fönstret Logikappdesigner öppnas.

    ![14][]

1. Lägg till din information på följande sätt:

    a. Ange namnet på ditt namnområde i rutan **Resursnamn**. 

    b. Ange filter för din prenumeration i rutan **Suffixfilter** under **Avancerade alternativ**.

1. Lägg till en Service Bus-mottagningsåtgärd för att ta emot meddelanden från en ämnesprenumeration.  
    Den sista åtgärden visas på följande bild:

    ![15][]

1. Lägg till en fullständig händelse som på följande bild:

    ![16][]

1. Spara logikappen och skicka ett meddelande till ditt Service Bus-ämne enligt avsnittet Nödvändiga komponenter.  
    Iaktta logikappkörningen. Om du vill visa mer data för körningen väljer du **Översikt** och granskar data under **Körningshistorik**.

    ![17][]

    ![18][]

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Läs mer om [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Läs mer om [Logic Apps-funktionen i Azure App Service](https://docs.microsoft.com/azure/logic-apps/).
* Läs mer om [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
