---
title: 'Självstudie: Azure Service Bus till Event Grid integrations exempel'
description: 'Självstudie: den här artikeln innehåller exempel på Service Bus meddelande-och Event Grid-integration.'
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 6f64c8155724a1306f5074a9164bcf97391dfbf6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000353"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Självstudie: svara på Azure Service Bus händelser som tas emot via Azure Event Grid med Azure Functions och Azure Logic Apps
I den här självstudien får du lära dig hur du svarar på Azure Service Bus händelser som tas emot via Azure Event Grid med Azure Functions och Azure Logic Apps. 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett namnområde för Service Bus
> * Förbereda ett exempel program för att skicka meddelanden
> * Skicka meddelanden till Service Bus ämnet
> * Ta emot meddelanden med Logic Apps
> * Konfigurera en test funktion på Azure
> * Anslut funktionen och namnområdet via Event Grid
> * Ta emot meddelanden med Azure Functions

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa de här självstudierna ska du kontrollera att du har installerat:

- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) eller senare.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), version 2.0 eller senare.

## <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus
Följ anvisningarna i den här självstudien: [snabb start: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på avsnittet](service-bus-quickstart-topics-subscriptions-portal.md) för att utföra följande uppgifter:

- Skapa ett **premium** Service Bus-namnområde. 
- Hämta anslutnings strängen. 
- Skapa ett Service Bus ämne.
- Skapa två prenumerationer på avsnittet. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Förbereda ett exempel program för att skicka meddelanden
Du kan använda valfri metod för att skicka ett meddelande till ditt Service Bus-ämne. I exempel koden i slutet av den här proceduren förutsätter vi att du använder Visual Studio 2017.

