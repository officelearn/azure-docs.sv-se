---
title: 'Självstudier: Registrera händelser från ett Azure Digital Twins-utrymme | Microsoft Docs'
description: Lär dig hur du tar emot meddelanden från dina utrymmet genom att integrera Azure Digital Twins med Logic Apps med hjälp av stegen i den här självstudien.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/18/2018
ms.author: dkshir
ms.openlocfilehash: 524ca96687e9395b65ec513326ad0fd4f7c6d429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57528915"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Självstudier: Ta emot meddelanden från dina Azure Digital Twins-utrymmen med hjälp av Logic Apps

När du har distribuerat din Azure Digital Twins-instans, etablerat dina utrymmen och implementerat anpassade funktioner för att övervaka specifika villkor kan du meddela din kontorsadministratör via e-post när de övervakade villkoren är uppfyllda.

I [den första självstudien](tutorial-facilities-setup.md) konfigurerade du den rumsliga grafen för en föreställd byggnad. Ett rum i byggnaden innehåller sensorer för rörelse, koldioxid och temperatur. I [den andra självstudien](tutorial-facilities-udf.md) etablerade du diagrammet och en användardefinierad funktion för att övervaka dessa sensorvärden och utlösa meddelanden när utrymmet är tomt och temperatur och kolkoldioxid är i ett lämpligt intervall. 

Den här självstudien visar hur du kan integrera dessa meddelanden med Azure Logic Apps för att skicka e-postmeddelanden när det finns ett sådant rum tillgängligt. En kontorsadministratör kan använda den här informationen för att hjälpa medarbetarna att boka det mest produktiva mötesrummet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Integrera händelser med Azure Event Grid.
> * Meddela händelser med Logic App.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du har [konfigurerat](tutorial-facilities-setup.md) och [etablerat](tutorial-facilities-udf.md) Azure Digital Twins-konfigurationen. Innan du fortsätter bör du kontrollera att du har:

