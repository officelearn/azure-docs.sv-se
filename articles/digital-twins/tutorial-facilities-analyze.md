---
title: 'Självstudie: analysera händelser i Time Series Insights – digitala Azure-dubbla Microsoft Docs'
description: Lär dig mer om att visualisera och analysera händelser från dina Azure Digital Twins-utrymmen, med Azure Time Series Insights, med hjälp av stegen i den här självstudien.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: cab13fd65e9fdbd7179e6ba759b1aa696ef95fa1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790327"
---
# <a name="tutorial-visualize-and-analyze-events-from-azure-digital-twins-by-using-time-series-insights"></a>Självstudie: visualisera och analysera händelser från Azure Digitals dubbla med hjälp av Time Series Insights

När du har distribuerat din instans av Azure Digital Twins, etablerat din utrymmen och implementerat en anpassad funktion för att övervaka specifika villkor kan du sedan visualisera händelser och data från din utrymmen för att leta efter trender och avvikelser.

I [den första självstudien](tutorial-facilities-setup.md) konfigurerade du ett spatialt diagram över en imaginär byggnad, med ett rum som innehåller sensorer för rörelse, koldioxid och temperatur. I [den andra självstudien](tutorial-facilities-udf.md) har du etablerat ett diagram och en användardefinierad funktion. Funktionen övervakar sensorvärdena och utlöser aviseringar för villkor som är rätt. Det vill säga att rummet är tomt och temperatur- och koldioxidnivåer är normala.

Den här kursen visar hur du kan integrera meddelanden och data från din Azure Digital Twins-konfiguration med Azure Time Series Insights. Sedan kan du visualisera dina sensorvärden över tid. Du kan söka efter trender som vilket rum som får mest användning och de mest använda tiderna på dagen. Du kan också identifiera avvikelser, till exempel vilka rum som känns kvava och varma, eller vilken del av huset som konsekvent skickar höga temperaturvärden som indikerar en bristfällig luftkonditionering.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Strömma data med Azure Event Hubs.
> * Analysera med Time Series Insights.

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du har [konfigurerat](tutorial-facilities-setup.md) och [etablerat](tutorial-facilities-udf.md) Azure Digital Twins-konfigurationen. Innan du fortsätter bör du kontrollera att du har:

