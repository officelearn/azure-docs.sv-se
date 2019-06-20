---
title: 'Självstudier: Skapa en Azure Time Series Insights-miljö | Microsoft Docs'
description: Lär dig hur du skapar en Time Series Insights-miljö som är ifyllda med data från simulerade enheter.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 06a450c47c7264bdecb663c9f71e3a9753df5e1e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273504"
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

Time Series Insights-miljö är där enhetsdata som samlas in och lagras. En gång lagras den [Azure Time Series Insights explorer](time-series-quickstart.md) och [Time Series Insights fråge-API](/rest/api/time-series-insights/ga-query-api) kan användas för att fråga efter och analysera data.

Azure IoT Hub är händelsekällan som används av alla enheter (simulerade eller fysiska) i självstudien på ett säkert sätt ansluta och överföra data till din Azure-molnet.

Den här självstudien använder också en [IoT lösningsaccelerator](https://www.azureiotsolutions.com) att generera och strömma exempeltelemetridata som IoT Hub.

>[!TIP]
> [IoT-Lösningsacceleratorer](https://www.azureiotsolutions.com) tillhandahåller företagsklass förkonfigurerade lösningar som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

Börja med att skapa enheten simuleringslösning, vilket genererar testdata för att fylla i din Time Series Insights-miljö.

1. I ett separat fönster eller flik går du till [azureiotsolutions.com](https://www.azureiotsolutions.com). Logga in med samma konto för Azure-prenumeration och välj den **Enhetssimulering** accelerator.

   [![Kör Enhetssimulering accelerator](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Ange de obligatoriska parametrarna på den **skapa Enhetssimulering lösning** sidan.

   Parameter|Beskrivning
   ---|---
   **Distributionsnamn** | Det här unika värdet används för att skapa en ny resursgrupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen.
   **Azure-prenumeration** | Ange samma prenumeration som användes för att skapa en Time Series Insights-miljö i föregående avsnitt.
   **Distributionsalternativ** | Välj **etablera nya IoT-hubben** att skapa en ny IoT hub som är specifika för den här självstudien.
   **Azure-plats** | Ange samma region som används för att skapa en Time Series Insights-miljö i föregående avsnitt.

   När du är klar väljer du **skapa lösning** att etablera lösningen Azure-resurser. Det kan ta upp till 20 minuter att slutföra den här processen.

   [![Etablera enheten simulering lösningen](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. När etableringen har slutförts ändras texten ovanför den nya lösningen från **etablering** till **redo**.

   >[!IMPORTANT]
   > Markera inte **starta** ännu! Behåll den här sidan öppen eftersom du kommer tillbaka till den senare.

   [![Lösning för enheter simulering etablering klar](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Nu kan granska de nyligen skapade resurserna i Azure-portalen. På den **resursgrupper** Observera att en ny resursgrupp har skapats med hjälp av den **lösningsnamn** i det sista steget. Anteckna de resurser som har skapats för enhetssimulering.

   [![Simulering enhetsresurser](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Skapa en miljö

Skapa sedan en Time Series Insights-miljö i din Azure-prenumeration.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ditt Azure-prenumeration. 
1. Välj **+ skapa en resurs** i det övre vänstra hörnet. 
1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**. 

   [![Välj Time Series Insights-miljöresursen](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. På den **Time Series Insights-miljö** fyller du i de obligatoriska parametrarna.

   Parameter|Beskrivning
   ---|---
   **Miljönamn** | Välj ett unikt namn för Time Series Insights-miljö. Namn som används av Time Series Insights explorer och [fråga API: er](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Prenumeration** | Prenumerationer är containrar för Azure-resurser. Välj en prenumeration för att skapa Time Series Insights-miljö.
   **Resursgrupp** | En resursgrupp är en container för Azure-resurser. Välj en befintlig resursgrupp eller skapa ett nytt lösenord för resursen för Time Series Insights-miljö.
   **Location** | Välj ett dataområde center för din Time Series Insights-miljö. Skapa Time Series Insights-miljö i samma region som andra IoT-resurser för att undvika ytterligare fördröjning.
   **Nivå** | Välj det genomflöde som behövs. Välj **S1**.
   **Kapacitet** | Kapaciteten är multiplikatorn som tillämpas på den inkommande hastigheten och lagringskapacitet som är associerade med den valda SKU: N. Du kan ändra kapaciteten när du har skapat. Välj en kapacitet på **1**.

   När du är klar väljer **granska + skapa** för att gå vidare till nästa steg.

   [![Skapa en resurs för Time Series Insights-miljö](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Nu kan ansluta Time Series Insights-miljö till IoT-hubben som skapats av Solution Accelerator. Ange **Välj en hubb** till `Select existing`. Välj IoT-hubben som skapats av Solution Accelerator när du ställer in **IoT-hubbnamn**.

   [![Ansluta Time Series Insights-miljön till den skapade IoT hub](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

1. Kontrollera den **meddelanden** panelen för att övervaka distributionen slutförande. 

   [![Time Series Insights-miljö distributionen lyckades](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Kör enhetssimulering att sända data

Nu när distributionen och den inledande konfigurationen är klar kan du fylla i Time Series Insights-miljö med exempeldata från [simulerade enheter som skapats av snabbtangenten](#create-a-device-simulation).

Tillsammans med IoT hub skapades en Azure App Service-webbapp för att skapa och överföra simulerad enhetstelemetri.

1. Gå tillbaka till din [Instrumentpanel för lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard). Logga in igen, om det behövs med hjälp av samma Azure-konto du använde i den här självstudien. Nu kan du välja **starta** under ”Enhetssimulering”-lösning.

     [![Instrumentpanelen för Lösningsacceleratorer](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Simulering web-app för enheter börjar med att uppmana dig att ge webbprogrammet ”logga in och läsa din profil” behörighet. Den här behörigheten kan programmet hämta användarprofilinformation krävs för att stödja driften av programmet.

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

Den här självstudiekursen skapar flera aktiva Azure-tjänster för att stödja lösningen för Time Series Insights-miljö och enheten simulering. Ta bort dem genom att gå tillbaka till Azure-portalen.

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