1. Klona [GitHub-lagringsplatsen azure-service-bus](https://github.com/Azure/azure-service-bus/).
2. Gå till mappen *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* i Visual Studio och öppna filen *SBEventGridIntegration.sln*.
3. Gå till projektet **MessageSender** och välj sedan **Program.cs**.
4. Fyll i namnet på Service Bus ämnet och anslutnings strängen du fick från föregående steg:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Uppdatera `numberOfMessages` värdet till **5**. 
5. Skapa och kör programmet för att skicka test meddelanden till avsnittet Service Bus. 

## <a name="receive-messages-by-using-logic-apps"></a>Ta emot meddelanden med Logic Apps
Anslut en Logic app med Azure Service Bus och Azure Event Grid genom att följa dessa steg:

1. Skapa en Logic-app i Azure Portal.
    1. Välj **+ skapa en resurs**, Välj **integration**och välj sedan **Logic app**. 
    2. På sidan **Logic app – skapa** anger du ett **namn** för Logic app.
    3. Välj din Azure- **prenumeration**. 
    4. Välj **Använd befintlig** för **resurs gruppen**och välj den resurs grupp som du använde för andra resurser (t. ex. Azure Function, Service Bus namnrymd) som du skapade tidigare. 
    5. Välj **plats** för Logic-appen. 
    6. Välj **skapa** för att skapa Logic-appen. 
2. På sidan **Logic Apps designer** väljer du **Tom Logic app** under **mallar**. 
3. I designern utför du följande steg:
    1. Sök efter **Event Grid**. 
    2. Välj **när en resurs händelse inträffar – Azure Event Grid**. 

        ![Logic Apps designer – Välj Event Grid utlösare](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Välj **Logga**in, ange dina autentiseringsuppgifter för Azure och välj **Tillåt åtkomst**. 
5. Utför följande steg på sidan **när en resurs händelse inträffar** :
    1. Välj din Azure-prenumeration. 
    2. För **resurs typ**väljer du **Microsoft. Service Bus. Namespaces**. 
    3. För **resurs namn**väljer du namn området för Service Bus. 
    4. Välj **Lägg till ny parameter**och välj **suffix filter**. 
    5. Vid **filter för suffix**anger du namnet på din andra Service Bus ämnes prenumeration. 
        ![Logic Apps designer – konfigurera händelse](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Välj **+ nytt steg** i designern och utför följande steg:
    1. Sök efter **Service Bus**.
    2. Välj **Service Bus** i listan. 
    3. Välj för **Hämta meddelanden** i listan **åtgärder** . 
    4. Välj **Hämta meddelanden från en ämnes prenumeration (Peek-lock)**. 

        ![Logic Apps designer – åtgärd för att hämta meddelanden](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Ange ett **namn för anslutningen**. Exempel: **Hämta meddelanden från ämnes prenumerationen**och välj Service Bus namn området. 

        ![Logic Apps designer – Välj namn området Service Bus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Välj **RootManageSharedAccessKey**och välj sedan **skapa**.

        ![Logic Apps designer – Välj den delade åtkomst nyckeln](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Välj ditt **ämne** och din **prenumeration**. 
    
        ![Logic Apps designer – Välj ditt Service Bus ämne och prenumeration](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Välj **+ nytt steg**och utför följande steg: 
    1. Välj **Service Bus**.
    2. Välj **Slutför meddelandet i en ämnes prenumeration** i listan med åtgärder. 
    3. Välj ditt Service Bus **ämne**.
    4. Välj den andra **prenumerationen** för ämnet.
    5. För **meddelandets lås-token**väljer du **Lås token** från det **dynamiska innehållet**. 

        ![Logic Apps designer – Välj ditt Service Bus ämne och prenumeration](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Spara Logic-appen genom att välja **Spara** i verktygsfältet på Logic Apps designer. 
9. Följ anvisningarna i avsnittet [skicka meddelanden till Service Bus ämne](#send-messages-to-the-service-bus-topic) för att skicka meddelanden till ämnet. 
10. Växla till **översikts** sidan för din Logic app. Du ser att Logic App körs i **körnings historiken** för de meddelanden som skickas.

    ![Logic Apps designer – Logic App körs](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="set-up-a-test-function-on-azure"></a>Konfigurera en test funktion på Azure 
Innan du går igenom hela scenariot ställer du in minst en liten test funktion som du kan använda för att felsöka och observera de händelser som flödar. Följ anvisningarna i [skapa din första funktion i Azure Portal](../azure-functions/functions-create-first-azure-function.md) artikeln för att utföra följande uppgifter: 

1. Skapa en Function-app.
2. Skapa en HTTP-utlöst funktion. 

Utför sedan följande steg: 


# <a name="azure-functions-v2"></a>[Azure Functions v2](#tab/v2)

1. Expandera **funktioner** i trädvyn och välj din funktion. Ersätt koden för funktionen med följande kod: 

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
                log.LogInformation("Validating the subscription");            
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation($"Validation code: {code}");
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
2. Välj **Spara** i verktygsfältet för att spara koden för funktionen.

    ![Spara funktions kod](./media/service-bus-to-event-grid-integration-example/save-function-code.png)
3. Välj **testa/kör** i verktygsfältet och utför följande steg: 
    1. Ange följande JSON i **bröd texten**.

        ```json
        [{
          "id": "64ba80ae-9f8e-425f-8bd7-d88d2c0ba3e3",
          "topic": "/subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/spegridsbusrg/providers/Microsoft.ServiceBus/namespaces/spegridsbusns",
          "subject": "",
          "data": {
            "validationCode": "D7D825D4-BD04-4F73-BDE3-70666B149857",
            "validationUrl": "https://rp-eastus.eventgrid.azure.net:553/eventsubscriptions/spsbusegridsubscription/validate?id=D7D825D4-BD04-4F73-BDE3-70666B149857&t=2020-06-09T18:28:51.5724615Z&apiVersion=2020-04-01-preview&[Hidden Credential]"
          },
          "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
          "eventTime": "2020-06-09T18:28:51.5724615Z",
          "metadataVersion": "1",
          "dataVersion": "2"
        }]
        ```    
    2. Klicka på **Lägg till sidhuvud**och Lägg till ett sidhuvud med namn `aeg-event-type` och värde `SubscriptionValidation` . 
    3. Välj **Kör**. 

        ![Kör test](./media/service-bus-to-event-grid-integration-example/test-run-function.png)
    4. Bekräfta att du ser retur status koden **OK** och verifierings koden i svars texten. Se även informationen som loggats av funktionen. 

        ![Testkörning-svar](./media/service-bus-to-event-grid-integration-example/test-function-response.png)        
3. Välj **Hämta funktions webb adress** och anteckna URL: en. 

    ![Hämta funktionswebbadress](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Välj **kopierings** knappen bredvid URL-texten.    
    ![Kopiera funktions webb adress](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

# <a name="azure-functions-v1"></a>[Azure Functions v1](#tab/v1)

1. Konfigurera funktionen för att använda **v1** -version: 
    1. Välj din Function-app i trädvyn och välj **Function app-inställningar**. 
    2. Välj **~ 1** för **körnings version**. 
2. Expandera **funktioner** i trädvyn och välj din funktion. Ersätt koden för funktionen med följande kod: 

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

    ![Funktion för programmets utdata](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Välj **Hämta funktions webb adress** i verktygsfältet. 

    ![Hämta funktionswebbadress](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Välj **kopierings** knappen bredvid URL-texten.    
    ![Kopiera funktions webb adress](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Anslut funktionen och namnområdet via Event Grid
I det här avsnittet binder du samman funktionen och Service Bus namn området med hjälp av Azure Portal. 

Följ dessa steg om du vill skapa en Azure Event Grid-prenumeration:

1. Gå till ditt namn område i Azure Portal och välj sedan **händelser**i det vänstra fönstret. Ditt namnområdesfönster öppnas med två Event Grid-prenumerationer i fönstret till höger. 
    
    ![Sidan Service Bus-händelser](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Välj **+ händelse prenumeration** i verktygsfältet. 
3. Utför följande steg på sidan **Skapa händelse prenumeration** :
    1. Ange ett **namn** för prenumerationen. 
    2. Ange ett **namn** för **system-ämnet**. System ämnen är avsnitt som skapats för Azure-resurser, till exempel Azure Storage konto och Azure Service Bus. Mer information om system ämnen finns i [Översikt över system avsnitt](../event-grid/system-topics.md).
    2. Välj **Web Hook** för **slut punkts typ**. 

        ![Service Bus-Event Grid-prenumeration](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Välj **Välj en slut punkt**, klistra in funktions webb adressen och välj sedan **Bekräfta markering**. 

        ![Funktion-välj slut punkten](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Växla till fliken **filter** och utför följande aktiviteter:
        1. Välj **Aktivera ämnes filtrering**
        2. Ange namnet på den **första prenumerationen** på Service Bus avsnittet som du skapade tidigare.
        3. Välj knappen **Skapa**. 

            ![Filter för händelse prenumeration](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Växla till fliken **händelse prenumerationer** på sidan **händelser** och bekräfta att du ser händelse prenumerationen i listan.

    ![Händelse prenumeration i listan](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Skicka meddelanden till Service Bus ämnet
1. Kör .NET C#-programmet som skickar meddelanden till Service Bus avsnittet. 

    ![Utdata för konsol program](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. På sidan för din Azure Function-app växlar du till fliken **övervakning** från fliken **kod + test** . Du bör se en post för varje meddelande som publiceras i avsnittet Service Bus. Om du inte ser dem uppdaterar du sidan efter att ha väntat några minuter. 

    ![Övervaknings funktion](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

    Du kan också använda fliken **loggar** på **Monitor** -sidan för att se loggnings informationen när meddelandena skickas. Det kan ta en stund att se om det finns några minuter att se de loggade meddelandena. 

## <a name="receive-messages-by-using-azure-functions"></a>Ta emot meddelanden med Azure Functions
I det föregående avsnittet visade vi ett enkelt test och felsökningsscenario där du kunde se att händelser flödar in. 

I det här avsnittet lär du dig hur du tar emot och bearbetar meddelanden när du har fått en händelse.

### <a name="publish-a-function-from-visual-studio"></a>Publicera en funktion från Visual Studio
1. I samma Visual Studio-lösning (**SBEventGridIntegration**) som du öppnade väljer du **ReceiveMessagesOnEvent.cs** i **SBEventGridIntegration** -projektet. 
2. Ange Service Bus anslutnings sträng i följande kod:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Hämta **publicerings profilen** för funktionen:
    1. Välj din funktionsapp. 
    2. Välj fliken **Översikt** om den inte redan är markerad. 
    3. Välj **Hämta publicerings profil** i verktygsfältet. 

        ![Hämta publicerings profil för funktionen](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Spara filen i projektets mapp. 
4. Högerklicka sedan på **SBEventGridIntegration** i Visual Studio och välj sedan **Publicera**. 
5. Utför följande steg på sidan **publicera**: 
    1. Välj **Starta** på sidan **publicera** 
    2. För **målet**väljer du **Importera profil**. 
    3. Välj **Nästa**. 

        ![Knappen Visual Studio – import profil](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Välj den **publicerings profil fil** som du laddade ned tidigare och välj **Slutför**.

    ![Välj publicerings profil](./media/service-bus-to-event-grid-integration-example/select-publish-profile.png)
8. Välj **publicera** på **publicerings** sidan. 

    ![Visual Studio – publicera](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Bekräfta att du ser den nya Azure Function- **ReceiveMessagesOnEvent**. Uppdatera sidan om det behövs. 

    ![Bekräfta att den nya funktionen har skapats](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Hämta URL: en till den nya funktionen och anteckna den. 

### <a name="event-grid-subscription"></a>Event Grid prenumeration

1. Ta bort den befintliga Event Grid prenumerationen:
    1. På sidan **Service Bus namn område** väljer du **händelser** på den vänstra menyn. 
    2. Växla till fliken **händelse prenumerationer** . 
    2. Välj den befintliga händelse prenumerationen. 

        ![Välj händelse prenumeration](./media/service-bus-to-event-grid-integration-example/select-event-subscription.png)
    3. På sidan **händelse prenumeration** väljer du **ta bort**. Bekräfta borttagningen genom att välja **Ja** . 
        ![Knappen Ta bort händelse prenumeration](./media/service-bus-to-event-grid-integration-example/delete-subscription-button.png)
2. Följ anvisningarna i avsnittet [Anslut funktionen och namn området via Event Grid](#connect-the-function-and-namespace-via-event-grid) för att skapa en Event Grid-prenumeration med hjälp av den nya funktions-URL: en.
3. Följ anvisningarna i avsnittet [skicka meddelanden till Service Bus ämne](#send-messages-to-the-service-bus-topic) för att skicka meddelanden till ämnet och övervaka funktionen. 


## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Event Grid](../event-grid/index.yml).
* Läs mer om [Azure Functions](../azure-functions/index.yml).
* Läs mer om [Logic Apps-funktionen i Azure App Service](../logic-apps/index.yml).
* Läs mer om [Azure Service Bus](/azure/service-bus/).


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