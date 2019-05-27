---
title: 'Självstudier: Skapa en Azure Time Series Insights-miljö | Microsoft Docs'
description: Lär dig hur du skapar en Time Series Insights-miljö som är ifyllda med data från simulerade enheter.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: b8b46db043113f29f559ad44855d19f0d6ca73c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244163"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Självstudier: Skapa en Azure Time Series Insights-miljö

Den här självstudien vägleder dig genom processen att skapa en Azure Time Series Insights-miljö som är ifyllda med data från simulerade enheter. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Time Series Insights-miljö.
> * Skapa en lösning för simulering som innehåller en IoT-hubb.
> * Anslut Time Series Insights-miljö till IoT hub.
> * Kör en enhetssimulering att sända data till Time Series Insights-miljö.
> * Kontrollera simulerad telemetri-data.

## <a name="video"></a>Video

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Lär dig hur du använder en Azure IoT-lösningsaccelerator att generera data och kom igång med Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Din Azure inloggningskonto måste också vara medlem i prenumerationens **ägare** roll. Mer information finns i [hantera åtkomst med hjälp av rollbaserad åtkomstkontroll och Azure-portalen](/azure/role-based-access-control/role-assignments-portal).

## <a name="overview"></a>Översikt

Time Series Insights-miljö är där enhetsdata som samlas in och lagras. När data lagras i [Azure Time Series Insights explorer](time-series-quickstart.md) och [Time Series Insights fråge-API](/rest/api/time-series-insights/ga-query-api) kan användas för att fråga efter och analysera data. Azure IoT Hub är den kopplingspunkt som används av alla enheter (simulerade eller fysiska) på ett säkert sätt ansluta och överföra data till Azure-molnet. Den [översikt över Time Series Insights](time-series-insights-overview.md) lägger märke till att Azure IoT Hub även fungerar som en händelsekälla för att strömma data till en Time Series Insights-miljö. Den här självstudien används en [IoT lösningsaccelerator](/azure/iot-accelerators/) att generera och strömma exempeltelemetridata som IoT Hub.

>[!TIP]
> IoT-Lösningsacceleratorer tillhandahåller företagsklass förkonfigurerade lösningar som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

## <a name="create-an-environment"></a>Skapa en miljö