- Ett [Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En instans av Digital Twins som körs.
- [Digital Twins C#-exemplen](https://github.com/Azure-Samples/digital-twins-samples-csharp) nedladdade och extraherade på din arbetsdator.
- [.NET Core SDK version 2.1.403 eller senare](https://www.microsoft.com/net/download) på utvecklingsdatorn för att köra exemplet. Kör `dotnet --version` för att kontrollera att rätt version är installerad.

> [!TIP]
> Använd ett unikt digitalt namn på en delad instans om du skapar en ny instans.

## <a name="stream-data-by-using-event-hubs"></a>Strömma data med Event Hubs

Med tjänsten [Event Hubs](../event-hubs/event-hubs-about.md) kan du skapa en pipeline för att strömma dina data. Det här avsnittet visar hur du skapar din händelsehubb som en anslutning mellan dina Azure Digital Twins- och Time Series Insights-instanser.

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Skapa en resurs** i fönstret till vänster.

1. Sök efter och välj **Event Hubs**. Välj **Skapa**.

    [![skapa ett Event Hubs-namnområde](./media/tutorial-facilities-analyze/create-event-hubs.png)](./media/tutorial-facilities-analyze/create-event-hubs.png#lightbox)

1. Ange ett **namn** för Event Hubs-namnområdet. Välj **Standard** som **Prisnivå**, din **prenumeration**, **resursgruppen** du använde för din Digital Twins-instans och **platsen**. Välj **Skapa**.

1. I Event Hubs namn områdes distribution väljer du **översikts** fönstret och väljer sedan **gå till resurs**.

    [![Event Hubs namn område efter distribution](./media/tutorial-facilities-analyze/open-event-hub-ns.png)](./media/tutorial-facilities-analyze/open-event-hub-ns.png#lightbox)

1. I **översiktsfönstret** för Event Hubs-namnområdet väljer du knappen **Event Hub** längst upp.
    [knapp för ![Händelsehubben](./media/tutorial-facilities-analyze/create-event-hub.png)](./media/tutorial-facilities-analyze/create-event-hub.png#lightbox)

1. Ange ett **namn** för din händelsehubb och välj sedan **Skapa**.

   När händelsehubben har distribuerats visas den i rutan **Event Hubs** i Event Hubs-namnområdet med en **aktiv** status. Välj den här händelsehubben för att öppna dess **översiktsfönster**.

1. Välj knappen **Konsumentgrupp** högst upp och ange ett namn som **tsievents** för konsumentgruppen. Välj **Skapa**.

    [![konsument grupp för Event Hub](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   När du har skapat konsumentgruppen visas den på listan längst ned på händelsehubbens **översiktsfönster**.

1. Öppna fönstret **Policyer för delad åtkomst** för din händelsehubb och välj knappen **Lägg till**. Ange principnamnet **ManageSend** och se till att alla kryssrutor är markerade och välj **Skapa**.

    [anslutnings strängar för ![Event Hub](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

    > [!TIP]
    > Kontrol lera att du skapar en SAS-princip för Event Hub-instansen i stället för ditt namn område.

1. Öppna **ManageSend** -principen som du skapade och Kopiera värdena för **anslutnings strängen – primär nyckel** och **anslutnings sträng – sekundär nyckel** till en temporär fil. Du behöver dessa värden för att skapa en slutpunkt för händelsehubben i nästa avsnitt.

### <a name="create-an-endpoint-for-the-event-hub"></a>Skapa en slutpunkt för händelsehubb

1. I kommandofönstret kontrollerar du att du är i mappen **occupancy-quickstart\src** för Azure Digital Twins-exemplet.

1. Öppna filen **actions\createEndpoints.yaml** i redigeringsprogrammet. Ersätt innehållet med följande:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Ersätt plats hållarna `Primary_connection_string_for_your_event_hub` med värdet för **anslutnings sträng – primär nyckel** för händelsehubben. Kontrollera att formatet för den här anslutningssträngen är följande:

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Ersätt plats hållarna `Secondary_connection_string_for_your_event_hub` med värdet för **anslutnings sträng – sekundär nyckel** för händelsehubben. Kontrollera att formatet för den här anslutningssträngen är följande: 

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Ersätt platshållarna `Name_of_your_Event_Hub` med namnet på din händelsehubb.

    > [!IMPORTANT]
    > Ange alla värden utan citattecken. Se till att det finns minst ett blanksteg efter kolonen i YAML-filen. Du kan även verifiera YAML-filinnehållet med hjälp av en YAML-onlineverifierare, till exempel [det här verktyget](https://onlineyamltools.com/validate-yaml).

1. Spara och stäng filen. Kör följande kommando i kommandofönstret och logga in med ditt Azure-konto när du tillfrågas.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Det skapar två slutpunkter för din händelsehubb.

   [![slut punkter för Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Analysera med Time Series Insights

1. Välj **Skapa en resurs** längst upp till vänster i [Azure-portalen](https://portal.azure.com). 

1. Sök efter och välj en resurs för **Time Series Insights** allmän tillgänglighet (ga). Välj **Skapa**.

1. Ange en **namn** på Time Series Insights-instansen och välj sedan din **prenumeration**. Välj **resursgruppen** du använde för din Digital Twins-instans och din **plats**. Välj **Nästa: knappen händelse källa** eller fliken **händelse källa** .

    [![val för att skapa en Time Series Insights instans](./media/tutorial-facilities-analyze/create-tsi.png)](./media/tutorial-facilities-analyze/create-tsi.png#lightbox)

1. På fliken **händelse källa** anger du ett **namn**, väljer **händelsehubben** som **typ av källa**och kontrollerar att de andra värdena är korrekt markerade för att referera till händelsehubben som du skapade. Välj **ManageSend** för **åtkomst princip namnet för händelsehubben**och välj sedan den konsument grupp som du skapade i föregående avsnitt för **konsument gruppen Event Hub**. Välj **Granska + skapa**.

    [![val för att skapa en händelse källa](./media/tutorial-facilities-analyze/tsi-event-source.png)](./media/tutorial-facilities-analyze/tsi-event-source.png#lightbox)

1. I fönstret **Granska + skapa** granskar du den information du har angett och väljer **skapa**.

1. I fönstret distribution väljer du den Time Series Insights resurs som du nyss skapade. Den öppnar fönstret **Översikt** för din Time Series Insightss miljö.

1. Välj knappen **gå till miljö** överst. Om du får en dataåtkomstvarning öppnar du fönstret **Dataåtkomstprinciper** för din Time Series Insights-instans, väljer **Lägg till**, väljer **Deltagare** som roll och väljer den aktuella användaren.

1. Med knappen för att **gå till miljön** öppnar du [Time Series Insights-utforskaren](../time-series-insights/time-series-insights-explorer.md). Om det inte visas några händelser kan du simulera enhetshändelser genom att bläddra till projektet **device-connectivity** för Digital Twins-exemplet och köra `dotnet run`.

1. När några simulerade händelser genereras går du tillbaka till Time Series Insights-utforskaren och väljer uppdateringsknappen högst upp. Du bör se analytiska diagram som skapas för dina simulerade sensordata. 

    [![diagram i Time Series Insights Explorer](./media/tutorial-facilities-analyze/tsi-explorer.png)](./media/tutorial-facilities-analyze/tsi-explorer.png#lightbox)

1. I Time Series Insights Explorer kan du sedan generera diagram och termiska kartor för olika händelser och data från dina rum, sensorer och andra resurser. På vänster sida använder du listrutorna **MÅTT** och **DELA MED** för att skapa egna visualiseringar. 

   Välj exempelvis **Händelser** som **MÅTT** och **DigitalTwins SensorHardwareId** att **DELA MED** för att generera en termisk karta för var och en av dina sensorer. Den termiska kartan blir något som liknar:

   [![termisk karta i Time Series Insights Explorer](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill utforska Azure Digital Twins vidare kan du ta bort resurser som du har skapat i den här självstudien:

1. På den vänstra menyn i [Azure-portalen](https://portal.azure.com) väljer du **Alla resurser**, väljer din Digital Twins-resursgrupp sedan **Ta bort**.

    > [!TIP]
    > Om det inträffade problem när du skulle ta bort Digital Twins-instansen finns det nu en tjänstuppdatering som åtgärdar det. Försök att ta bort instansen igen.

2. Ta bort exempelprogrammen på datorn om det behövs.

## <a name="next-steps"></a>Nästa steg

Gå till nästa artikel för att läsa mer om diagram för spatial intelligens och objektmodeller i Azure Digital Twins.

> [!div class="nextstepaction"]
> [Förstå grafen för objektmodell och rumslig intelligens i Digital Twins](concepts-objectmodel-spatialgraph.md)
