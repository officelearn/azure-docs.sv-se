---
title: 'Självstudiekurs: Exempel på integrering av Azure Service Bus to Event Grid'
description: 'Självstudiekurs: Den här artikeln innehåller exempel på Service Bus-meddelanden och integrering av eventrutnät.'
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
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: fef325b67c38eda09a05dac9d74bd5b97df164cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067771"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Självstudiekurs: Svara på Azure Service Bus-händelser som tas emot via Azure Event Grid med hjälp av Azure Functions och Azure Logic Apps
I den här självstudien får du lära dig hur du svarar på Azure Service Bus-händelser som tas emot via Azure Event Grid med hjälp av Azure Functions och Azure Logic Apps. 

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett namnområde för Service Bus
> * Förbereda ett exempelprogram för att skicka meddelanden
> * Konfigurera en testfunktion på Azure
> * Anslut funktionen och namnområdet via Event Grid
> * Skicka meddelanden till servicebussavsnittet
> * Ta emot meddelanden med Azure Functions
> * Ta emot meddelanden med Logic Apps

## <a name="prerequisites"></a>Krav

För att kunna följa de här självstudierna ska du kontrollera att du har installerat:

- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) eller senare.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), version 2.0 eller senare.

## <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus
Följ instruktionerna i den här självstudien: [Snabbstart: Använd Azure-portalen för att skapa ett Service Bus-ämne och prenumerationer på ämnet](service-bus-quickstart-topics-subscriptions-portal.md) för att utföra följande uppgifter:

- Skapa **premium** ett premium-servicebussnamnområde. 
- Hämta anslutningssträngen. 
- Skapa ett servicebussämne.
- Skapa två prenumerationer på ämnet. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Förbereda ett exempelprogram för att skicka meddelanden
Du kan använda valfri metod för att skicka ett meddelande till ditt Service Bus-ämne. Exempelkoden i slutet av den här proceduren förutsätter att du använder Visual Studio 2017.