Skapa först en Time Series Insights-miljö i Azure-prenumerationen.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ditt Azure-prenumeration. 
1. Välj **+ skapa en resurs** i det övre vänstra hörnet. 
1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**. 

   [![Välj Time Series Insights-miljöresursen](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. På den **Time Series Insights-miljö** fyller du i de obligatoriska parametrarna.

   Parameter|Beskrivning
   ---|---
   **Miljönamn** | Välj ett unikt namn för Time Series Insights-miljö. Namn som används av Time Series Insights explorer och API: er för frågan.
   **Prenumeration** | Prenumerationer är containrar för Azure-resurser. Välj en prenumeration för att skapa Time Series Insights-miljö.
   **Resursgrupp** | En resursgrupp är en container för Azure-resurser. Välj en befintlig resursgrupp eller skapa ett nytt lösenord för resursen för Time Series Insights-miljö.
   **Plats** | Välj ett dataområde center för din Time Series Insights-miljö. För att undvika kostnader för extra bandbredd och latens kan behålla du Time Series Insights-miljö i samma region som andra IoT-resurser.
   **Priser för SKU** | Välj det genomflöde som behövs. Lägsta kostnad och starter kapacitet, väljer `S1`.
   **Kapacitet** | Kapacitet är den multiplikator som appliceras på ingångshastigheten, lagringskapaciteten och de kostnader som är associerade med den valda SKU:n. Du kan ändra kapaciteten när du har skapat. Välj en kapacitet på 1 för lägsta kostnad.

   När du är klar väljer **skapa** att påbörja etableringen.

   [![Skapa en resurs för Time Series Insights-miljö](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Kontrollera den **meddelanden** panelen för att övervaka distributionen slutförande. 

   [![Time Series Insights-miljö distributionen lyckades](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

Skapa sedan enheten simuleringslösning, vilket genererar testdata för att fylla i din Time Series Insights-miljö.

1. I ett separat fönster eller flik går du till [azureiotsolutions.com](https://www.azureiotsolutions.com). Logga in med samma konto för Azure-prenumeration och välj den **Enhetssimulering** accelerator.

   [![Kör Enhetssimulering accelerator](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Ange de obligatoriska parametrarna på den **skapa Enhetssimulering lösning** sidan.

   Parameter|Beskrivning
   ---|---
   **Namn på lösning** | Det här unika värdet används för att skapa en ny resursgrupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen.
   **Prenumeration** | Ange samma prenumeration som användes för att skapa en Time Series Insights-miljö i föregående avsnitt.
   **Region** | Ange samma region som används för att skapa en Time Series Insights-miljö i föregående avsnitt.
   **Distribuera valfria Azure-resurser** | Lämna **IoT Hub** kontrolleras. De simulerade enheterna använda den för att ansluta eller strömma data.

   När du är klar väljer du **skapa lösning** att etablera lösningen Azure-resurser. Det kan ta 6 – 7 minuter att slutföra den här processen.

   [![Etablera enheten simulering lösningen](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. När etableringen har slutförts ändras texten ovanför den nya lösningen från **etablering** till **redo**.

   >[!IMPORTANT]
   > Markera inte **starta** ännu! Behåll den här sidan öppen eftersom du kommer tillbaka till den senare.

   [![Lösning för enheter simulering etablering klar](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Nu går du tillbaka till Azure-portalen och granska de nyligen skapade resurserna i din prenumeration. I portalen **resursgrupper** Observera att en ny resursgrupp har skapats med hjälp av den **lösningsnamn** i det sista steget. Observera även alla resurser som har skapats för att stödja simulering-lösning för enheter.

   [![Resurser för enhetssimuleringslösning](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>Ansluta miljön till IoT hub

Nu har du lärt dig hur du skapar två uppsättningar med resurser, var och en i sin egen resursgrupp:

- En tom Time Series Insights-miljö.
- Simulering lösning enhetsresurser, som innehåller en IoT-hubb som genererats av en lösningsaccelerator.

Kom ihåg att simulerade enheter behöver ansluta till en IoT Hub för att strömma enhetsdata. För att flöda data i Time Series Insights-miljön, måste du göra konfigurationsändringar i både din IoT-hubb och Time Series Insights-miljö.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>IoT hub-konfiguration: Definiera en konsumentgrupp

IoT Hub innehåller olika slutpunkter för att dela funktioner för att andra aktörer. Slutpunkten ”händelser” gör det möjligt för andra program kan använda data som strömmas till en IoT hub-instans. Mer specifikt tillhandahåller ”konsumentgrupper” en mekanism för program för att lyssna på och hämta data från IoT hub.

Nu ska du definiera en ny **konsumentgrupp** egenskapen på enheten simulering lösningens IoT-hubb **slutpunkten händelser**.

1. I Azure-portalen går du till den **översikt** sidan i resursgruppen du skapade för simulering-lösning för enheter. Välj den IoT hub-resursen.

   [![Resursgrupp för enhetssimuleringslösning](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Anteckna den **namn** av IoT Hub-resurs som genererats för lösningen. Du refererar till den senare.

1. Bläddra nedåt och välj den **slutpunkter** och välj sedan den **händelser** slutpunkt. På slutpunkten för **egenskaper** anger du ett unikt namn för din slutpunkt under konsumentgrupp ”$Default”. Välj **Spara**.

   [![IoT Hub-slutpunkter för enhetssimuleringslösning](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>Miljökonfiguration: definiera en händelsekälla

Nu kan ansluta den nya IoT-hubben **konsumentgrupp** händelse slutpunkten i Time Series Insights-miljö som en **händelsekälla**.

1. Gå till den **översikt** sidan i resursgruppen du skapade för Time Series Insights-miljö. Välj Time Series Insights-miljön.

   [![Resursgrupp för Time Series Insights-miljö och miljö](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. På sidan Time Series Insights-miljö väljer **händelsekällor**. Välj sedan **+ Lägg till**.

   [![Översikt över Time Series Insights-miljö](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Ange de obligatoriska parametrarna på den **ny händelsekälla** sidan.

   Parameter|Beskrivning
   ---|---
   **Händelsekällans namn** | Kräver ett unikt värde som används för att namnge händelsekällan.
   **Källa** | Välj **IoT-hubb**.
   **Importalternativ** | Välj standard `Use IoT hub from available subscriptions`. Det här alternativet gör att nästa listrutan fyllas i med de tillgängliga prenumerationerna.
   **Prenumeration** | Välj samma prenumeration där du skapade Time Series Insights-miljön och Enhetssimulering resurser.
   **IoT Hub-namn** | Bör som standard vara namnet på den IoT Hub som du noterade tidigare. Om inte, välj rätt IoT Hub.
   **Principnamn för IoT Hub** | Välj **iothubowner**.
   **IoT Hub-konsumentgrupp** | Bör som standard vara namnet på IoT Hub:ens konsumentgrupp som du skapade tidigare. Annars väljer du rätt konsumentgruppnamn.
   **Händelseserialiseringsformat** | Lämna som återbetalats värde för `JSON`.
   **Egenskapsnamn för tidsstämpel** | Ange som `timestamp`.

   När du är klar väljer du **skapa** att lägga till händelsekällan. När du återgår till resursgruppen **översikt** sidan, tillsammans med din miljö Time Series Insights-resurs som du ser en ny ”Time Series Insights-händelsekälla”-resurs.

   [![Time Series Insights-miljö ny händelsekälla](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Kör enhetssimulering att sända data

Nu när allt konfigurationsarbete som är klar är det dags att fylla i Time Series Insights-miljö med exempeldata från simulerade enheter.

Du kanske kommer ihåg från den [skapa en enhet simulering del](#create-a-device-simulation), flera Azure-resurser har skapats av accelerator för lösningen. Tillsammans med IoT Hub som beskrivits tidigare har ett webbprogram i Azure App Service genererats för att skapa och överföra simulerad enhetstelemetri.

1. Gå tillbaka till din [Instrumentpanel för lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard). Logga in igen, om det behövs med hjälp av samma Azure-konto du använde i den här självstudien. Nu kan du välja **starta** under ”Enhetssimulering”-lösning.

     [![Instrumentpanelen för Lösningsacceleratorer](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Enheten simulering web app startar enligt den här punkten, och det kan ta flera sekunder vid inledande inläsningen. Du uppmanas också medgivande till att ge webbprogrammet ”logga in och läsa din profil” behörighet. Den här behörigheten kan programmet hämta användarprofilinformation krävs för att stödja driften av programmet.

     [![Enheten simulering web application medgivande](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Efter den **inställningar** sidan belastning, anger du de obligatoriska parametrarna.

   Parameter|Beskrivning
   ---|---
   **Mål-IoT Hub** | Välj **Använd företablerade IoT Hub**.
   **Enhetsmodell** | Välj **Kylaggregat**.
   **Antal enheter**  | Ange `1000` under **belopp**.
   **Telemetrifrekvens** | Ange `10` sekunder.
   **Simuleringens varaktighet** | Välj **filändelsen:** och ange `5` minuter.

   När du är klar väljer du **starta simulering**. Simuleringen körs i totalt 5 minuter. Den genererar data från 1 000 simulerade enheter var tionde sekund. 

   [![Enhetsinställningar för simulering](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. När simuleringen körs, Observera att den **Totalt antal meddelanden** och **meddelanden per sekund** fält uppdatera cirka var tionde sekund. Simuleringen avslutas efter cirka 5 minuter och tillbaka till **inställningar**.

   [![Enhetssimulering körs](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Kontrollera telemetridata

I det här sista delen kan kontrollera du att dessa data har genererats och lagras i Time Series Insights-miljö. För att verifiera data, kan du använda Time Series Insights-utforskaren som används för att fråga och analysera telemetridata.

1. Gå tillbaka till Time Series Insights-miljön resursgrupp **översikt** sidan. Välj Time Series Insights-miljön.

   [![Resursgrupp för Time Series Insights-miljö och miljö](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. På Time Series Insights-miljön **översikt** väljer den **Webbadressen för Time Series Insights explorer** att öppna Time Series Insights explorer.

   [![Time Series Insights-Utforskaren](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Time Series Insights explorer läser in och autentiserar med hjälp av Azure portal-kontot. Vid inledande vy kan visas i diagramområdet att Time Series Insights-miljö har fyllts med data för simulerad telemetri. Välj i listrutan i det övre vänstra hörnet för att filtrera ett snävare tidsintervall. Ange ett tidsintervall som är tillräckligt stor för att sträcka sig över hela enheten simuleringen. Välj sedan förstoringsglaset sökning.

   [![Time Series Insights explorer intervallet tidsfiltret](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Begränsa tidsintervallet kan diagrammet för att zooma in på distinkta topparna data som överförs till IoT-hubben och Time Series Insights-miljö. Lägg också märke till de **strömning slutförd** texten i det övre högra hörnet, som visar det totala antalet händelser hittades. Du kan också dra den **intervallstorlek** skjutreglaget för att styra ritytans precisionen i diagrammet.

   [![Tidsintervallet för Time Series Insights explorer filtrerad vy](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Slutligen kan du även klickar en region för att filtrera ett intervall. Högerklicka och använda **utforska händelser** att visa händelseinformation i den tabular **händelser** vy.

   [![Tidsintervallet för Time Series Insights explorer filtrerad vy och händelser](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudiekursen skapar flera aktiva Azure-tjänster för att stödja lösningen för Time Series Insights-miljö och enheten simulering. Om du vill lämna eller skjuta upp ditt arbete på den här självstudien tar du bort alla resurser för att undvika onödiga kostnader.

På menyn till vänster i Azure portal:

1. Välj den **resursgrupper** ikon. Välj sedan den resursgrupp du skapade för Time Series Insights-miljö. Längst ned på sidan Välj **ta bort resursgrupp**, ange namnet på resursgruppen och välj **ta bort**.

1. Välj den **resursgrupper** ikon. Välj sedan den resursgrupp som har skapats av lösningsaccelerator för enheten simulering. Längst ned på sidan Välj **ta bort resursgrupp**, ange namnet på resursgruppen och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en Time Series Insights-miljö.
> * Skapa en lösning för simulering som innehåller en IoT-hubb.
> * Anslut Time Series Insights-miljö till IoT hub.
> * Kör en enhetssimulering att sända data till Time Series Insights-miljö.
> * Kontrollera simulerad telemetri-data.

Nu när du vet hur du skapar en egen Time Series Insights-miljö, lär du dig hur du skapar ett webbprogram som använder data från en Time Series Insights-miljö:

> [!div class="nextstepaction"]
> [Skapa en enkelsidig Azure Time Series Insights-webbapp](tutorial-create-tsi-sample-spa.md)
