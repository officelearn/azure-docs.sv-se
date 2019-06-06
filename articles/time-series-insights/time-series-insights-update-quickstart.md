---
title: 'Snabbstart: Utforska demomiljön för förhandsversionen av Azure Time Series Insights | Microsoft Docs'
description: Förstå demomiljön för förhandsversionen av Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 0bbab2acc9bf9e22e1d3c36336aa9dad04b0b73a
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688506"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Snabbstart: Utforska demomiljön för förhandsversionen av Azure Time Series Insights

Den här snabbstarten kommer du igång med förhandsversionen av Azure Time Series Insights-miljö. I den kostnadsfria demon rundtur viktiga funktioner som har lagts till Time Series Insights Preview.

Time Series Insights Preview demomiljö innehåller ett scenario företaget Contoso, som körs två vind turbinen servergrupper. Varje grupp har 10 syfte. Varje turbin har 20 sensorer som rapporterar data varje minut till Azure IoT Hub. Sensorerna samla in information om väderförhållanden, bladet försäljningsargument och yaw position. Information om generator prestanda, växellåda beteende och säkerhet Övervakare också registreras.

I den här snabbstarten får du lära dig hur du använder Time Series Insights för att hitta värdefulla insikter i Contoso-data. Du genomför också en kort Rotorsaksanalys att bättre förutse kritiska problem och för att utföra underhåll.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Se Time Series Insights-utforskaren i en demomiljö

Förhandsversionen av Time Series Insights explorer visar historiska data och analys av rotorsaker. Så här kommer du igång:

1. Skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) om du inte har något.

1. Gå till den [Contoso vind servergruppen demo](https://insights.timeseries.azure.com/preview/samples) miljö.  

1. Om du uppmanas logga Time Series Insights explorer med hjälp av dina Azure-autentiseringsuppgifter.

## <a name="work-with-historical-data"></a>Arbeta med historiska data

1. I **Contoso anläggning 1**, titta på vindturbin **W7**.  

   1. Ändra vy intervallet till **1/1/17 20:00 till 17/10/3 20:00 (UTC)** .
   1. Om du vill välja en sensor **Contoso anläggning 1** > **W7** > **Generator System** > **GeneratorSpeed** . Granska de värden som visas.

      [![W7 på Contoso anläggningen 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Contoso upptäckte nyligen en brand i vindturbin **W7**. Yttranden variera om vad som orsakade fire. Vi kan se att fire avisering sensorn har aktiverats vid fire i Time Series Insights.

   1. Ändra vy intervallet till **3/9/17 20:00 till 17/10/3 20:00 (UTC)** .
   1. Välj **säkerhetssystem** > **FireAlert**.

      [![Contoso finns fire i vindturbin W7](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Granska andra händelser ungefär samma tidpunkt som fire att förstå vad som hänt. Tryck för olja och aktiva varningar som högst nådde precis före fire.

   1. Välj **luta System** > **HydraulicOilPressure**.
   1. Välj **luta System** > **ActiveWarning**.

      [![Granska andra händelser ungefär samma tidpunkt](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Olja hög belastning och aktiva varningen sensorer som högst nådde strax innan startar. Expandera visas tidsserien om du vill se andra tecken som var tydligt ledde till fire. Båda sensorer växlade konsekvent under tiden. På grund av variationer tyda på ett beständigt och lite nervöst mönster.

    * Ändra vy intervallet till **2/24/17 20:00 till 17/10/3 20:00 (UTC)** .

      [![Tryck för olja och den aktiva varningen sensorer också högst nådde](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Undersöka två år av historiska data visar en annan fire-händelse som hade samma sensor variationer.

    * Ändra vy intervallet till **1/1/16 till 12/31/17** (alla data).

      [![Leta efter historiska mönster](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Med hjälp av Time Series Insights och våra sensor telemetri, har vi upptäckt en långsiktig och problematiska trend som är dolda i historiska data. Med dessa nya insikter kan vi:

> [!div class="checklist"]
> * Beskriver vad som verkligen inträffade.
> * Rätta till problemet.
> * Placera överlägsen avisering om system på plats.

## <a name="root-cause-analysis"></a>Rotorsaksanalys

1. Vissa scenarier kräver avancerad analys för att få fram diskret ledtrådar i data. Välj windmill **W6** på datum **6/25**.

    1. Ändra vy intervallet till **6/1/17 20:00-7/1/17 20:00 (UTC)** .
    1. Välj **Contoso anläggning 1** > **W6** > **säkerhetssystem** > **VoltageActuatorSwitchWarning**.

       [![Ändra intervallet vyn och välj W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. Varningen anger ett problem med spänningen från generatorn. Totala effekt av generatorn arbetar inom normal parametrar i det aktuella intervallet. Genom att öka våra intervall, växer en annan mönstret. En bestämd Samlingsbibliotek är påtaglig.

    1. Ta bort den **VoltageActuatorSwitchWarning** sensorn.
    1. Välj **Generator System** > **ActivePower**.
    1. Ändra intervallet **3d**.

       [![Ändra intervallet till 3d](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Genom att expandera tidsintervallet kan vi fastställa om problemet är stoppad eller om det fortsätter.

    * Utöka tidsintervallet till 60 dagar.

      [![Utöka tidsintervallet till 60 dagar](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Andra sensorn datapunkter kan läggas till att ge större sammanhang. Flera sensorer vi visa, desto mer fullständig vår förståelse för problemet är. Vi släpper en markör för att se de faktiska värdena. 

    1. Välj **Generator System**, och välj sedan tre sensorer: **GridVoltagePhase1**, **GridVoltagePhase2**, och **GridVoltagePhase3**.
    1. Släpp en markör på den sista datapunkten i det synliga området.

       [![Ta bort en markör](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Två av spänningssensorer fungerar samma prestanda och inom normal parametrar. Det ser ut som den **GridVoltagePhase3** sensorn är orsaken.

1. Fas 3-Samlingsbibliotek visas ännu mer till problem med hög kontextuella data som har lagts till. Nu har vi ett bra lead på orsaken till varningen. Vi är redo att referera problemet till vårt underhållsteam för.  

    * Ändra skärmen för att täcka över alla **Generator System** sensorer på samma diagram skala.

      [![Ändra skärmen för att omfatta allt](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Du är redo att skapa en egen Time Series Insights Preview-miljö. Så här startar:

> [!div class="nextstepaction"]
> [Planera en förhandsversion av Time Series Insights-miljön](time-series-insights-update-plan.md)

Lär dig att navigera demon och dess funktioner:

> [!div class="nextstepaction"]
> [I förhandsversionen av Time Series Insights-Utforskaren](time-series-insights-update-explorer.md)