1. Klona [GitHub-lagringsplatsen azure-service-bus](https://github.com/Azure/azure-service-bus/).
2. Gå till mappen *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* i Visual Studio och öppna filen *SBEventGridIntegration.sln*.
3. Gå till projektet **MessageSender** och välj sedan **Program.cs**.
4. Fyll i ditt Service Bus-ämnesnamn och anslutningssträngen du fick från föregående steg:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Skapa och kör programmet för att skicka testmeddelanden till servicebussavsnittet. 

## <a name="set-up-a-test-function-on-azure"></a>Konfigurera en testfunktion på Azure 
Innan du arbetar igenom hela scenariot ställer du in minst en liten testfunktion som du kan använda för att felsöka och observera de händelser som flödar. Följ instruktionerna i artikeln [Skapa din första funktion i Azure Portal](../azure-functions/functions-create-first-azure-function.md) för att utföra följande uppgifter: 

1. Skapa en funktionsapp.
2. Skapa en HTTP-utlöst funktion. 

Gör sedan följande: 


# <a name="azure-functions-v2"></a>[Azure-funktioner V2](#tab/v2)

1. Expandera **Funktioner** i trädvyn och välj din funktion. Ersätt funktionens kod med följande kod: 

    ```csharp
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

    ![Utdata för funktionsappar](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Välj **Hämta funktions-URL** och lägg ned webbadressen. 

    ![Hämta funktionswebbadress](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1"></a>[Azure-funktioner V1](#tab/v1)

1. Konfigurera funktionen för att använda **V1-versionen:** 
    1. Välj funktionsappen i trädvyn och välj **Funktionsappinställningar**. 

        ![Funktionsappinställningar]()./media/service-bus-to-event-grid-integration-exempel/funktion-app-settings.png)
    2. Välj **~1** för **Runtime-version**. 
2. Expandera **Funktioner** i trädvyn och välj din funktion. Ersätt funktionens kod med följande kod: 

    ```csharp
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

    ![Utdata för funktionsappar](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Välj **Hämta funktions-URL** och lägg ned webbadressen. 

    ![Hämta funktionswebbadress](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Anslut funktionen och namnområdet via Event Grid
I det här avsnittet knyter du ihop funktionen och servicebussens namnområde med hjälp av Azure-portalen. 

Så här skapar du en Azure Event Grid-prenumeration:

1. Gå till ditt namnområde i Azure-portalen och välj sedan **Händelser**i den vänstra rutan . Ditt namnområdesfönster öppnas med två Event Grid-prenumerationer i fönstret till höger. 
    
    ![Service Bus - evenemang sida](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Välj **+ Händelseprenumeration** i verktygsfältet. 
3. Gör följande på sidan **Skapa händelseprenumeration:**
    1. Ange ett **namn** för prenumerationen. 
    2. Välj **Webbkrok** för **slutpunktstyp**. 

        ![Service Bus - Event Grid-prenumeration](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Välj **Välj en slutpunkt,** klistra in funktionens URL och välj sedan **Bekräfta markering**. 

        ![Funktion - välj slutpunkt](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Växla till fliken **Filter,** ange namnet på den **första prenumerationen** på det Service Bus-ämne som du skapade tidigare och välj sedan knappen **Skapa.** 

        ![Filter för händelseprenumeration](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Bekräfta att du ser händelseprenumerationen i listan.

    ![Händelseprenumeration i listan](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Skicka meddelanden till servicebussavsnittet
1. Kör .NET C#-programmet, som skickar meddelanden till servicebussavsnittet. 

    ![Utdata för konsolappar](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. På sidan för din Azure-funktionsapp expanderar du **Funktioner,** utökar **funktionen**och väljer **Övervaka**. 

    ![Övervaka funktion](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Ta emot meddelanden med Azure Functions
I det föregående avsnittet visade vi ett enkelt test och felsökningsscenario där du kunde se att händelser flödar in. 

I det här avsnittet lär du dig hur du tar emot och bearbetar meddelanden när du har fått en händelse.

### <a name="publish-a-function-from-visual-studio"></a>Publicera en funktion från Visual Studio
1. I samma Visual Studio-lösning **(SBEventGridIntegration)** som du öppnade väljer du **ReceiveMessagesOnEvent.cs** i **SBEventGridIntegration-projektet.** 
2. Ange din Service Bus-anslutningssträng i följande kod:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Ladda ner **publicera profilen** för funktionen:
    1. Välj din funktionsapp. 
    2. Välj fliken **Översikt** om den inte redan är markerad. 
    3. Välj **Hämta publiceringsprofil** i verktygsfältet. 

        ![Hämta publiceringsprofil för funktionen](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Spara filen i projektets mapp. 
4. Högerklicka sedan på **SBEventGridIntegration** i Visual Studio och välj sedan **Publicera**. 
5. Välj **Start** på sidan **Publicera.** 
6. På sidan **Välj ett publiceringsmål** gör du följande steg och väljer **Importera profil**. 

    ![Knappen Importera profil - knappen Importera profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Välj den **publicera profilfil** som du hämtade tidigare. 
8. Välj **Publicera** på sidan **Publicera.** 

    ![Visual Studio - Publicera](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Bekräfta att du ser den nya Azure-funktionen **ReceiveMessagesOnEvent**. Uppdatera sidan om det behövs. 

    ![Bekräfta att den nya funktionen har skapats](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Hämta webbadressen till den nya funktionen och anteckna den. 

### <a name="event-grid-subscription"></a>Prenumeration på Event Grid

1. Ta bort den befintliga Event Grid-prenumerationen:
    1. Välj **Händelser** på den vänstra menyn på sidan **Service Bus-namnområde.** 
    2. Välj den befintliga händelseprenumerationen. 
    3. På sidan **Händelseprenumeration** väljer du **Ta bort**.
2. Följ instruktionerna i avsnittet [Anslut funktionen och namnområdet via Event Grid](#connect-the-function-and-namespace-via-event-grid) för att skapa en Event Grid-prenumeration med den nya funktions-URL:en.
3. Följ instruktionerna i [avsnittet Skicka meddelanden till avsnittet Servicebuss](#send-messages-to-the-service-bus-topic) om du vill skicka meddelanden till ämnet och övervaka funktionen. 

## <a name="receive-messages-by-using-logic-apps"></a>Ta emot meddelanden med Logic Apps
Anslut en logikapp med Azure Service Bus och Azure Event Grid genom att följa dessa steg:

1. Skapa en logikapp i Azure-portalen.
    1. Välj **+ Skapa en resurs,** välj **Integration**och välj sedan **Logic App**. 
    2. På sidan **Logikapp - Skapa** anger du ett **namn** på logikappen.
    3. Välj din **Azure-prenumeration**. 
    4. Välj **Använd befintligt** för **resursgruppen**och välj den resursgrupp som du använde för andra resurser (till exempel Azure-funktionen, Service Bus-namnområdet) som du skapade tidigare. 
    5. Välj **plats** för logikappen. 
    6. Välj **Skapa** om du vill skapa logikappen. 
2. På sidan **Logic Apps Designer** väljer du Tom Logic **App** under **Mallar**. 
3. Gör följande på designern:
    1. Sök efter **händelserutnät**. 
    2. Välj **När en resurshändelse inträffar (förhandsgranskning) - Azure Event Grid**. 

        ![Logic Apps Designer - välj utlösare av händelserutnät](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Välj **Logga in,** ange dina Azure-autentiseringsuppgifter och välj **Tillåt åtkomst**. 
5. Gör följande på sidan **När en resurshändelse inträffar:**
    1. Välj din Azure-prenumeration. 
    2. För **resurstyp**väljer du **Microsoft.ServiceBus.Namespaces**. 
    3. För **Resursnamn**väljer du namnområdet Service Bus. 
    4. Välj **Lägg till ny parameter**och välj **Suffixfilter**. 
    5. För **Suffixfilter**anger du namnet på din andra servicebussämnesprenumeration. 
        ![Logic Apps Designer - konfigurera händelse](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Välj **+ Nytt steg** i designern och gör följande:
    1. Sök efter **servicebuss**.
    2. Välj **Servicebuss** i listan. 
    3. Välj för **Hämta meddelanden** i listan **Åtgärder.** 
    4. Välj **Hämta meddelanden från en ämnesprenumeration (peek-lock)**. 

        ![Logic Apps Designer - få meddelandeåtgärd](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Ange ett **namn på anslutningen**. Till exempel: **Hämta meddelanden från ämnesprenumerationen**och välj namnområdet Service Bus. 

        ![Logic Apps Designer - välj servicebussnamnområdet](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Välj **RootManageSharedAccessKey**.

        ![Logic Apps Designer – välj nyckeln för delad åtkomst](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Välj **Skapa**. 
    8. Välj ämne och prenumeration. 
    
        ![Logic Apps Designer - välj ditt Service Bus-ämne och din prenumeration](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Välj **+ Nytt steg**och gör följande: 
    1. Välj **Service Bus**.
    2. Välj **Slutför meddelandet i en ämnesprenumeration** i listan över åtgärder. 
    3. Välj ditt **servicebussämne**.
    4. Välj den andra **prenumerationen** på ämnet.
    5. För **Lås token för meddelandet**väljer du Lås **token** från **dynamiskt innehåll**. 

        ![Logic Apps Designer - välj ditt Service Bus-ämne och din prenumeration](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Välj **Spara** i verktygsfältet i Logic Apps Designer för att spara logikappen. 
9. Följ instruktionerna i [avsnittet Skicka meddelanden till avsnittet Servicebuss](#send-messages-to-the-service-bus-topic) om du vill skicka meddelanden till ämnet. 
10. Växla till **sidan Översikt i** logikappen. Logikappen körs i **historiken Runs** för de skickade meddelandena.

    ![Logic Apps Designer - logikapp körs](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

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
