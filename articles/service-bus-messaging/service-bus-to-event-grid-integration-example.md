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
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: b29798bb87b7c5c677e7d80e552e45e8d1290541
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786947"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Svara på Azure Service Bus-händelser som tagits emot via Azure Event Grid med hjälp av Azure Functions och Azure Logic Apps
I den här självstudien får du lära dig hur du svarar på Azure Service Bus-händelser som tas emot via Azure Event Grid med hjälp av Azure Functions och Azure Logic Apps. Gör du följande steg:
 
- Skapa ett test Azure-funktion för att felsöka och visa det första flödet av händelser från Event Grid.
- Skapa en Azure-funktion som tar emot och bearbetar Azure Service Bus-meddelanden baserat på Event Grid-händelser.
- Skapa en logikapp för att svara på Event Grid-händelser

När du skapar Service Bus, Event Grid, Azure Functions och Logic Apps artefakter kan göra du följande: 

1. Skicka meddelanden till en Service Bus-ämne. 
2. Kontrollera att prenumerationer till ämnet tagit emot dessa meddelanden
3. Kontrollera att funktionen eller logikappen appen som prenumererar på för händelsen har tagit emot händelsen. 

## <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus
Följ anvisningarna i den här självstudien: [Snabbstart: Använd Azure-portalen för att skapa ett Service Bus-ämne och prenumerationer till ämnet](service-bus-quickstart-topics-subscriptions-portal.md) att utföra följande uppgifter:

- Skapa en **premium** Service Bus-namnområde. 
- Hämta anslutningssträngen. 
- Skapa ett Service Bus-ämne.
- Skapa två prenumerationer till ämnet. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Förbereda ett exempelprogram för att skicka meddelanden
Du kan använda valfri metod för att skicka ett meddelande till ditt Service Bus-ämne. Exempelkoden i slutet av den här proceduren förutsätter att du använder Visual Studio 2017.

