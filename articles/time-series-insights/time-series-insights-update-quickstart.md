---
title: 'Snabbstart: Utforska demomiljön för förhandsversionen – Insikter i Azure Time Series | Microsoft-dokument'
description: Utforska viktiga funktioner i demomiljön för förhandsversionen av Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 02/07/2020
ms.openlocfilehash: dc4a8da69b0398c6487008c106a9f5bcdb8a885e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77110233"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Snabbstart: Utforska demomiljön för förhandsversionen av Azure Time Series Insights

Den här snabbstarten får dig att börja med förhandsversionen av Azure Time Series Insights. I den kostnadsfria demon turnerar du viktiga funktioner som har lagts till i Förhandsversionen av Time Series Insights.

Demomiljön Time Series Insights Preview innehåller ett scenarioföretag, Contoso, som driver två vindkraftverksgrupper. Varje gård har 10 turbiner. Varje turbin har 20 sensorer som rapporterar data varje minut till Azure IoT Hub. Sensorerna samlar in information om väderförhållanden, bladlutning och girposition. Information om generatorprestanda, växellådans beteende och säkerhetsmonitorer registreras också.

I den här snabbstarten får du lära dig hur du använder Time Series Insights för att hitta användbara insikter i Contoso-data. Du utför också en kort grundorsaksanalys för att bättre förutsäga kritiska fel och utföra underhåll.

> [!IMPORTANT]
> Skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) om du inte har ett.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Se Time Series Insights-utforskaren i en demomiljö

Informationsutforskaren För förhandsversionen av Time Series Insights visar historiska data och rotorsaksanalys. Så här kommer du igång:

1. Gå till [Demomiljön contoso wind farm.](https://insights.timeseries.azure.com/preview/samples)  

1. Om du uppmanas att logga in på Time Series Insights explorer med hjälp av dina Azure-kontouppgifter.

## <a name="work-with-historical-data"></a>Arbeta med historiska data

1. I **Contoso Plant 1**, titta på vindkraftverk **W7**.  

   1. Ändra vyintervallet till **1/1/17 20:00:00.00 till 3/10/17 20:00:00.00 (UTC)**.
   1. Om du vill välja en sensor väljer du **Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed**. Granska sedan de värden som visas.

      [![W7 i Contoso Plant 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. Contoso upptäckte nyligen en brand i vindturbin **W7**. Åsikterna går isär om vad som orsakade branden. I Time Series Insights visas brandvarningssensorn som aktiverades under branden.

   1. Ändra vyintervallet till **20:00/00 3/9 20:00.00 till 3/10/17 20:00:00.00 (UTC).**
   1. Välj **Säkerhetssystem** > **FireAlert**.

      [![Contoso hittade en brand i vindkraftverk W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Granska andra händelser runt tiden för branden för att förstå vad som hände. Oljetrycket och aktiva varningar spetsade strax före branden.

   1. Välj **Pitch System** > **HydraulicOilPressure**.
   1. Välj **Pitch System** > **ActiveWarning**.

      [![Granska andra händelser ungefär samtidigt](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Oljetrycket och de aktiva varningssensorerna spetsade precis före branden. Expandera den visade tidsserien för att granska andra tecken som var uppenbara som ledde fram till branden. Båda sensorerna fluktuerade konsekvent över tiden. Fluktuationerna indikerar ett ihållande och oroande mönster.

    * Ändra vyintervallet till **2/24/17 20:00:00.00 till 3/10/17 20:00:00.00 (UTC)**.

      [![Oljetryck och aktiva varningssensorer ökade också](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Undersöka två år av historiska data avslöjar en annan brand händelse som hade samma sensor fluktuationer.

    * Ändra visningsområdet till **1/1/16 till 12/31/17** (alla data).

      [![Leta efter historiska mönster](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Genom att använda Time Series Insights och sensortelemetri har vi upptäckt en långsiktig trend som är dold i historiska data. Med dessa nya insikter kan vi:

* Förklara vad som faktiskt hände.
* Rätta till problemet.
* Sätt bättre varningsmeddelandesystem på plats.

## <a name="root-cause-analysis"></a>Rotorsaksanalys

1. Vissa scenarier kräver sofistikerad analys för att upptäcka ledtrådar i data. Välj väderkvarn **W6** på datum **6 /25**.

    1. Ändra vyintervallet till **20:00 00/17 20:00.00 till 7/1/17 20:00:00.00 (UTC).**
    1. Välj **Contoso Plant 1** > **W6** > **Säkerhetssystem** > **VoltageActuatorSwitchWarning**.

       [![Ändra visningsområdet och välj W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Varningen indikerar ett problem med spänningen från generatorn. Generatorns totala uteffekt ligger inom normala parametrar i det aktuella intervallet. Genom att öka vårt intervall framträder ett annat mönster. En drop-off är uppenbar.

    1. Ta bort **VoltageActuatorSwitchWarning** sensorn.
    1. Välj **Generator System** > **ActivePower**.
    1. Ändra intervallet till **3d**.

       [![Ändra intervallet till 3d](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Genom att utöka tidsintervallet kan vi avgöra om problemet har upphört eller om det fortsätter.

    * Förläng tidsperioden till 60 dagar.

      [![Förläng tidsspannet till 60 dagar](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Andra sensordatapunkter kan läggas till för att ge större sammanhang. Ju fler sensorer vi ser, desto mer förståelse för problemet är. Nu släpper vi en markör för att visa de faktiska värdena. 

    1. Välj **Generator System**och välj sedan tre sensorer: **GridVoltagePhase1**, **GridVoltagePhase2**och **GridVoltagePhase3**.
    1. Släpp en markör på den sista datapunkten i det synliga området.

       [![Släpp en markör](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Två av spänningssensorerna fungerar jämförbart och inom normala parametrar. Det ser ut som **GridVoltagePhase3** sensorn är den skyldige.

1. Med mycket kontextuella data tillagda, fas 3 drop-off verkar ännu mer vara problemet. Nu har vi ett bra försprång på orsaken till varningen. Vi är redo att hänskjuta problemet till vårt underhållsteam.  

    * Ändra skärmen så att alla **Generator System-sensorer** överlagras på samma diagramskala.

      [![Ändra skärmen så att den innehåller allt](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Nu när du har slutfört snabbstarten rensar du de resurser som du har skapat:

1. På den vänstra menyn i [Azure-portalen](https://portal.azure.com)väljer du **Alla resurser**och letar reda på resursgruppen Azure Time Series Insights.
1. Ta antingen bort hela resursgruppen (och alla resurser i den) genom att välja **Ta bort** eller ta bort varje resurs individuellt.

## <a name="next-steps"></a>Nästa steg

Du är redo att skapa din egen förhandsversion av Time Series Insights. Så här startar du:

> [!div class="nextstepaction"]
> [Planera en förhandsversion av Time Series Insights-miljön](time-series-insights-update-plan.md)

Lär dig att använda demon och dess funktioner:

> [!div class="nextstepaction"]
> [Förhandsgranskningsutforskaren Förhandsgranskning av Time Series Insights](time-series-insights-update-explorer.md)