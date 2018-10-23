---
title: Analysera händelser från Azure Digital Twins-konfigurationen | Microsoft Docs
description: Lär dig mer om att visualisera och analysera händelser från dina Azure Digital Twins-utrymmen, med Azure Time Series Insights, med hjälp av stegen i den här självstudien.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1366cafe5d2c526e86905c108b9c7b865aac8f69
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323468"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-using-time-series-insights"></a>Självstudie: Visualisera och analysera händelser från dina Azure Digital Twins-utrymmen med hjälp av Time Series Insights

När du har distribuerat din instans av Azure Digital Twins, etablerat din utrymmen och implementerat en anpassad funktion för att övervaka specifika villkor kan du sedan visualisera händelser och data från din utrymmen för att leta efter trender och avvikelser. 

I [den första självstudien](tutorial-facilities-setup.md) har du konfigurerat ett rumsligt diagram över en imaginär byggnad, med ett rum som innehåller sensorer för rörelse, koldioxid och temperatur. I [den andra självstudien](tutorial-facilities-udf.md) har du etablerat ett diagram och en användardefinierad funktion. Funktionen övervakar sensorvärdena och utlöser aviseringar för rätt villkor, det vill säga att rummet är tomt och temperatur- och koldioxidnivåerna är normala. Den här kursen visar hur du kan integrera meddelanden och data från din Digital Twins-konfiguration med Azure Time Series Insights. Du kan sedan visualisera dina sensorvärden över tid och leta efter trender som vilket rum som används mest, vilken tid på dagen då det är mest folk och så vidare. Du kan också identifiera avvikelser, till exempel vilka rum som känns kvava och varma, eller vilken del av huset som konsekvent skickar höga temperaturvärden som indikerar en bristfällig luftkonditionering.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Strömma data med Event Hubs
> * Analysera med Time Series Insights

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien förutsätter att du har [konfigurerat](tutorial-facilities-setup.md) och [etablerat](tutorial-facilities-udf.md) Azure Digital Twins-konfigurationen. Innan du fortsätter bör du kontrollera att du har:
- Ett [Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En instans av Digital Twins som körs.
- [Digital Twins C#-exempel](https://github.com/Azure-Samples/digital-twins-samples-csharp) nedladdade och extraherade på din arbetsdator.
- [.NET Core SDK version 2.1.403 eller senare](https://www.microsoft.com/net/download) på utvecklingsdatorn för att köra exemplet. Kör `dotnet --version` för att kontrollera att rätt version är installerad. 


## <a name="stream-data-using-event-hubs"></a>Strömma data med Event Hubs
Tjänsten [Event Hubs](../event-hubs/event-hubs-about.md) gör att du kan skapa en pipeline för att strömma dina data. Det här avsnittet visar hur du skapar din händelsehubb som en anslutning mellan dina Digital Twins- och TSI-instanser.

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I den vänstra navigeringspanelen klickar du på **Skapa en resurs**. 

1. Sök efter och välj **Event Hubs**. Klicka på **Skapa**.

1. Ange ett **namn** för Event Hubs-namnområdet, välj *Standard* **Prisnivå**, din **prenumeration**, **resursgruppen** du använde för din Digital Twins-instans och **platsen**. Klicka på **Skapa**. 

1. När distributionen är klar går du till Event Hubs-namnområdet *distribution* och klickar på namnområdet under **RESURS**.

    ![Namnområde för händelsehubb](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. I **översiktsfönstret** för Event Hubs-namnområdet klickar du på knappen **Event Hub** längst upp. 
    ![Händelsehubb](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Ange ett **namn** för din händelsehubb och klicka sedan på **Skapa**. När distributionen är klar visas den i rutan **Event Hubs** i Event Hubs-namnområdet med en *aktiv* **STATUS**. Klicka på den här händelsehubben för att öppna dess **översiktsfönster**.

1. Klicka på knappen **Konsumentgrupp** högst upp och ange ett namn som *tsievents* för konsumentgruppen. Klicka på **Skapa**.
    ![Händelsehubbkonsumentgrupp](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   När du har skapat konsumentgruppen visas den på listan längst ned på händelsehubbens **översiktsfönster**. 

1. Öppna fönstret **Policyer för delad åtkomst** för din händelsehubb och klicka på knappen **Lägg till**. **Skapa** en princip med namnet *ManageSend*, och se till att alla kryssrutor är markerade. 

    ![Händelsehubbens anslutningssträngar](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Öppna principen *ManageSend* som du har skapat och kopiera värdena för **Anslutningssträng – primär nyckel** och **Anslutningssträng – sekundär nyckel** till en temporär fil. Du behöver dessa värden för att skapa en slutpunkt för händelsehubben i nästa avsnitt.

### <a name="create-endpoint-for-the-event-hub"></a>Skapa slutpunkt för händelsehubb

1. I kontrollera kontrollerar du att du är i mappen **_occupancy-quickstart\src** i Digital Twins-exemplet.

1. Öppna filen **_actions\createEndpoints.yaml_** i redigeringsprogrammet. Ersätt innehållet med följande:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. Ersätt platshållarna `Primary_connection_string_for_your_event_hub` med värdet för **Anslutningssträng – primär nyckel** för händelsehubben. Kontrollera att formatet för den här anslutningssträngen är följande:
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
```

1. Ersätt platshållarna `Secondary_connection_string_for_your_event_hub` med värdet för **Anslutningssträng – sekundär nyckel** för händelsehubben. Kontrollera att formatet för den här anslutningssträngen är följande: 
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
```

1. Ersätt platshållarna `Name_of_your_Event_Hubs_namespace` med namnet på ditt Event Hubs-namnområde.

    > [!IMPORTANT]
    > Ange alla värden utan citattecken. Kontrollera att det finns minst ett blanksteg efter kolonen i *YAML*-filen. Du kan också bekräfta *YAML*-filinnehållet med YAML-onlineverifierare som [det här verktyget](https://onlineyamltools.com/validate-yaml).


1. Spara och stäng filen. Kör följande kommando i kommandofönstret och logga in med ditt Azure-konto när du tillfrågas.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   Det skapar två slutpunkter för din händelsehubb.

   ![Slutpunkter för Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Analysera med Time Series Insights

1. I det vänstra navigeringsfönstret i [Azure-portalen](https://portal.azure.com) klickar du på **Skapa en resurs**. 

1. Sök efter och välj en ny **Time Series Insights**-resurs. Klicka på **Skapa**.

1. Ange en **namn** på Time Series Insights-instansen och välj sedan din **prenumeration**. Välj **resursgruppen** du använde för din Digital Twins-instans och din **plats**. Klicka på **Skapa**.

    ![Skapa TSI](./media/tutorial-facilities-analyze/create-tsi.png)

1. När den har distribuerats öppnar du Time Series Insights-miljön och öppnar fönstret **Händelsekällor**. Klicka på **Lägg till** längst upp för att lägga till en konsumentgrupp.

1. I fönstret **Ny händelsekälla** anger du ett **namn** och kontrollerar att de andra värdena är markerade på rätt sätt. Välj *ManageSend* som **Namn på händelsehubbsprincip** och välj sedan den *konsumentgrupp* du skapade i föregående avsnitt som **händelsehubbkonsumentgrupp**. Klicka på **Skapa**.

    ![TSI-händelsekälla](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Öppna **översiktsfönstret** för din Time Series Insights-miljö och klicka på knappen för att **gå till miljön** längst upp. Om du får en *dataåtkomstvarning* öppnar du fönstret **Dataåtkomstprinciper** för din TSI-instans, klickar på **Lägg till**, väljer **Deltagare** som roll och väljer den aktuella användaren.

1. Med knappen för att **gå till miljön** öppnar du [Time Series Insights-utforskaren](../time-series-insights/time-series-insights-explorer.md). Om det inte visas några händelser kan du simulera enhetshändelser genom att gå till projektet **_device-connectivity_** för Digital Twins-exemplet och köra `dotnet run`.

1. När några simulerade händelser genereras går du tillbaka till Time Series Insights-utforskaren och klickar på uppdateringsknappen högst upp. Du bör se analytiska diagram som skapas för dina simulerade sensordata. 

    ![TSI-utforskaren](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. I Time Series Explorer kan du sedan generera diagram och termiska kartor för olika händelser och data från dina rum, sensorer och andra resurser. Till vänster klickar du på listrutorna med namnet **MÅTT** och **DELA MED** för att skapa egna visualiseringar. Välj exempelvis *Händelser* som **MÅTT** och *DigitalTwins SensorHardwareId* att **DELA MED** för att generera en termisk karta för var och en av dina sensorer, ungefär som på följande bild:

    ![TSI-utforskaren](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill utforska Azure Digital Twins vidare kan du ta bort resurser som du har skapat i den här självstudien:

1. Klicka på **Alla resurser** på menyn till vänster i [Azure-portalen](http://portal.azure.com), välj din Digital Twins-resursgrupp och **ta bort** den.
2. Om du vill kan fortsätta att ta bort exempelprogrammen på arbetsdatorn också. 


## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel för att läsa mer om diagram för spatial intelligens och objektmodeller i Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Förstå grafen för objektmodell och rumslig intelligens i Digital Twins](concepts-objectmodel-spatialgraph.md)