1. Klona [GitHub-lagringsplatsen azure-service-bus](https://github.com/Azure/azure-service-bus/).
2. Gå till mappen *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* i Visual Studio och öppna filen *SBEventGridIntegration.sln*.
3. Gå till projektet **MessageSender** och välj sedan **Program.cs**.
4. Fyll i Service Bus avsnittets namn och anslutningssträngen som du fick från föregående steg:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Skapa och köra program för att skicka testmeddelanden till Service Bus-ämne. 

## <a name="set-up-a-test-function-on-azure"></a>Skapa en testfunktion på Azure 
Innan du går igenom hela scenariot, ställa in minst en liten testfunktion, som du kan använda för att felsöka och se de händelser som flödar in. Följ instruktionerna i den [skapa din första funktion i Azure-portalen](../azure-functions/functions-create-first-azure-function.md) artikeln om du vill utföra följande uppgifter: 

1. Skapa en funktionsapp.
2. Skapa en HTTP-utlöst funktion. 

Gör sedan följande steg: 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions V2](#tab/v2)

1. Expandera **Functions** i trädet visa och välj din funktion. Ersätt Koden för funktionen med följande kod: 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
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
2. Välj **Spara och kör**.

    ![Funktionen app-utdata](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Välj **hämta Funktionswebbadress** och noterar URL: en. 

    ![Hämta funktionswebbadress](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions V1](#tab/v1)

1. Konfigurera funktionen för att använda **V1** version: 
    1. Välj funktionsappen i trädvyn och välj **fungera appinställningar**. 

        ![Funktionsappinställningar]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Välj **~ 1** för **körningsversion**. 
2. Expandera **Functions** i trädet visa och välj din funktion. Ersätt Koden för funktionen med följande kod: 

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
4. Välj **Spara och kör**.

    ![Funktionen app-utdata](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Välj **hämta Funktionswebbadress** och noterar URL: en. 

    ![Hämta funktionswebbadress](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Anslut funktionen och namnområdet via Event Grid
I det här avsnittet ska koppla du ihop funktionen och Service Bus-namnområdet med hjälp av Azure portal. 

Följ dessa steg om du vill skapa en Azure Event Grid-prenumeration:

1. Gå till ditt namnområde i Azure-portalen och välj i den vänstra rutan **händelser**. Ditt namnområdesfönster öppnas med två Event Grid-prenumerationer i fönstret till höger. 
    
    ![Service Bus - sidan för evenemang](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Välj **+ händelseprenumeration** i verktygsfältet. 
3. På den **skapa händelseprenumeration** gör du följande steg:
    1. Ange en **namn** för prenumerationen. 
    2. Välj **Webhook** för **Slutpunktstyp**. 

        ![Service Bus - Event Grid-prenumeration](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Valde **väljer du en slutpunkt**, klistra in funktionens URL och välj sedan **bekräfta val**. 

        ![Funktionen – väljer du slutpunkten](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Växla till den **filter** ange namnet på den **första prenumerationen** att Service Bus-ämne som du skapade tidigare och välj sedan den **skapa** knappen. 

        ![Händelsefilter för prenumeration](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Kontrollera att du ser händelseprenumeration i listan.

    ![Händelseprenumeration i listan](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Skicka meddelanden till Service Bus-ämnet
1. Kör .NET C# program som skickar meddelanden till Service Bus-ämne. 

    ![App konsolutdata](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. På sidan för din app i Azure-funktion, expandera **Functions**, expandera din **funktionen**, och välj **övervakaren**. 

    ![Övervaka funktion](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Ta emot meddelanden med Azure Functions
I det föregående avsnittet visade vi ett enkelt test och felsökningsscenario där du kunde se att händelser flödar in. 

I det här avsnittet lär du dig hur du tar emot och bearbetar meddelanden när du har fått en händelse.

### <a name="publish-a-function-from-visual-studio"></a>Publicera en funktion från Visual Studio
1. I samma Visual Studio-lösning (**SBEventGridIntegration**) som du öppnade, Välj **ReceiveMessagesOnEvent.cs** i den **SBEventGridIntegration** projekt. 
2. Ange din Service Bus-anslutningssträng i följande kod:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Ladda ned den **publiceringsprofil** för funktionen:
    1. Markera din funktionsapp. 
    2. Välj den **översikt** fliken om det inte redan är markerat. 
    3. Välj **hämta publiceringsprofil** i verktygsfältet. 

        ![Hämta publiceringsprofil för funktionen](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Spara filen till din projektmapp, till exempel. 
4. Högerklicka sedan på **SBEventGridIntegration** i Visual Studio och välj sedan **Publicera**. 
5. Välj *starta** på den **publicera** sidan. 
6. På den **välja en publiceringsmål** utför följande steg, markera **Importprofil**. 

    ![Visual Studio - knappen Importera profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Välj den **publicera profilfil** du hämtade tidigare. 
8. Välj **publicera** på den **publicera** sidan. 

    ![Visual Studio – publicera](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Kontrollera att du ser den nya Azure-funktionen **ReceiveMessagesOnEvent**. Uppdatera sidan om det behövs. 

    ![Bekräfta att den nya funktionen skapas](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Hämta URL: en på den nya funktionen och Skriv ned. 

### <a name="event-grid-subscription"></a>Event Grid-prenumeration

1. Ta bort den befintliga Event Grid-prenumerationen:
    1. På den **Service Bus Namespace** väljer **händelser** på den vänstra menyn. 
    2. Välj befintlig händelseprenumerationen. 
    3. På den **händelseprenumeration** väljer **ta bort**.
2. Följ instruktionerna i den [Anslut funktionen och namnområdet via Event Grid](#connect-the-function-and-namespace-via-event-grid) avsnitt för att skapa en Event Grid-prenumeration med hjälp av den nya funktions-URL.
3. Följ anvisningarna i den [skicka meddelanden till Service Bus-ämnet](#send-messages-to-the-service-bus-topic) avsnitt för att skicka meddelanden till ämnet och övervaka funktionen. 

## <a name="receive-messages-by-using-logic-apps"></a>Ta emot meddelanden med Logic Apps
Anslut en logikapp med Azure Service Bus och Azure Event Grid genom att följa dessa steg:

1. Skapa en logikapp i Azure-portalen.
    1. Välj **+ skapa en resurs**väljer **integrering**, och välj sedan **Logikapp**. 
    2. På den **Logikapp – skapa** anger en **namn** för logikappen.
    3. Välj din Azure-**prenumeration**. 
    4. Välj **Använd befintlig** för den **resursgrupp**, och välj den resursgrupp som du använde för andra resurser (t.ex. Azure-funktion, Service Bus-namnrymd) som du skapade tidigare. 
    5. Välj den **plats** för logikappen. 
    6. Välj **skapa** att skapa logikappen. 
2. På den **Logic Apps Designer** väljer **tom Logikapp** under **mallar**. 
3. På designern gör du följande:
    1. Sök efter **Event Grid**. 
    2. Välj **när en resurshändelse inträffar (förhandsversion), Azure Event Grid**. 

        ![Logikappdesigner – väljer Event Grid-utlösare](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Välj **logga in**, ange dina autentiseringsuppgifter för Azure och välj **Tillåt åtkomst**. 
5. På den **när en resurshändelse inträffar** gör du följande steg:
    1. Välj din Azure-prenumeration. 
    2. För **resurstyp**väljer **Microsoft.ServiceBus.Namespaces**. 
    3. För **resursnamn**, ditt Service Bus-namnområde. 
    4. Välj **Lägg till ny parameter**, och välj **Suffixfilter**. 
    5. För **Suffixfilter**, anger du namnet på din andra Service Bus-ämnesprenumeration. 

        ![Logikappdesigner – Konfigurera händelse](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Välj **+ nytt steg** i designer och utför följande steg:
    1. Sök efter **Service Bus**.
    2. Välj **Service Bus** i listan. 
    3. Välj **hämta meddelanden** i den **åtgärder** lista. 
    4. Välj **hämta meddelanden från en ämnesprenumeration (peek-lock)**. 

        ![Logikappdesigner – get meddelanden åtgärd](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Ange en **namn för anslutningen**. Exempel: **Hämta meddelanden från ämnesprenumeration**, och välj Service Bus-namnområdet. 

        ![Logikappdesigner – Välj Service Bus-namnområdet](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Välj **RootManageSharedAccessKey**.

        ![Logikappdesigner – Välj den delade åtkomstnyckeln](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Välj **Skapa**. 
    8. Välj ditt ämne och en prenumeration. 
    
        ![Logikappdesigner – väljer Service Bus-ämne och prenumeration](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Välj **+ nytt steg**, och utför följande steg: 
    1. Välj **Service Bus**.
    2. Välj **komplettera meddelandet i en ämnesprenumeration** från listan över åtgärder. 
    3. Välj din Service Bus **avsnittet**.
    4. Välj andra **prenumeration** till ämnet.
    5. För **låstoken för meddelandet**väljer **Lås Token** från den **dynamiskt innehåll**. 

        ![Logikappdesigner – väljer Service Bus-ämne och prenumeration](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Välj **spara** i verktygsfältet på Logic Apps Designer att spara logikappen. 
9. Följ anvisningarna i den [skicka meddelanden till Service Bus-ämnet](#send-messages-to-the-service-bus-topic) avsnitt för att skicka meddelanden till ämnet. 
10. Växla till den **översikt** sidan av din logikapp. Du ser logikappen körs den **Körningshistorik** för meddelanden som skickas.

    ![Logikappdesigner – logikapp-körningar](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

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
