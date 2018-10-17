---
title: Skapa en Azure Time Series Insights-miljö
description: Lär dig hur du skapar en Time Series Insights-miljö, fylld med data från simulerade enheter.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/04/2018
ms.author: anshan
manager: cshankar
ms.openlocfilehash: 46fecf6f1b2826f0e23b7b6c4f4511ce9a662d1c
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365697"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Självstudier: Skapa en Azure Time Series Insights-miljö

Den här självstudien vägleder dig genom processen att skapa en Time Series Insight-miljö (TSI) med data från simulerade enheter. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en TSI-miljö 
> * Skapa en lösning för enhetssimulering som innehåller en IoT Hub
> * Anslut TSI-miljön till IoT Hub
> * Kör en enhetssimulering för att strömma data till TSI-miljön
> * Kontrollera simulerade telemetridata

## <a name="video"></a>Video: 

### <a name="in-this-video-we-show-you-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-that-can-be-used-to-get-started-with-time-series-insightsbr"></a>I den här videon visar vi hur du använder en Azure IoT Solution Accelerator för att skapa data som kan användas för att komma igång med Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 

Ditt Azure-inloggningskonto måste också vara medlem i prenumerationens ägarroll. Mer information finns i [Hantera åtkomst med hjälp av RBAC och Azure-portalen](/azure/role-based-access-control/role-assignments-portal)

## <a name="overview"></a>Översikt

TSI-miljön är var enhetsdata samlas in och lagras. Väl lagrade i TSI-miljön kan du använda [TSI-utforskaren](time-series-quickstart.md) och [TSI fråge-API](/rest/api/time-series-insights/time-series-insights-reference-queryapi) för att fråga och analysera dessa data.

Precis som alla simulerade eller fysiska enheter är IoT Hub den anslutningspunkt som används av enheter för att på ett säkert sätt ansluta och överföra data till Azure-molnet. Enligt beskrivningen i [TSI-översikt](time-series-insights-overview.md), fungerar IoT Hub också som en händelsekälla för att strömma data till TSI-miljön. 

Den här självstudien använder också en [IoT-lösningsaccelerator](/azure/iot-accelerators/), för att generera och strömma exempeltelemetridata till IoT Hub. IoT-lösningsacceleratorer ger förkonfigurerade lösningar i företagsklass som hjälper dig att påskynda utvecklingen av anpassade IoT-lösningar. 

## <a name="create-a-tsi-environment"></a>Skapa en TSI-miljö