- Ett [Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En instans av Digital Twins som körs.
- [Digital Twins C#-exemplen](https://github.com/Azure-Samples/digital-twins-samples-csharp) nedladdade och extraherade på din arbetsdator.
- [.NET Core SDK version 2.1.403 eller senare](https://www.microsoft.com/net/download) på utvecklingsdatorn för att köra exemplet. Kör `dotnet --version` för att kontrollera att rätt version är installerad.
- Ett Office 365-konto för att skicka e-postmeddelanden.

## <a name="integrate-events-with-event-grid"></a>Integrera händelser med Event Grid

I det här avsnittet konfigurerar du en [Event Grid](../event-grid/overview.md) för att samla in händelser från din Azure Digital Twins-instans och omdirigerar dem till en [händelsehanterare](../event-grid/event-handlers.md) såsom Logic Apps.

### <a name="create-an-event-grid-topic"></a>Skapa ett Event Grid-ämne

[Event Grid-ämnen](../event-grid/concepts.md#topics) tillhandahåller ett gränssnitt för att dirigera händelser som genereras av den användardefinierade funktionen. 

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **Skapa en resurs** i fönstret till vänster. 

1. Sök efter och välj **Event Grid-ämne**. Välj **Skapa**.

1. Ange ett **namn** för Event Grid-ämnet och välj **prenumeration**. Välj den **resursgrupp** som du använde eller skapade för din Digital Twins-instans samt **plats**. Välj **Skapa**. 

    ![Skapa ett Event Grid-ämne](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Gå till Event Grid-ämnet från resursgruppen, välj **Översikt** och kopiera värdet för **Ämnesslutpunkt** till en temporär fil. Du behöver webbadressen i nästa avsnitt. 

1. Välj **Åtkomstnycklar** och kopiera **YOUR_KEY_1** och **YOUR_KEY_2** (nyckel 1 och nyckel 2) till en temporär fil. Du behöver dessa värden för att skapa slutpunkten i nästa avsnitt.

    ![Event Grid-nycklar](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Skapa en slutpunkt för Event Grid-ämnet

1. I kommandofönstret kontrollerar du att du är i mappen **occupancy-quickstart\src** för Digital Twins-exemplet.

1. Öppna filen **actions\createEndpoints.yaml** i Visual Studio Code-redigeringsprogrammet. Kontrollera att den har följande innehåll:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. Ersätt platshållaren `Primary_connection_string_for_your_Event_Grid` med värdet för **YOUR_KEY_1**.

1. Ersätt platshållaren `Secondary_connection_string_for_your_Event_Grid` med värdet för **YOUR_KEY_2**.

1. Ersätt platshållaren `Event_Grid_Topic_Path` med sökvägen till Event Grid-ämnet. Hämta den här sökvägen genom att ta bort den **https://** och avslutande resurssökvägar från **Ämnesslutpunkt**-URL: en. Det bör se ut ungefär som det här formatet: *yourEventGridName.yourLocation.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Ange alla värden utan citattecken. Se till att det finns minst ett blanksteg efter kolonen i YAML-filen. Du kan även verifiera YAML-filinnehållet med hjälp av en YAML-onlineverifierare, till exempel [det här verktyget](https://onlineyamltools.com/validate-yaml).

1. Spara och stäng filen. I kommandofönstret kör du följande kommando och loggar in när du tillfrågas. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Det här kommandot skapar slutpunkten för Event Grid. 

   ![Slutpunkter för Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)

## <a name="notify-events-with-logic-apps"></a>Meddela händelser med Logic Apps

Du kan använda [Azure Logic Apps](../logic-apps/logic-apps-overview.md)-tjänsten för att skapa automatiserade uppgifter för händelser som tas emot från andra tjänster. I det här avsnittet konfigurerar du Logic Apps att skapa e-postmeddelanden för händelser som dirigeras från dina spatiala sensorer, med hjälp av ett [Event Grid-ämne](../event-grid/overview.md).

1. Välj **Skapa en resurs** längst upp till vänster i [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj en ny **logikappresurs**. Välj **Skapa**.

1. Ange ett **namn** för logikappresursen och välj sedan din **prenumeration**, din **resursgrupp** samt **plats**. Välj **Skapa**.

    ![Skapa en resurs för en logikapp](./media/tutorial-facilities-events/create-logic-app.png)

1. Öppna logikappresursen när den har distribuerats och öppna sedan fönsterrutan **Logic App Designer**. 

1. Välj utlösaren **When an Event Grid event occurs** (När en Event Grid-händelse inträffar). Logga in till din klientorganisation med Azure-kontot när du tillfrågas. Välj **Tillåt åtkomst** för Event Grid när du tillfrågas. Välj **Fortsätt**.

1. I fönstret **When a resource event occurs (Preview)** (När en resurshändelse inträffar (förhandsversion)): 
   
   a. Välj den **prenumeration** som du använde för att skapa Event Grid-ämnet.

   b. Välj **Microsoft.EventGrid.Topics** som **Resurstyp**.

   c. Välj din Event Grid-resurs från listrutan för **Resursnamn**.

   ![Fönstret Logic App Designer](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Välj knappen **Nytt steg**.

1. I fönstret **Välj en åtgärd**:

   a. söker du efter frasen **parse json** och väljer åtgärden **Parsa JSON**.

   b. I fältet **Innehåll** markerar du **Brödtext** på listan **Dynamiskt innehåll**.

   c. Markera **Use sample to payload to generate schema** (Använd exempelnyttolast för att generera schema). Klistra in följande JSON-nyttolast och välj sedan **Klar**.

    ```JSON
    {
    "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
      "ResourceType": "Space",
      "Payload": "\"Air quality is poor.\"",
      "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
    },
    "eventType": "UdfCustom",
    "eventTime": "0001-01-01T00:00:00Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
    }
    ```

    Den här nyttolasten har fiktiva värden. Logic App använder den här exempelnyttolasten för att generera ett *schema*.

    ![Logic Apps Parsa JSON-fönstret för Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Välj knappen **Nytt steg**.

1. I fönstret **Välj en åtgärd**:

   a. Välj **Kontroll > Villkor** eller sök efter **Villkor** i listan **Åtgärder**. 

   b. I den första textrutan **Välj ett värde** väljer du **eventType** (händelsetyp) från listan **Dynamiskt innehåll** för fönstret **Parsa JSON**.

   c. I den textrutan med **Välj ett värde** anger du `UdfCustom`.

   ![Valda villkor](./media/tutorial-facilities-events/logic-app-condition.png)

1. I fönstret **If true** (Om sant):

   a. Markera **Lägg till en åtgärd** och väljer **Office 365 Outlook**.

   b. I listan med **Åtgärder** väljer du **Skicka ett e-postmeddelande**. Välj **Logga in** och använd autentiseringsuppgifterna för ditt e-postkonto. Välj **Tillåt åtkomst** när du tillfrågas.

   c. I rutan **Till** anger du ditt e-post-ID för att få meddelanden. I **Ämne** anger du texten **Digital Twins notification for poor air quality in space** (Digital Twins-meddelande för dålig luftkvalitet i utrymmet). Välj sedan **TopologyObjectId** på listan **Dynamiskt innehåll** för **Parsa JSON**.

   d. Under **Brödtext** i samma fönster anger du text som liknar detta: **Dålig luftkvalitet har identifierats i ett rum, och temperaturen behöver justeras**. Du kan utveckla detta med element från listan **Dynamiskt innehåll**.

   ![Logic Apps-val för ”Skicka ett e-postmeddelande”](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Välj knappen **Spara** längst upp på fönsterrutan **Logic App Designer**.

1. Se till att simulera sensordata genom att gå till mappen **device-connectivity** för Digital Twin-exemplet i ett kommandofönster och köra `dotnet run`.

Om några minuter bör du börja få e-postmeddelanden från den här Logic Apps-resursen. 

   ![E-postavisering](./media/tutorial-facilities-events/logic-app-notification.png)

Om du vill sluta få dessa e-postmeddelanden går du till din logikappresurs i portalen och väljer fönsterrutan **Översikt**. Välj **Inaktivera**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill utforska Azure Digital Twins nu kan du ta bort resurser som du har skapat i den här självstudien:

1. På den vänstra menyn i [Azure-portalen](https://portal.azure.com) väljer du **Alla resurser**, väljer din Digital Twins-resursgrupp och **Ta bort**.

    > [!TIP]
    > Om det inträffade problem när du skulle ta bort Digital Twins-instansen finns det nu en tjänstuppdatering som åtgärdar det. Försök att ta bort instansen igen.

2. Ta bort exempelprogrammen på datorn om det behövs.

## <a name="next-steps"></a>Nästa steg

Du kan gå till nästa självstudie och lära dig att visualisera dina sensordata, analysera trender och upptäcka avvikelser:

> [!div class="nextstepaction"]
> [Självstudier: Visualisera och analysera händelser från dina Azure Digital Twins-utrymmen med hjälp av Time Series Insights](tutorial-facilities-analyze.md)

Du kan även lära dig mer om diagram för spatial intelligens och objektmodeller i Azure Digital Twins:

> [!div class="nextstepaction"]
> [Förstå Digital Twins-objektmodeller och diagram för spatial intelligens](concepts-objectmodel-spatialgraph.md)
