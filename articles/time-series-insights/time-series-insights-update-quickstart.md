---
title: Utforska demomiljön för Azure Time Series Insights (förhandsversion) | Microsoft Docs
description: Förstå demomiljön för Azure Time Series Insights (förhandsversion)
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 12/03/2018
ms.openlocfilehash: 76bdc122cab5b4ee87717c9c4df9774ecc3bd8f7
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888791"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>Utforska demomiljön för Azure Time Series Insights (förhandsversion)

Den här snabbstarten visar hur du kommer igång med förhandsversionen av Azure Time Series Insights-utforskaren (TSI) i en gratis demonstrationsmiljö. Du lär dig hur du kan använda webbläsaren för att visualisera stora volymer med historiska industriella IoT-data, och du får se huvudfunktionerna i Azure Time Series Insights-utforskaren (förhandsversion).

Azure TSI utgör ett heltäckande plattform som tjänst-erbjudande för att mata in, bearbeta, lagra och fråga efter mycket kontextualiserade, tidsserieoptimerade IoT-skalningsdata för ad hoc-datagranskning samt operationell analys. Time Series Insights är ett differentierat erbjudande som skräddarsytts efter industriella IoT-distributioners unika behov.

Demomiljön visar ett elbolag, Contoso, som använder TSI för att hitta åtgärdsinriktade insikter i sina data och utföra en kort rotorsaksanalys. Contoso driver två vindkraftparker, med 10 vindkraftverk var, och varje vindkraftverk har 20 sensorer som rapporterar data varje minut till Azure IoT Hub. Sensorerna samlar in information om väderförhållanden, bladens vridning, generatorprestanda, växellådans funktion och säkerhetsövervakning.

TSI (förhandsversion) används för att analysera den ständigt växande datauppsättningen från de senaste två åren – för närvarande på 40 GB – för att bättre förstå och förutsäga både kritiska fel och långsamma underhållsproblem.

Om du inte har en Azure-prenumeration skapar du ett  [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  innan du börjar.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Utforska Time Series Insights-utforskaren i en demomiljö

1. Gå till  [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples) i webbläsaren.  

1. Logga in i TSI-utforskaren med dina autentiseringsuppgifter för Azure-kontot om du uppmanas att göra det.

### <a name="demo-step-one"></a>Demo – steg ett

1. Vi tar en titt på **vindkraftverk 7 i vindkraftpark 1**.  

    * **Åtgärd**: Uppdatera visningsintervallet till `1/1/17 20:00 – 3/10/17 20:00 (UTC)` och lägg till sensorn `Farm 1 > W7 > Generator > GeneratorSpeed`. Visa sedan värdena.

       ![Snabbstart ett][1]

1. **Contoso upptäckte nyligen en brand i vindkraftverk 7**. Nu tittar vi närmare. Vi kan se att brandaviseringssensorn var aktiverad under brandperioden.

    * **Åtgärd**: Uppdatera visningsintervallet till `3/9/17 20:00 – 3/10/17 20:00 (UTC)` och lägg till sensorn `Safety > FireAlert`.

      ![Snabbstart två][2]

1. Nu tittar vi på vad mer som hände vid ungefär samma tidpunkt som branden. Både oljetryck och aktiva varningar hade toppar precis före branden men då var det för sent för att förhindra problemet.

    * **Åtgärd**: Lägg till sensorn `Pitch > HydraulicOilPressure` och sensorn `Pitch > ActiveWarning`.

      ![Snabbstart tre][3]

1. Om vi zoomar ut kan vi se att det fanns tecken som ledde fram till branden. Båda sensorerna fluktuerade. Så har det här hänt tidigare?

    * **Åtgärd**: Uppdatera visningsintervallet till `2/24/17 20:00 – 3/10/17 20:00 (UTC)`.

      ![Snabbstart fyra][4]

1. Om vi tittar närmare på de två årens data kan vi se en tidigare brandhändelse med samma tecken. Med hjälp av dessa data skapar vi system för att identifiera sådana här problem tidigt.

    * **Åtgärd**: Uppdatera visningsintervallet till `1/1/16 – 12/31/17` (alla data).

       ![Snabbstart fem][5]

### <a name="demo-step-two"></a>Demo – steg två

1. Andra problem är mer subtila och svårare att diagnostisera. Time Series Insights tillhandahåller en mängd funktioner som hjälper oss att identifiera svåra problem. Här kan vi se ett varningssensoravbrott på `turbine #6` datumet `6/25`. Men vad händer egentligen?

    * **Åtgärd**: Ta bort de aktuella sensorerna. Uppdatera sedan visningsintervallet till `6/1/17 20:00 – 7/1/17 20:00 (UTC)` och lägg till `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`.

       ![Snabbstart sex][6]

1. Varningen anger ett problem med spänningen från generatorn. Men vad är orsaken? Den totala uteffekten från generatorn ser bra ut med detaljerade intervall. Men genom att aggregera data kan vi se en tydlig minskning.

    * **Åtgärd**: Ta bort `VoltageActuatorSwitchWarning` och lägg till `Generator > ActivePower` och uppdatera intervallet till `3d`.

       ![Snabbstart sju][7]

1. Om vi går framåt i datauppsättningen kan vi se att det här inte bara är ett tillfälligt problem. Problemet fortsätter.

    * **Åtgärd**: Utöka tidsintervallet till höger.

       ![Snabbstart åtta][8]

1. Nu tittar vi ännu närmare. Vi kan visa andra sensordatapunkter för att visa spänning efter fas. Men alla ser jämförbara ut. Vi släpper en markör för att se de faktiska värdena. Det verkar vara ett problem med uteffekten från fas 3.

    * **Åtgärd**: `Add Generator > GridVoltagePhase1, 2, & 3`. Släpp en markör på den sista datapunkten i det synliga området.

       ![Snabbstart åtta][8]

1. Om vi visar alla tre på samma skala blir minskningen för fas 3 ännu tydligare. Nu kan vi skicka vidare problemet till vårt underhållsteam med en bra uppfattning om orsaken till varningen.  

    * **Åtgärd**: Uppdatera visningen för att överlägga alla sensorer på samma diagramskala.

       ![Snabbstart nio][9]

## <a name="next-steps"></a>Nästa steg

Du är redo att skapa en egen Azure TSI-miljö (förhandsversion):

> [!div class="nextstepaction"]
> [Planera en Azure TSI-miljö (förhandsversion)](time-series-insights-update-plan.md)

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