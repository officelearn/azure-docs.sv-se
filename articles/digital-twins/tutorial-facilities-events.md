---
title: Registrera händelser från ett Azure Digital Twins-utrymme | Microsoft Docs
description: Lär dig hur du tar emot meddelanden från dina utrymmet genom att integrera Azure Digital Twins med Logic Apps med hjälp av stegen i den här självstudien.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 91dd16938efbd1e24419352f66e3238646a77e8a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323436"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-using-logic-apps"></a>Självstudie: Ta emot meddelanden från dina Azure Digital Twins-utrymmen med hjälp av Logic Apps

När du har distribuerat din Azure Digital Twins-instans, etablerat dina utrymmen och implementerat anpassade funktioner för att övervaka specifika villkor kan du meddela din kontorsadministratör via e-post när de övervakade villkoren är uppfyllda. 

I [den första självstudien](tutorial-facilities-setup.md) konfigurerade du ett spatialt diagram över en imaginär byggnad, med ett rum som innehåller sensorer för rörelse, koldioxid och temperatur. I [den andra självstudien](tutorial-facilities-udf.md) etablerade du diagrammet och en anpassad funktion som kallas den användardefinierade funktionen för att övervaka dessa sensorvärden och utlösa meddelanden när utrymmet är tomt och temperatur och kolkoldioxid är i ett lämpligt intervall. Den här självstudien visar hur du kan integrera dessa meddelanden med Azure Logic Apps för att skicka e-postmeddelanden när det finns ett sådant rum tillgängligt. En kontorsadministratör kan använda den här informationen för att hjälpa medarbetarna att boka det mest produktiva mötesrummet. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Integrera händelser med Event Grid
> * Meddela händelser med Logic App
    
## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien förutsätter att du har [konfigurerat](tutorial-facilities-setup.md) och [etablerat](tutorial-facilities-udf.md) Azure Digital Twins-konfigurationen. Innan du fortsätter bör du kontrollera att du har:
- Ett [Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En instans av Digital Twins som körs.
- [Digital Twins C#-exemplen](https://github.com/Azure-Samples/digital-twins-samples-csharp) nedladdade och extraherade på din arbetsdator.
- [.NET Core SDK version 2.1.403 eller senare](https://www.microsoft.com/net/download) på utvecklingsdatorn för att köra exemplet. Kör `dotnet --version` för att kontrollera att rätt version är installerad. 
- Ett Office 365-konto för att skicka e-postmeddelanden.

## <a name="integrate-events-with-event-grid"></a>Integrera händelser med Event Grid 
I det här avsnittet konfigurerar du en [Event Grid](../event-grid/overview.md) för att samla in händelser från din Digital Twins-instans och omdirigerar dem till en [händelsehanterare](../event-grid/event-handlers.md) såsom Logic Apps.

### <a name="create-event-grid-topic"></a>Skapa ett Event Grid-ämne
[Event Grid-ämnen](../event-grid/concepts.md#topics) tillhandahåller ett gränssnitt för att dirigera händelser som genereras av den användardefinierade funktionen. 

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I den vänstra navigeringspanelen klickar du på **Skapa en resurs**. 

1. Sök efter och välj **Event Grid-ämne**. Klicka på **Skapa**.

1. Ange ett **namn** för Event Grid-ämnet och välj **prenumeration**. Välj den **resursgrupp** som du använde eller skapade för din Digital Twins-instans samt **plats**. Klicka på **Skapa**. 

    ![Skapa ett Event Grid-ämne](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Navigera till Event Grid-ämnet från resursgruppen, klicka på **Översikt** och kopiera värdet för **Ämnesslutpunkt** till en temporär fil. Du behöver den här URL:en i följande avsnitt. 

1. Klicka på **Åtkomstnycklar** och kopiera **Key 1** och **Key 2** (nyckel 1 och nyckel 2) till en temporär fil. Du behöver dessa värden för att skapa slutpunkten i följande avsnitt.

    ![Event Grid-nycklar](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Skapa en slutpunkt för Event Grid-ämnet

1. I kommandofönstret kontrollerar du att du är i mappen **_occupancy-quickstart\src_** för Digital Twins-exemplet.

1. Öppna filen **_actions\createEndpoints.yaml_** i Visual Studio Code-redigeringsprogrammet. Kontrollera att den har följande innehåll:

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

1. Ersätt platshållaren `Primary_connection_string_for_your_Event_Grid` med värdet för **Key1**. 

1. Ersätt platshållaren `Secondary_connection_string_for_your_Event_Grid` med värdet för **Key2**.

1. Ersätt platshållaren `Event_Grid_Topic_Path` med sökvägen till Event Grid-ämnet. Hämta den här sökvägen genom att ta bort den *https://* och avslutande resurssökvägar från **Ämnesslutpunkt**-URL: en. Det bör se ut ungefär som det här formatet: **yourEventGridName.yourLocation.eventgrid.azure.net**. 

    > [!IMPORTANT]
    > Ange alla värden utan citattecken. Se till att det finns minst ett blanksteg efter kolonen i *YAML*-filen. Du kan även verifiera *YAML*-filinnehållet med hjälp av en YAML-onlineverifierare, till exempel [det här verktyget](https://onlineyamltools.com/validate-yaml).

1. Spara och stäng filen. I kommandofönstret kör du följande kommando och loggar in när du tillfrågas. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Det här kommandot skapar slutpunkten för Event Grid. 

   ![Slutpunkter för Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-app"></a>Meddela händelser med Logic App
Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md)-tjänsten kan du skapa automatiserade uppgifter för händelser som tas emot från andra tjänster. I det här avsnittet konfigurerar du Logic Apps att skapa e-postmeddelanden för händelser som dirigeras från dina spatiala sensorer, med hjälp av ett [Event Grid-ämne](../event-grid/overview.md).

1. I det vänstra navigeringsfönstret i [Azure-portalen](https://portal.azure.com) klickar du på **Skapa en resurs**.

1. Sök efter och välj en ny **logikappresurs**. Klicka på **Skapa**.

1. Ange ett **namn** för logikappen och välj sedan din **prenumeration**, din **resursgrupp** samt **plats**. Klicka på **Skapa**.

    ![Skapa en logikapp](./media/tutorial-facilities-events/create-logic-app.png)

1. Öppna logikappen när den har distribuerats och öppna sedan fönsterrutan **Logic App Designer**. 

1. Välj utlösaren **When an Event Grid event occurs** (När en Event Grid-händelse inträffar). **Logga in** till din klientorganisation med Azure-kontot när du tillfrågas. Bekräfta att **tillåta åtkomst** till din Event Grid när du tillfrågas. Klicka på **Fortsätt**.

1. I fönstret **When a resource event occurs (Preview)** (När en resurshändelse inträffar (förhandsversion)) 
    1. väljer du den **prenumeration** som du använde för att skapa Event Grid tidigare,

    1. välj **Microsoft.EventGrid.Topics** som **Resurstyp** och

    1. välj din Event Grid-resurs från listrutan för **Resursnamn**.

    ![Skapa en logikapp](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Klicka på knappen **Nytt steg**.

1. I fönstret **Välj en åtgärd**
    1. söker du efter frasen *parse json* och väljer åtgärden **Parsa JSON**.

    1. Klicka i fältet **Innehåll** och markera **Brödtext** från listan **Dynamiskt innehåll**.

    1. Välj **Use sample to payload to generate schema** (Använd exempelnyttolast för att generera schema). Klistra in följande JSON-nyttolast och klicka sedan på **Klar**.

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
    
    Den här nyttolasten har fiktiva värden. Logic App använder den här exempelnyttolasten för att generera ett **Schemat**.
    
    ![Logic App Parsa JSON för Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Klicka på knappen **Nytt steg**.

1. I fönstret **Välj en åtgärd**
    1. Sök och välj **Villkorskontroll** från listan över **åtgärder**. 

    1. Klicka i den första textrutan **Välj ett värde** och välj **eventType** (händelsetyp) från listan **Dynamic content** för fönstret **Parsa JSON**.

    1. Klicka i den andra textrutan **Välj ett värde** och skriv *UdfCustom*.

    ![Logic App Parsa JSON för Event Grid](./media/tutorial-facilities-events/logic-app-condition.png)

1. I fönstret **If true** (Om sant)
    1. klickar du på **Lägg till en åtgärd** och väljer *Office 365 Outlook*.

    1. Från listan med **Åtgärder** väljer du **Skicka ett e-postmeddelande**. Klicka på **Logga in** och använd autentiseringsuppgifterna för ditt e-postkonto. Klicka på **Tillåt åtkomst** när du tillfrågas.

    1. I rutan **Till** anger du ditt e-post-ID för att få meddelanden. I **Ämne** anger du texten *Digital Twins-meddelande för dålig luftkvalitet i utrymmet* och väljer sedan **TopologyObjectId** från listan **Dynamiskt innehåll** för **Parsa JSON**.

    1. I **Brödtext** i samma fönster anger du text som liknar detta: *Dålig luftkvalitet har identifierats i ett rum och temperatur behöver justeras*. Du kan utveckla detta med element från listan **Dynamiskt innehåll** såsom det visas nedan.

    ![Logic App Skicka ett e-postmeddelande](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Klicka på knappen **Spara** längst upp på fönsterrutan **Logic App Designer**.

1. Se till att simulera sensordata genom att navigera till mappen **_device-connectivity_** för Digital Twin-exemplet i ett kommandofönster och köra `dotnet run`.

Om några minuter bör du börja få e-postmeddelanden från den här logikappen. 

   ![Logic App Skicka ett e-postmeddelande](./media/tutorial-facilities-events/logic-app-notification.png)

Om du vill sluta få dessa e-postmeddelanden navigerar du till din **logikapp** i portalen och väljer fönsterrutan **Översikt**. Klicka på **Inaktivera**.


## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill utforska Azure Digital Twins vidare kan du ta bort resurser som du har skapat i den här självstudien:

1. På den vänstra menyn i [Azure-portalen](http://portal.azure.com) klickar du på **Alla resurser**, väljer din Digital Twins-resursgrupp och **tar bort** den.
2. Om du vill kan fortsätta att ta bort exempelprogrammen på arbetsdatorn också. 


## <a name="next-steps"></a>Nästa steg

Du kan gå vidare till nästa självstudie och lär dig att visualisera dina sensordata, analysera trender och upptäcka avvikelser. 
> [!div class="nextstepaction"]
> [Självstudie: Visualisera och analysera händelser från dina Azure Digital Twins-utrymmen med hjälp av Time Series Insights](tutorial-facilities-analyze.md)

Du kan även gå vidare till mer information om diagram för spatial intelligens och objektmodeller i Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Förstå Digital Twins-objektmodeller och diagram för spatial intelligens](concepts-objectmodel-spatialgraph.md)