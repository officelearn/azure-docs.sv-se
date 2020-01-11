---
title: 'Självstudie: skapa en miljö – Azure Time Series Insights | Microsoft Docs'
description: Lär dig hur du skapar en Time Series Insights miljö som är ifylld med data från simulerade enheter.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 12/04/2019
ms.custom: seodec18
ms.openlocfilehash: 83462d47af49a02817dcfa05afcd8fdb5e0beab1
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863714"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Självstudier: Skapa en Azure Time Series Insights-miljö

Den här självstudien vägleder dig genom processen att skapa en Azure Time Series Insights-miljö som är ifylld med data från simulerade enheter. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en Time Series Insights-miljö.
> * Skapa en lösning för enhets simulering som innehåller en IoT-hubb.
> * Anslut Time Series Insightss miljön till IoT Hub.
> * Kör en enhets simulering för att strömma data till Time Series Insightss miljön.
> * Verifiera simulerade telemetridata.

> [!IMPORTANT]
> Registrera dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Krav

* Ditt Azure-inloggningsnamn måste också vara medlem i prenumerationens **ägar** roll. Mer information finns i [Hantera åtkomst med hjälp av rollbaserad åtkomst kontroll och Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Granska video

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Lär dig hur du använder en Azure IoT Solution Accelerator för att generera data och komma igång med Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Översikt

Time Series Insightss miljön är den plats där enhets data samlas in och lagras. När du har lagrat kan du använda [API: et](/rest/api/time-series-insights/ga-query-api) för [Azure Time Series Insights Explorer](time-series-quickstart.md) och Time Series Insights frågor för att fråga och analysera data.

Azure IoT Hub är händelse källan som används av alla enheter (simulerade eller fysiska) i självstudien för att på ett säkert sätt ansluta och skicka data till Azure-molnet.

Den här självstudien använder också en [IoT Solution Accelerator](https://www.azureiotsolutions.com) för att generera och strömma exempel på telemetridata till IoT Hub.

>[!TIP]
> [IoT Solution Accelerators](https://www.azureiotsolutions.com) tillhandahåller förkonfigurerade lösningar i företags klass som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

Först skapar du lösningen för enhets simulering, som genererar test data för att fylla i din Time Series Insightss miljö.

1. I ett separat fönster eller flik går du till [azureiotsolutions.com](https://www.azureiotsolutions.com). Logga in med samma Azure-prenumerations konto och välj **enhets simulerings** Accelerator.

   [![köra acceleratorn för enhets simulering](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1.  Välj **Testa nu**. Ange sedan de nödvändiga parametrarna på sidan **skapa lösning för enhets simulering** .

   Parameter|Beskrivning
   ---|---
   **Distributions namn** | Det unika värdet används för att skapa en ny resurs grupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen.
   **Azure-prenumeration** | Ange samma prenumeration som användes för att skapa din Time Series Insights-miljö i föregående avsnitt.
   **Distributions alternativ** | Välj **etablera nya IoT Hub** för att skapa en ny IoT-hubb som är speciell för den här självstudien.
   **Azure-plats** | Ange samma region som användes för att skapa din Time Series Insights-miljö i föregående avsnitt.

   När du är klar väljer du **skapa** för att etablera lösningens Azure-resurser. Det kan ta upp till 20 minuter att slutföra den här processen.

   [![etablera lösningen för enhets simulering](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. När etableringen har slutförts visas två uppdateringar som meddelar dig att distributions statusen har flyttats från **etablering** till **klar**. 

   >[!IMPORTANT]
   > Ange inte Solution Accelerator ännu! Behåll den här webb sidan öppen eftersom du kommer tillbaka till den senare.

   [lösnings etableringen för ![enhets simulering slutförd](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Granska nu de nyligen skapade resurserna i Azure Portal. På sidan **resurs grupper** ser du att en ny resurs grupp har skapats med hjälp av **lösnings namnet** som angavs i det sista steget. Anteckna de resurser som har skapats för enhets simuleringen.

   [resurser för ![enhets simulering](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Skapa en miljö

Skapa sedan en Time Series Insights-miljö i din Azure-prenumeration.

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-prenumerations konto. 
1. Välj **+ skapa en resurs** i det övre vänstra hörnet. 
1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**. 

   [![Välj Time Series Insights-miljöresursen](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Fyll i de obligatoriska parametrarna på sidan **Time Series Insights miljö** .

   Parameter|Beskrivning
   ---|---
   **Miljönamn** | Välj ett unikt namn för Time Series Insightss miljön. Namnen används av Time Series Insights Explorer och API: er för [frågor](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Prenumeration** | Prenumerationer är containrar för Azure-resurser. Välj en prenumeration för att skapa Time Series Insightss miljön.
   **Resursgrupp** | En resursgrupp är en container för Azure-resurser. Välj en befintlig resurs grupp eller skapa en ny för resursen Time Series Insightss miljö.
   **Plats** | Välj en data Center region för din Time Series Insightss miljö. Om du vill undvika ytterligare svars tid skapar du Time Series Insightss miljön i samma region som andra IoT-resurser.
   **Nivå** | Välj det genomflöde som behövs. Välj **S1**.
   **Kapacitet** | Kapaciteten är multiplikatorn som tillämpas på den ingångs takt och lagrings kapacitet som är kopplad till den valda SKU: n. Du kan ändra kapaciteten när du har skapat den. Välj en kapacitet på **1**.

   När du är färdig väljer du **Nästa: händelse källa** för att gå vidare till nästa steg.

   [![skapa en Time Series Insights miljö resurs](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Nu ansluter du Time Series Insightss miljön till IoT Hub som skapats av Solution Accelerator. Ange **Välj en hubb** att `Select existing`. Välj sedan den IoT-hubb som skapats av Solution Accelerator när du anger **IoT Hub namn**.

   [![ansluta Time Series Insightss miljön till den skapade IoT-hubben](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Slutligen väljer du **Granska + skapa**.

1. Gå till panelen **meddelanden** för att övervaka slut för ande av distribution. 

   [distribution av ![Time Series Insights miljön lyckades](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Kör enhets simulering

Nu när distributionen och den inledande konfigurationen är slutförd, fyller du i Time Series Insights-miljön med exempel data från [simulerade enheter som skapats av acceleratorn](#create-a-device-simulation).

Tillsammans med IoT Hub genererades ett Azure App Service-webbprogram för att skapa och överföra simulerad enhets telemetri.

1. Gå tillbaka till din [Instrumentpanel för lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard). Logga in igen, om det behövs, med samma Azure-konto som du har använt i den här självstudien. Välj din "enhets lösning" och **gå sedan till Solution Accelerator** för att starta din distribuerade lösning.

   [instrument panel för ![Solution Accelerators](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Webb programmet för enhets simulering börjar genom att du tillfrågas om du vill ge webb programmet det **loggade du in och läsa din profil** behörighet. Med den här behörigheten kan programmet hämta den användar profil information som krävs för att stödja programmets funktion.

   [tillstånd för ![enhets simulerings webb program](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Välj **+ ny simulering**. Ange de parametrar som krävs efter att installations sidan för **simulering** har lästs in.

   Parameter|Beskrivning
   ---|---
   **Mål-IoT Hub** | Välj **Använd företablerade IoT Hub**.
   **Enhetsmodell** | Välj **Kylaggregat**.
   **Antal enheter**  | Ange `10` under **belopp**.
   **Telemetrifrekvens** | Ange `10` sekunder.
   **Simuleringens varaktighet** | Välj **sluta i:** och ange `5` minuter.

   När du är klar väljer du **Starta simulering**. Simuleringen körs i totalt 5 minuter. Den genererar data från 1 000 simulerade enheter var 10: e sekund. 

   [installation av ![enhets simulering](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. När simuleringen körs ser du att fälten **Totalt antal meddelanden** och **meddelanden per sekund** uppdateras, ungefär var 10: e sekund. Simuleringen slutar efter cirka 5 minuter och återgår till **simulerings installationen**.

   [![enhets simulering körs](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Kontrollera telemetridata

I det här sista avsnittet verifierar du att telemetridata har genererats och lagrats i Time Series Insightss miljön. För att verifiera data, kan du använda Time Series Insights-utforskaren som används för att fråga och analysera telemetridata.

1. Gå tillbaka till **översikts** sidan för Time Series Insightss miljöns resurs grupp. Välj Time Series Insightss miljö.

   [resurs grupp och miljö för ![Time Series Insights miljö](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. På sidan **Översikt över** Time Series Insightss miljö väljer du **URL: en för Time Series Insights Explorer** för att öppna Time Series Insights Explorer.

   [![Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Time Series Insights Explorer läser in och autentiserar med hjälp av ditt Azure Portal-konto. I början visas det diagram fält som Time Series Insightss miljön fylldes med tillsammans med dess simulerade telemetridata. Om du vill filtrera en snävare tid väljer du List rutan i det övre vänstra hörnet. Ange ett tidsintervall som är tillräckligt stort för att omfatta varaktigheten för enhets simuleringen. Välj sedan förstorings glaset för sökning.

   [tids intervalls filter för ![Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Genom att begränsa tidsintervallet kan diagrammet zooma in i de distinkta burst-data överföringarna till IoT Hub och Time Series Insights miljön. Observera också att den **strömmande texten är klar** i det övre högra hörnet, vilket visar det totala antalet händelser som har hittats. Du kan också dra skjutreglaget för **intervall storlek** för att kontrol lera diagrammets rityta.

   [filtrera vyn tidsintervallet i ![Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Slutligen kan du också vänsterklicka på en region för att filtrera ett intervall. Högerklicka sedan och Använd **utforska händelser** för att visa händelse information i vyn tabell **händelser** .

   [![Time Series Insights Explorer-tidsintervallet filtrerad vy och händelser](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien skapar flera Azure-tjänster som körs för att stödja Time Series Insights miljö och enhets simulerings lösning. Om du vill ta bort dem går du tillbaka till Azure Portal.

Från menyn till vänster i Azure Portal:

1. Välj **resurs grupps** ikonen. Välj sedan den resurs grupp som du skapade för Time Series Insightss miljön. Välj **ta bort resurs grupp**längst upp på sidan, ange namnet på resurs gruppen och välj **ta bort**.

1. Välj **resurs grupps** ikonen. Välj sedan den resurs grupp som skapades av enhets simulerings lösnings Accelerator. Välj **ta bort resurs grupp**längst upp på sidan, ange namnet på resurs gruppen och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en Time Series Insights-miljö.
> * Skapa en lösning för enhets simulering som innehåller en IoT-hubb.
> * Anslut Time Series Insightss miljön till IoT Hub.
> * Kör en enhets simulering för att strömma data till Time Series Insightss miljön.
> * Verifiera simulerade telemetridata.

Nu när du vet hur du skapar en egen Time Series Insights miljö, lär du dig hur du skapar ett webb program som använder data från en Time Series Insights-miljö:

> [!div class="nextstepaction"]
> [Läs exempel på den värdbaserade klient-SDK-visualiseringen](https://tsiclientsample.azurewebsites.net/)
