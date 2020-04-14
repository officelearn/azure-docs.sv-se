---
title: 'Snabbstart: Utforskaren för Azure Time Series Insights – Insikter i Azure Time Series | Microsoft-dokument'
description: Lär dig hur du kommer igång med Utforskaren för Azure Time Series Insights. Visualisera stora volymer IoT-data och turnera viktiga funktioner i din miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/13/2020
ms.openlocfilehash: a566c55d2ac51efc5ff2650ad682f10dae2bdc31
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272006"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Snabbstart: Utforska Azure Time Series Insights

Den här snabbstarten för Azure Time Series Insights Explorer hjälper dig att komma igång med Time Series Insights i en kostnadsfri demonstrationsmiljö. I den här snabbstarten får du lära dig hur du använder din webbläsare för att visualisera stora volymer IoT-data och turnényckelfunktioner som är allmänt tillgängliga nu.

Azure Time Series Insights är en fullständigt hanterad analys-, lagrings- och visualiseringstjänst som förenklar hur du utforskar och analyserar miljarder IoT-händelser samtidigt. Det ger dig en global bild av dina data så att du snabbt kan validera din IoT-lösning och undvika kostsamma driftstopp till verksamhetskritiska enheter. Azure Time Series Insights hjälper dig att upptäcka dolda trender, upptäcka avvikelser och utföra rotorsaksanalyser i nära realtid.

För ytterligare flexibilitet kan du lägga till Azure Time Series Insights i ett befintligt program via dess kraftfulla [REST-API:er](./time-series-insights-update-tsq.md) och [klient-SDK](https://github.com/microsoft/tsiclient). Du kan använda API:erna för att lagra, fråga och använda tidsseriedata i ett klientprogram som du väljer. Du kan också använda klienten SDK för att lägga till gränssnittskomponenter i ditt befintliga program.

Den här snabbstarten för Time Series Insights explorer erbjuder en guidad rundtur med funktioner som är i allmän tillgänglighet.

> [!IMPORTANT]
> Skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) om du inte redan har skapat ett.

## <a name="prepare-the-demo-environment"></a>Förbered demomiljön

1. I webbläsaren går du till den [allmänna tillgänglighetsdemonstrationen](https://insights.timeseries.azure.com/demo).

1. Om du uppmanas att logga in på Time Series Insights explorer med hjälp av dina Azure-kontouppgifter.

1. Snabbvisningssidan time series insights visar. Välj **Nästa** för att påbörja snabbturen.

   [![Snabbstart välkommen - välj Nästa](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Utforska demomiljön

1. **Tidsvalspanelen** visas. Använd den här panelen för att välja en tidsram som ska visualiseras.

   [![Panelen för val av tid](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Markera en tidsram och dra den i regionen. Välj sedan **Sök**.

   [![Välj en tidsram](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   Time Series Insights visar en diagramvisualisering för den tidsram du angav. Du kan utföra olika åtgärder i linjediagrammet. Du kan till exempel filtrera, fästa, sortera och stapla.

   Om du vill återgå till **urvalspanelen Tid**markerar du nedpilen enligt bilden:

   [![Diagram](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Välj **Lägg till** på panelen **Termer** om du vill lägga till en ny sökterm.

   [![Panelen Lägg till söktermer](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. I diagrammet kan du välja en region, högerklicka på regionen och välja **Utforska händelser**.

   [![Utforska händelser](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Ett rutnät med rådata visas från den region som du utforskar.

   [![Utforska händelser – rutnätsdatavy](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Markera och filtrera data

1. Redigera dina termer för att ändra värdena i diagrammet. Lägg till ytterligare en term för att korskorrelera olika typer av värden.

   [![Lägga till ett villkor](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Lämna **rutan Filterserie** tom om du vill visa alla valda söktermer eller ange en filterterm i rutan **Filterserie** för improviserad seriefiltrering.

   [![Filtrera serie](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   För snabbstarten anger du **Station5** om du vill korskorrelera temperatur och tryck för den stationen.

När du har slutfört snabbstarten kan du experimentera med exempeldata för att skapa olika visualiseringar.

## <a name="clean-up-resources"></a>Rensa resurser

Nu när du har slutfört självstudien rensar du de resurser du har skapat:

1. På den vänstra menyn i [Azure-portalen](https://portal.azure.com)väljer du **Alla resurser**, hitta din Azure Time Series Insights-resursgrupp.
1. Ta antingen bort hela resursgruppen (och alla resurser i den) genom att välja **Ta bort** eller ta bort varje resurs individuellt.

## <a name="next-steps"></a>Nästa steg

Du är redo att skapa din egen Time Series Insights-miljö:
> [!div class="nextstepaction"]
> [Planera din time series insights-miljö](time-series-insights-environment-planning.md)
