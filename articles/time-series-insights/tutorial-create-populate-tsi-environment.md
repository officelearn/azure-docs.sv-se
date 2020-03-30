---
title: 'Självstudiekurs: Skapa en miljö – Insikter i Azure Time Series | Microsoft-dokument'
description: Lär dig hur du skapar en Time Series Insights-miljö som fylls med data från simulerade enheter.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 7bebc9e682f5156fa235b77ff020e502695a28be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76981199"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Självstudier: Skapa en Azure Time Series Insights-miljö

Den här självstudien guidar dig genom processen att skapa en Azure Time Series Insights-miljö som fylls med data från simulerade enheter. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en time series insights-miljö.
> * Skapa en enhetssimuleringslösning som innehåller en IoT-hubb.
> * Anslut time series insights-miljön till IoT-hubben.
> * Kör en enhetssimulering för att strömma data till time series insights-miljön.
> * Verifiera de simulerade telemetridata.

> [!IMPORTANT]
> Registrera dig för en [kostnadsfri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Krav

* Ditt Azure-inloggningskonto måste också vara medlem i prenumerationens **ägarroll.** Mer information finns i [Hantera åtkomst med hjälp av rollbaserad åtkomstkontroll och Azure-portalen](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Granska video

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Lär dig hur du använder en Azure IoT-lösningsaccelerator för att generera data och komma igång med Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Översikt

Time Series Insights-miljön är där enhetsdata samlas in och lagras. När Azure [Time Series Insights Explorer](time-series-quickstart.md) och [Time Series Insights Query API](/rest/api/time-series-insights/ga-query-api) har lagrats kan du använda för att fråga och analysera data.

Azure IoT Hub är den händelsekälla som används av alla enheter (simulerade eller fysiska) i självstudien för att säkert ansluta och överföra data till ditt Azure-moln.

Den här självstudien använder också en [IoT-lösningsaccelerator](https://www.azureiotsolutions.com) för att generera och strömma exempeltelemetridata till IoT Hub.

>[!TIP]
> [IoT-lösningsacceleratorer](https://www.azureiotsolutions.com) tillhandahåller förkonfigurerade lösningar i företagsklass som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

Skapa först enhetssimuleringslösningen, som genererar testdata för att fylla i time series insights-miljön.

1. Gå till [azureiotsolutions.com](https://www.azureiotsolutions.com)i ett separat fönster eller en separat flik . Logga in med samma Azure-prenumerationskonto och välj **device simulation-accelerator.**

   [![Kör acceleratorn för enhetssimulering](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1.  Välj **Testa nu**. Ange sedan de parametrar som krävs på **sidan Skapa enhetssimuleringslösning.**

   Parameter|Beskrivning
   ---|---
   **Namn på distribution** | Det här unika värdet används för att skapa en ny resursgrupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen.
   **Azure-prenumeration** | Ange samma prenumeration som användes för att skapa time series insights-miljön i föregående avsnitt.
   **Distributionsalternativ** | Välj **Etablera ny IoT-hubb** om du vill skapa en ny IoT-hubb som är specifik för den här självstudien.
   **Azure-plats** | Ange samma region som användes för att skapa time series insights-miljön i föregående avsnitt.

   När du är klar väljer du **Skapa** för att etablera lösningens Azure-resurser. Det kan ta upp till 20 minuter att slutföra den här processen.

   [![Etablera enhetssimuleringslösningen](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. När etableringen är klar visas två uppdateringar som meddelar dig att distributionstillståndet har flyttats från **etablering** till **klar**. 

   >[!IMPORTANT]
   > Gå inte in i din lösningsaccelerator än! Håll den här webbsidan öppen eftersom du kommer tillbaka till den senare.

   [![Etableringen för enhetssimuleringslösningen klar](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Inspektera nu de nyskapade resurserna i Azure-portalen. På sidan **Resursgrupper** märker du att en ny resursgrupp skapades med hjälp av **lösningsnamnet** som angavs i det sista steget. Anteckna de resurser som har skapats för enhetssimuleringen.

   [![Resurser för enhetssimulering](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Skapa en miljö

För det andra skapar du en Time Series Insights-miljö i din Azure-prenumeration.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-prenumerationskonto. 
1. Välj **+ skapa en resurs** i det övre vänstra hörnet. 
1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**. 

   [![Välj miljöresursen Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. På **miljösidan Time Series Insights** fyller du i de parametrar som krävs.

   Parameter|Beskrivning
   ---|---
   **Miljönamn** | Välj ett unikt namn för time series insights-miljön. Namnen används av utforskaren Time Series Insights och [Fråge-API:erna](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Prenumeration** | Prenumerationer är containrar för Azure-resurser. Välj en prenumeration för att skapa time series insights-miljön.
   **Resursgrupp** | En resursgrupp är en container för Azure-resurser. Välj en befintlig resursgrupp eller skapa en ny för miljöresursen Time Series Insights.
   **Location** | Välj en datacenterregion för time series insights-miljön. Om du vill undvika ytterligare svarstid skapar du time series insights-miljön i samma region som andra IoT-resurser.
   **Nivå** | Välj det genomflöde som behövs. Välj **S1**.
   **Kapacitet** | Capacity is the multiplier applied to the ingress rate and storage capacity associated with the selected SKU. Du kan ändra kapaciteten när du har skapat den. Välj en kapacitet **på 1**.

   När du är klar väljer du **Nästa: Händelsekälla** för att gå vidare till nästa steg.

   [![Skapa en Time Series Insights-miljöresurs](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Anslut nu time series insights-miljön till IoT-hubben som skapats av Solution Accelerator. Ange Välj en `Select existing` **hubb** till . Välj sedan den IoT-hubb som skapas av Solution Accelerator när du ställer in **IoT Hub-namn**.

   [![Ansluta time series insights-miljön till den skapade IoT-hubben](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Välj slutligen **Granska + skapa**.

1. Kontrollera **meddelandepanelen** för att övervaka slutförande av distributionen. 

   [![Distributionen av Time Series Insights-miljön utförd](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Kör enhetssimulering

Nu när distributionen och den första konfigurationen är klar fyller du i time series insights-miljön med exempeldata från [simulerade enheter som skapats av acceleratorn](#create-a-device-simulation).

Tillsammans med IoT-hubben genererades ett Azure App Service-webbprogram för att skapa och överföra simulerad enhetstelemetri.

1. Gå tillbaka till din [Instrumentpanel för lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard). Logga in igen, om det behövs, med samma Azure-konto som du har använt i den här självstudien. Välj din "Enhetslösning" och gå sedan **till din lösningsaccelerator** för att starta den distribuerade lösningen.

   [![Instrumentpanel för lösningsacceleratorer](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Webbappen för enhetssimulering börjar med att du uppmanas att ge webbprogrammet **inloggningen och läsa din** profilbehörighet. Med den här behörigheten kan programmet hämta den användarprofilinformation som krävs för att stödja programmets funktion.

   [![Medgivande för enhetssimuleringens webbprogram](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Välj **+ Ny simulering**. När sidan **Simuleringsinställning** har läses in anger du de parametrar som krävs.

   Parameter|Beskrivning
   ---|---
   **Mål-IoT Hub** | Välj **Använd företablerade IoT Hub**.
   **Enhetsmodell** | Välj **Kylaggregat**.
   **Antal enheter**  | Ange `10` under **Belopp**.
   **Telemetrifrekvens** | Ange `10` sekunder.
   **Simuleringens varaktighet** | Välj **Avsluta i:** och ange `5` minuter.

   När du är klar väljer du **Starta simulering**. Simuleringen pågår i totalt 5 minuter. Den genererar data från 1 000 simulerade enheter var tionde sekund. 

   [![Inställning av enhetssimulering](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Medan simuleringen körs, märker du att **fälten Totalt antal meddelanden** och **meddelanden per sekund uppdateras,** ungefär var tionde sekund. Simuleringen avslutas efter cirka 5 minuter och återgår till **Simuleringsinställningen**.

   [![Enhetssimuleringen körs](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Kontrollera telemetridata

I det här sista avsnittet kontrollerar du att telemetridata har genererats och lagrats i time series insights-miljön. För att verifiera data, kan du använda Time Series Insights-utforskaren som används för att fråga och analysera telemetridata.

1. Gå tillbaka till time series insights-miljöns resursgrupp **Översiktssida.** Välj time series insights-miljön.

   [![Tidsserie insikter miljö resursgrupp och miljö](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. På sidan **Översikt över** Time Series Insights väljer du time **series insights explorer-URL:en** för att öppna utforskaren Time Series Insights.

   [![Time Series Insights-utforskaren](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Tidsseriestatistikutforskaren läser in och autentiserar med hjälp av ditt Azure-portalkonto. Inledningsvis visas diagramområdet som time series insights-miljön har fyllts med tillsammans med dess simulerade telemetridata. Om du vill filtrera ett smalare tidsintervall markerar du listrutan i det övre vänstra hörnet. Ange ett tidsintervall som är tillräckligt stort för att kunna variera varaktigheten av enhetssimuleringen. Välj sedan sökglaset.

   [![Tidsseriestatistik explorer tidsintervall filter](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Om du begränsar tidsintervallet kan diagrammet zooma in till de distinkta skurarna av dataöverföring till IoT-hubben och time series insights-miljön. Lägg också märke till den fullständiga texten **för direktuppspelning** i det övre högra hörnet, som visar det totala antalet händelser som hittats. Du kan också dra skjutreglaget **Intervallstorlek** för att styra ritgranulariteten i diagrammet.

   [![Tidsseriestatistik explorer tidsintervall filtrerad vy](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Slutligen kan du också vänsterklicka på en region för att filtrera ett område. Högerklicka och använd **Utforska händelser** för att visa händelseinformation i **tabellvyn Händelser.**

   [![Tidsseriestatistik explorer tidsintervall filtrerad vy och händelser](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien skapar flera Azure-tjänster som körs för att stödja time series insights-miljön och enhetssimuleringslösningen. Om du vill ta bort dem navigerar du tillbaka till Azure-portalen.

Från menyn till vänster i Azure-portalen:

1. Välj ikonen **Resursgrupper.** Välj sedan den resursgrupp som du skapade för time series insights-miljön. Högst upp på sidan väljer du **Ta bort resursgrupp,** anger namnet på resursgruppen och väljer **Ta bort**.

1. Välj ikonen **Resursgrupper.** Välj sedan den resursgrupp som skapades av enhetssimuleringslösningsacceleratorn. Högst upp på sidan väljer du **Ta bort resursgrupp,** anger namnet på resursgruppen och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en time series insights-miljö.
> * Skapa en enhetssimuleringslösning som innehåller en IoT-hubb.
> * Anslut time series insights-miljön till IoT-hubben.
> * Kör en enhetssimulering för att strömma data till time series insights-miljön.
> * Verifiera de simulerade telemetridata.

Nu när du vet hur du skapar din egen Time Series Insights-miljö kan du lära dig hur du skapar ett webbprogram som använder data från en Time Series Insights-miljö:

> [!div class="nextstepaction"]
> [Läsa värdbaserade exempel på SDK-visualisering av klienter](https://tsiclientsample.azurewebsites.net/)