Skapa först en TSI-miljö i din Azure-prenumeration:

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-prenumerationskonto.  
2. Välj **+ skapa en resurs** i det övre vänstra hörnet.  
3. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**.  
   
   [![Välj Time Series Insights-miljöresursen](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. På sidan **Time Series Insights-miljö** fyller du i de obligatoriska parametrarna:
   
   Parameter|Beskrivning
   ---|---
   **Miljönamn** | Välj ett unikt namn för TSI-miljön. Namnet används av TSI-utforskaren och fråge-API.
   **Prenumeration** | Prenumerationer är containrar för Azure-resurser. Välj den prenumeration där du vill skapa TSI-miljön.
   **Resursgrupp** | En resursgrupp är en container för Azure-resurser. Välj en befintlig resursgrupp eller skapa en ny för TSI-miljöresursen.
   **Plats** | Välj ett datacenterområde för TSI-miljön. För att undvika kostnader för tillagd bandbredd och fördröjning, är det bäst att hålla TSI-miljön i samma region som andra IoT-resurser.
   **Priser för SKU** | Välj det genomflöde som behövs. Välj S1 för lägst kostnad och startkapacitet.
   **Kapacitet** | Kapacitet är den multiplikator som appliceras på ingångshastigheten, lagringskapaciteten och de kostnader som är associerade med den valda SKU:n.  Du kan ändra kapacitet för en miljö när den har skapats. Välj en kapacitet på 1 för lägsta kostnad. 

   När du är klar klickar du på **Skapa** för att påbörja etableringen.

   ![Skapa en Time Series Insights-miljöresurs](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. Du kan kontrollera panelen **Meddelanden** för att övervaka distributionens slutförande som bör ta mindre än en minut:  

   ![Distributionen av Time Series Insights-miljön utförd](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

Skapa sedan enhetssimuleringslösningen som genererar testdata för att fylla i TSI-miljön:

1. I ett separat fönster/flik gå till https://www.azureiotsolutions.com, logga in med samma konto som Azure-prenumeration och välj acceleratorn för ”enhetssimulering”:

   ![Kör acceleratorn för enhetssimulering](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. Ange de obligatoriska parametrarna på sidan **Skapa enhetssimuleringslösning**:

   Parameter|Beskrivning
   ---|---
   **Namn på lösning** | Ett unikt värde som används för att skapa en ny resursgrupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen.
   **Prenumeration** | Ange samma prenumeration som användes för att skapa TSI-miljön i föregående avsnitt.
   **Region** | Ange samma region som användes för att skapa TSI-miljön i föregående avsnitt. 
   **Distribuera valfria Azure-resurser** | Lämna **IoT Hub** markerad, eftersom de simulerade enheterna kommer att använda den för att ansluta/streama data.

   När du är klar klickar du på **Skapa lösning** för att etablera lösningens Azure-resurser, vilket tar cirka 6–7 minuter att slutföra:

   ![Etablera enhetssimuleringslösningen](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. När etableringen har slutförts ändras texten ovanför den nya lösningen från ”Etablerar ...” till ”Klar”:

   >[!IMPORTANT]
   > Klicka inte på knappen **Starta** ännu! Men håll den här webbsidan öppen eftersom du kommer tillbaka till den senare.

   ![Etableringen för enhetssimuleringslösningen klar](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. Gå nu tillbaka till Azure Portal och inspektera de nyligen skapade resurserna i din prenumeration. På sidan **Resursgrupper** i portalen kommer du att se att en ny resursgrupp har skapats med det **Lösningsnamn** som angavs i det sista steget. Observera också alla resurser som skapats för att stödja enhetssimuleringslösningen:

   [![Resurser för enhetssimuleringslösning](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Anslut TSI-miljön till IoT Hub 

Nu har du lärt dig hur du skapar två uppsättningar med resurser, var och en i sin egen resursgrupp: 
- En tom TSI-miljö 
- Resurser för enhetssimuleringslösning, inklusive en IoT Hub som genererats av en lösningsaccelerator

Kom ihåg att simulerade enheter behöver ansluta till en IoT Hub för att strömma enhetsdata. För att flöda data in i TSI-miljön, måste du göra konfigurationsändringar i både din IoT Hub och TSI-miljö. 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Konfiguration av IoT Hub: definiera en konsumentgrupp

IoT Hub innehåller olika slutpunkter för att dela funktioner till andra aktörer. Slutpunkten ”Händelser” låter andra program använda data, då de strömmas till en IoT Hub-instans. Mer specifikt tillhandahåller ”konsumentgrupper” en mekanism för program för att lyssna och hämtar data från IoT Hub. 

Därefter kan du definiera en ny ”konsumentgrupp”-egenskap på enhetssimuleringslösningens IoT Hub:ens slutpunkt ”händelser”. 

1. I Azure Portal går du till sidan **Översikt** i resursgruppen du skapade för enhetssimuleringslösningen och väljer sedan IoT Hub-resursen:

   [![Resursgrupp för enhetssimuleringslösning](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Anteckna även **Namn** på IoT Hub-resursen som genererats för lösningen, eftersom du kommer att referera till den senare.

2. Bläddra nedåt och välj sidan **Slutpunkter** och välj sedan slutpunkten **Händelser**. På slutpunktens sida **Egenskaper**, anger du ett unikt namn för din slutpunkt under konsumentgruppen ”$Default” och klickar sedan på **Spara**:

   [![IoT Hub-slutpunkter för enhetssimuleringslösning](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>Konfiguration av TSI-miljö: definiera en händelsekälla

Anslut nu den nya IoT Hub:ens händelseslutpunkt ”konsumentgrupp” till TSI-miljön som en ”händelsekälla”.

1. Gå till sidan **Översikt** i resursgruppen du skapade för TSI-miljön och välj sedan TSI-miljön:

   [![Resursgrupp och miljö för TSI-miljö](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. På sidan TSI-miljö väljer du **Händelsekällor** och klickar på **+ Lägg till**:

   ![Översikt över TSI-miljön](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. Ange de obligatoriska parametrarna på sidan **Ny händelsekälla**: 

   Parameter|Beskrivning
   ---|---
   **Händelsekällans namn** | Kräver ett unikt värde som används för att namnge händelsekällan.
   **Källa** | Välj ”IoT Hub”.
   **Importalternativ** | Använd standardvärdet ”Använd IoT Hub från tillgängliga prenumerationer”. Det här alternativet kommer att leda till att nästa listruta fylls i med tillgängliga prenumerationer.
   **Prenumeration** | Välj samma prenumeration som du skapade TSI-miljön och enhetssimuleringens resurser i.
   **IoT Hub-namn** | Bör som standard vara namnet på den IoT Hub som du noterade tidigare. Om inte, välj rätt IoT Hub.
   **Principnamn för IoT Hub** | Lämna som standardvärdet ”iothubowner”.
   **IoT Hub-konsumentgrupp** | Bör som standard vara namnet på IoT Hub:ens konsumentgrupp som du skapade tidigare. Annars väljer du rätt konsumentgruppnamn. 
   **Händelseserialiseringsformat** | Lämna som standardvärdet ”JSON”.
   **Egenskapsnamn för tidsstämpel** | Ange som ”tidsstämpel”.

   När du är klar klickar du på **Skapa** för att lägga till händelsekällan. När du kommer tillbaka till resursgruppens sida **Översikt** ser du, tillsammans med din TSI-miljöresurs, en ny resurs ”Time Series Insights-händelsekälla”.

   ![Ny händelsekälla för TSI-miljön](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>Kör en enhetssimulering för att strömma data till TSI-miljön

Nu när allt konfigurationsarbete har slutförts, är det dags att fylla i TSI-miljön med exempeldata från de simulerade enheterna.

Från avsnittet [Skapa en enhetssimulering](#create-a-device-simulation) kan du återställa flera Azure-resurser som har skapats av acceleratorn som stöd för lösningen. Tillsammans med IoT Hub som beskrivits tidigare har ett webbprogram i Azure App Service genererats för att skapa och överföra simulerad enhetstelemetri.

1. Gå tillbaka till din [Instrumentpanel för lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard). Logga in igen om det behövs med hjälp av samma Azure-konto som du använde i den här självstudien. Nu kan du klicka på knappen **Starta** under lösningen ”enhetssimulering”:

     ![Instrumentpanel för lösningsacceleratorer](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. Enhetssimuleringens webbprogram startas nu och det kan ta några sekunder vid första inläsningen. Du uppmanas också om medgivande till att ge webbprogrammet behörighet att ”logga in och läsa din profil”. Det tillåter programmet att hämta användarens profilinformation vilken krävs för att stödja programmets funktion:

     ![Medgivande för enhetssimuleringens webbprogram](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. På sidan för **Simuleringsinställningar** anger du de obligatoriska parametrarna: 

   Parameter|Beskrivning
   ---|---
   **Mål-IoT Hub** | Välj ”Använd i förväg etablerad IoT Hub”.
   **Enhetsmodell** | Välj ”Kylaggregat”.
   **Antal enheter**  | Ange 1000 under **Belopp**.
   **Telemetrifrekvens** | Ange 10 sekunder.
   **Simuleringens varaktighet** | Välj **Sluta om:** och ange 5 minuter.

   När du är klar klickar du på **Starta simuleringen**. Simuleringen kommer att köra i totalt 5 minuter, vilket genererar data från 1000 simulerade enheter var 10:e sekund.  

   ![Inställning av enhetssimulering](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. När simuleringen körs kommer du att se fälten **Totalt antal meddelanden** och **Meddelanden per sekund** uppdateras ungefär var 10:e sekund. Simuleringen kommer att avslutas efter cirka 5 minuter och du återgår till **Simuleringsinställningar**.

   ![Enhetssimuleringen körs](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>Kontrollera telemetridata

I det här sista avsnittet verifierar du att telemetridata skapats och lagrats i TSI-miljön. För att verifiera data, kan du använda Time Series Insights-utforskaren som används för att fråga och analysera telemetridata.

1. Gå tillbaka till sidan **Översikt** för TSI-miljöns resursgrupp och välj sedan TSI-miljön igen:

   [![Resursgrupp och miljö för TSI-miljö](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. På sidan **Översikt** i TSI-miljön klickar du på **Time Series Insights-utforskarens URL** för att öppna TSI-utforskaren:

   [![TSI-utforskaren](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. TSI-utforskaren kommer att läsa in och autentisera med ditt Azure Portal-konto. Vid första vyn ser du i diagramområdet att TSI-miljön verkligen har fyllts i med simulerade telemetridata. Välj listrutan längst upp till vänster om du vill filtrera ett smalare tidsintervall. Ange ett tidsintervall som är tillräckligt stort för att sträcka sig över hela enhetssimuleringen. Klicka sedan på sök förstoringsklass:

   [![Tidsintervallfilter för TSI-utforskaren](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. Genom att begränsa tidsintervallet kan diagrammet zooma in till distinkta belastningar för dataöverföring, till IoT Hub och TSI-miljön. Observera också texten **Strömning klar** längst upp till höger som visar det totala antalet händelser som hittades. Du kan också dra skjutreglaget **Intervallstorlek** för att styra ritytans precision på diagrammet:

   [![Filtrerad vy över tidsintervallet för TSI-utforskaren](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. Slutligen kan du också vänsterklicka på en region för att filtrera ett område, sedan högerklicka och använda **Utforska händelser** för att visa händelseinformation i tabellvyn **Händelser**:

   [![Filtrerad vy och händelser över tidsintervallet för TSI-utforskaren](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudiekursen skapar flera körande Azure-tjänster, för att stödja TSI-miljön och enhetssimuleringslösningen. Om du vill avbryta och/eller fördröja slutförandet av den här serien av självstudiekurser rekommenderar vi att du tar bort alla resurser för att undvika onödiga kostnader. 

Klicka på den vänstra menyn i Azure Portal:

1. Klicka på ikonen **Resursgrupper** välj sedan den resursgrupp som du skapade för TSI-miljön. Överst på sidan, klickar du på **Ta bort resursgrupp**, anger namnet på resursgruppen och klickar sedan på **Ta bort**. 
2. Klicka på ikonen **Resursgrupper** välj sedan den resursgrupp som har skapats av enhetssimuleringens lösningsaccelerator. Överst på sidan, klickar du på **Ta bort resursgrupp**, anger namnet på resursgruppen och klickar sedan på **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en TSI-miljö 
> * Skapa en lösning för enhetssimulering som innehåller en IoT Hub
> * Anslut TSI-miljön till IoT Hub
> * Kör en enhetssimulering för att strömma data till TSI-miljön
> * Kontrollera simulerade telemetridata

Nu när du kan skapa dina egna TSI-miljöer kan du lära dig att bygga en webbapp som konsumerar data från en TSI-miljö:

> [!div class="nextstepaction"]
> [Skapa en enkelsidig Azure Time Series Insights-webbapp](tutorial-create-tsi-sample-spa.md)


