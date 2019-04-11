---
title: Utföra en rotorsaksanalys för en avisering – Azure | Microsoft Docs
description: Lär dig hur du utför en rotorsaksanalys på en avisering med hjälp av Azure Time Series Insights i den här självstudien.
author: aditidugar
ms.author: adugar
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 70d29359d4a4bcf9f5badbbf0c553d7bed88a02b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284578"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Självstudie: Utföra en rotorsaksanalys för en avisering

I den här självstudiekursen lär du dig hur du diagnostiserar rotorsaken till en avisering med acceleratorn Fjärrövervakningslösning. Du ser att en avisering har utlösts i fjärrövervakningslösningens instrumentpanel. Använd sedan Azure Time Series Insights-utforskaren för att undersöka orsaken.

I självstudien används två simulerade lastbilsenheter som skickar telemetri om plats, höjd, hastighet och lasttemperatur. Lastbilarna hanteras av en organisation som heter Contoso och är anslutna till acceleratorn Fjärrövervakningslösning. Som Contoso-operatör måste du veta varför en av dina lastbilar (lastbil-02) har loggat en avisering om låg temperatur.

I den här kursen för du göra följande:

>[!div class="checklist"]
> * Filtrera enheterna på instrumentpanelen
> * Visa telemetri i realtid
> * Visa dina data i Time Series Insights-utforskaren
> * Utföra en rotorsaksanalys för en avisering
> * Skapa en ny regel baserat på dina erfarenheter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Välja enheter som ska visas

Du kan välja vilka anslutna enheters som ska visas på sidan **Instrumentpanel** med hjälp av filter. Om du bara vill visa enheter av typen **Truck** (Lastbil) väljer du de inbyggda filtret **Trucks** (Lastbilar) i filterlistrutan:

[![Filtrera lastbilar på instrumentpanelen](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

När du använder ett filter visas endast de enheter som matchar filtervillkoren på kartan och i telemetripanelen på **Instrumentpanelen**. Du kan se att det finns två lastbilar som är anslutna till lösningsacceleratorn, inklusive **lastbil-02**.

## <a name="view-real-time-telemetry"></a>Visa telemetri i realtid

Lösningsacceleratorn kartlägger telemetridata i realtid på sidan **Instrumentpanel**. Som standard visar diagrammet höjdtelemetri, vilket varierar över tid:

[![Diagram med lastbilens höjdtelemetri](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Visa temperaturtelemetri för lastbilarna genom att klicka på **Temperatur** i **telemetripanelen**. Du kan se hur temperaturen för båda lastbilar har varierat under de senaste 15 minuterna. Du kan också se att en varning för låg temperatur har aktiverats för lastbil-02 i aviseringspanelen.

[![RM-instrumentpanelen med avisering för låg temperatur](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Utforska data

Öppna lastbilens telemetridata i Time Series Insights-utforskaren för att identifiera orsaken till larmet om låg temperatur. Klicka på någon av länkarna **Utforska i Time Series Insights** på instrumentpanelen:

[![RM-instrumentpanel med markerade TSI-länkar](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

När utforskaren startas visas alla dina enheter:

[![Startvyn i TSI-Utforskaren](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Filtrera enheterna genom att skriva **lastbil** i filterrutan och välj **temperatur** som **mått** i den vänstra panelen:

[![TSI-utforskaren lastbilstemperatur](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Du ser samma vy som du såg i instrumentpanelen för fjärrövervakning. Dessutom kan du nu zooma i närmare på den tidsram som aviseringen utlöstes i:

[![Zoom för TSI-utforskaren](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Du kan också lägga till i andra telemetriströmmar som kommer från lastbilar. Klicka på knappen **Lägg till** i det övre vänstra hörnet. Ett nytt fönster visas:

[![TSI-Utforskaren med nytt fönster](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

I det nya fönstret ändrar du namnet på den nya etiketten till **Enheter** så att den matchar den tidigare. Välj **höjd** som **Mått** och **iothub-anslutning-enhet-id** som värde för **Delning enligt** för att lägga till höjdtelemetri i vyn:

[![TSI-utforskaren med temperatur och höjd](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Diagnostisera aviseringen

Du kan se att höjdprofilerna avviker från varandra när du tittar på flödena i den aktuella vyn. Dessutom faller temperaturen för **lastbil-02** när transporterar når en hög höjd. Du förvånas av att se detta eftersom lastbilarna förväntades följa samma väg.

För att bekräfta din misstanke om att lastbilarna tog olika resvägar lägger du till ett annat fönster i sidopanelen med knappen **Lägg till**. I det nya fönstret ändrar du namnet på den nya etiketten till **Enheter** så att den matchar den tidigare. Välj **Höjd** som **Mått** och **iothub-anslutning-enhet-id** som värde för **Delning enligt** för att lägga till höjdtelemetri i vyn. Du kan se att lastbilarna tog olika vägar genom att titta på skillnaden mellan **longitud**-flödena:

[![TSI-utforskaren med temperatur, longitud och höjd](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Skapa en ny regel

Medan lastbilsvägar vanligtvis optimeras i förväg upptäcker du att trafikmönster, väder och andra oförutsedda händelser kan orsaka förseningar och låter lastbilsförarna fatta de slutgiltiga besluten baserat på deras omdöme. Men eftersom tillgångarna i lastbilarna är temperaturkänsliga bör du skapa en extra regel i din fjärrövervakningslösning. Den här regeln är till för att ge dig en varning om genomsnittshöjden är över 105 meter över ett 1-minutsintervall:

[![Fliken fjärrövervakningsregler höjdregel](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Läs föregående självstudie för information om att skapa och [identifiera enhetsproblem](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen fick du lära dig att använda Time Series Insights-utforskaren för att diagnostisera rotorsaken till en avisering med acceleratorn Fjärrövervakningslösning. Fortsätt till nästa självstudiekurs om du vill lära dig hur du använder lösningsacceleratorn till att identifiera och åtgärda problem med anslutna enheter.

> [!div class="nextstepaction"]
> [Använda enhetsaviseringar för att identifiera och åtgärda problem med enheter anslutna till din övervakningslösning](iot-accelerators-remote-monitoring-maintain.md)
