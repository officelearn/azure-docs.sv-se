---
title: 'Snabbstart: Utforska demomiljön för förhandsversionen av Azure Time Series Insights | Microsoft Docs'
description: Förstå demomiljön för förhandsversionen av Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276840"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Snabbstart: Utforska demomiljön för förhandsversionen av Azure Time Series Insights

Den här snabbstarten visar hur du använder förhandsversionen av Azure Time Series Insights-utforskaren i en kostnadsfri demonstrationsmiljö. Du lär dig att använda webbläsaren för att visualisera stora volymer med historiska industriella IoT-data, och du får se huvudfunktionerna i Time Series Insights-utforskaren.

Time Series Insights innehåller en plattform för slutpunkt till slutpunkt som en tjänst (PaaS). Det kan mata in, bearbeta, lagra och köra frågor på sammanhangsbaserade och tidsserieoptimerade IoT-skalningsdata vid improviserad datautforskning. Det ger också operativa analyser. Time Series Insights är ett differentierat erbjudande som skräddarsytts efter industriella IoT-distributioners unika behov.

Demomiljön visar elproduktionsföretaget Contoso. I miljön använder du Time Series Insights för att hitta användbara insikter i Contosos data, och du utför även en kort analys av rotorsakerna. Contoso har två vindkraftverk med vardera 10 turbiner. Varje turbin har 20 sensorer som rapporterar data varje minut till Azure IoT Hub. Sensorerna samlar in information om väderförhållanden, bladens vridning, generatorprestanda, växellådans funktion och säkerhetsövervakning.

Du kan använda förhandsversionen av Time Series Insights till att analysera Contosos ständigt växande datamängd från de senaste två åren, vilken för närvarande är 40 GB. Det kan hjälpa dig att bättre förstå och förutsäga både kritiska fel och underhållsproblem som åtgärdas för långsamt.

Om du inte har en Azure-prenumeration skapar du ett  [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  innan du börjar.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Se Time Series Insights-utforskaren i en demomiljö

1. I webbläsaren går du till  [Contosos miljö för vindkraftverk](https://insights.timeseries.azure.com/preview/samples).  

1. Logga in på Time Series Insights-utforskaren med dina autentiseringsuppgifter för Azure-kontot om du uppmanas att göra det.

### <a name="demo-step-1"></a>Demo – steg 1

1. Låt oss ta en titt på vindturbinen **W7** i **Contosos anläggning 1**.  

    * **Åtgärd**: Uppdatera visningsintervallet till **1/1/17 20:00 till 3/10/17 20:00 (UTC)**, lägg till **Contosos anläggning 1** > **W7** > **Generatorsystem** > **GeneratorSpeed**-sensorn och visa sedan resulterande värden.

       ![Snabbstart ett][1]

1. Contoso upptäckte nyligen en brand i vindturbin **W7**. Nu tittar vi närmare. Vi kan se att brandsensorn aktiverades under branden.

    * **Åtgärd**: Uppdatera visningsintervallet till **3/9/17 20:00 till 3/10/17 20:00 (UTC)** och lägg till **Säkerhetssystem** > **FireAlert**-sensorn.

      ![Snabbstart två][2]

1. Nu tittar vi på vad mer som hände vid ungefär samma tidpunkt som branden. Både oljetryck och aktiva varningar sköt i höjden strax före branden, men då var det för sent att förhindra problemet.

    * **Åtgärd**: Lägg till **Nivåsystem** > **HydraulicOilPressure**-sensorn och **Nivåsystem** > **ActiveWarning**-sensorn.

      ![Snabbstart tre][3]

1. Om vi zoomar ut kan vi se att det fanns tecken som ledde fram till branden. Båda sensorerna fluktuerade. Har detta hänt tidigare?

    * **Åtgärd**: Uppdatera visningsintervallet till **2/24/17 20:00 till 3/10/17 20:00 (UTC)**.

      ![Snabbstart fyra][4]

1. Om vi tittar närmare på de två årens data kan vi se en tidigare brandhändelse med samma tecken. Med hjälp av dessa data kan vi skapa system för att identifiera sådana här problem tidigt.

    * **Åtgärd**: Uppdatera visningsintervallet till **1/1/16 till 12/31/17** (alla data).

       ![Snabbstart fem][5]

### <a name="demo-step-2"></a>Demo – steg 2

1. Andra problem är mer subtila och svårare att diagnostisera. Time Series Insights erbjuder en mängd funktioner som hjälper oss att hitta svåra problem. Här kan vi se en störning i varningssensorn för **W6** den **25 juni**. Men vad händer egentligen?

    * **Åtgärd**: Ta bort de aktuella sensorerna, uppdatera visningsintervallet till **6/1/17 20:00 till 7/1/17 20:00 (UTC)** och lägg sedan till **Contosos anläggning 1** > **W6** > **Säkerhetssystem** > **VoltageActuatorSwitchWarning**-sensorn.

       ![Snabbstart sex][6]

1. Varningen anger ett problem med spänningen från generatorn. Men vad är orsaken? Den totala uteffekten från generatorn ser bra ut med detaljerade intervall. Genom att aggregera datan kan vi se en tydlig minskning.

    * **Åtgärd**: Ta bort **VoltageActuatorSwitchWarning**-sensorn, lägg till **Generatorsystem** > **ActivePower**-sensorn och uppdatera intervallet till **3 dagar**.

       ![Snabbstart sju][7]

1. Om vi går vidare i datamängden kan vi se att det här inte är ett tillfälligt problem. Det fortsätter.

    * **Åtgärd**: Utöka tidsintervallet åt höger.

       ![Snabbstart åtta][8]

1. Nu tittar vi ännu närmare. Vi kan lägga till andra sensordatapunkter för att visa spänning per fas. Men alla datapunkter ser ungefär lika ut. Vi släpper en markör för att se de faktiska värdena. Det verkar vara ett problem med uteffekten från fas 3.

    * **Åtgärd**: Lägg till **Generator System** > **GridVoltagePhase1**-, **GridVoltagePhase2**- och **GridVoltagePhase3**-sensorerna. Släpp en markör på den sista datapunkten i det synliga området.

       ![Snabbstart åtta][8]

1. Om vi visar alla tre datapunkterna i samma skala, blir minskningen för fas 3 ännu tydligare. Nu kan vi skicka vidare problemet till vårt underhållsteam med en bra uppfattning om orsaken till varningen.  

    * **Åtgärd**: Uppdatera visningen för att lägga över alla sensorer på samma diagramskala.

       ![Snabbstart nio][9]

## <a name="next-steps"></a>Nästa steg

Du är redo att skapa en egen förhandsversion av Time Series Insights-miljön:

> [!div class="nextstepaction"]
> [Planera en förhandsversion av Time Series Insights-miljön](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
