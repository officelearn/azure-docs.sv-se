---
title: 'Snabb start: Azure Time Series Insights Explorer-Azure Time Series Insights | Microsoft Docs'
description: Lär dig hur du kommer igång med Azure Time Series Insights Explorer. Visualisera stora volymer med IoT-data och guidade funktioner i din miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 01/06/2020
ms.openlocfilehash: 21e9884ab4559b510b13691ac5cafccb753622d6
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690811"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Snabbstart: Utforska Azure Time Series Insights

Den här Azure Time Series Insights Explorer-snabb starten hjälper dig att komma igång med Time Series Insights i en kostnads fri demonstrations miljö. I den här snabb starten får du lära dig hur du använder webbläsaren för att visualisera stora mängder IoT-data och upptäckter som är allmänt tillgängliga nu.

Azure Time Series Insights är en helt hanterad tjänst för analys, lagring och visualisering som gör det enklare att utforska och analysera miljarder IoT-händelser samtidigt. Du får en global vy över dina data så att du snabbt kan verifiera din IoT-lösning och undvika kostsamma drift stopp för verksamhets kritiska enheter. Azure Time Series Insights hjälper dig att identifiera dolda trender, upptäcka avvikelser och utföra rotor Saks analyser i nära real tid.

För ytterligare flexibilitet kan du lägga till Azure Time Series Insights i ett befintligt program via dess kraftfulla [REST-API: er](./time-series-insights-update-tsq.md) och [klient-SDK](https://github.com/microsoft/tsiclient). Du kan använda API: er för att lagra, fråga och använda Time Series-data i ett valfritt klient program. Du kan också använda klient-SDK: n för att lägga till UI-komponenter i ditt befintliga program.

Den här snabb starten i Time Series Insights Explorer innehåller en guidad genom gång av funktioner som är allmänt tillgängliga.

> [!IMPORTANT]
> Skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) om du inte har skapat något redan.

## <a name="prepare-the-demo-environment"></a>Förbered demo miljön

1. Gå till [demonstrationen allmän tillgänglighet](https://insights.timeseries.azure.com/demo)i webbläsaren.

1. Om du uppmanas till det loggar du in på Time Series Insights Explorer med dina autentiseringsuppgifter för Azure-kontot.

1. Sidan Time Series Insights guidad visning visas. Välj **Nästa** för att starta den guidade visningen.

   [Välkommen till ![snabb start – Välj nästa](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Utforska demo miljön

1. **Panelen för val av tid** visas. Använd den här panelen för att välja en tidsram som ska visualiseras.

   [panel för ![val av tid](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Välj en tidsram och dra den i regionen. Välj sedan **Sök**.

   [![Välj en tidsram](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   Time Series Insights visar en diagramvisualisering för den tidsram du angav. Du kan utföra olika åtgärder i linje diagrammet. Du kan till exempel filtrera, fästa, sortera och stapla.

   Gå tillbaka till **panelen för val av tid**genom att välja nedåtpilen som visas:

   [![diagram](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Välj **Lägg till** på **panelen villkor** för att lägga till ett nytt sökord.

   [![Lägg till Sök villkors panel](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. I diagrammet kan du välja en region, högerklicka på regionen och välja **Utforska händelser**.

   [![utforska händelser](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Ett rutnät med dina rå data visas från den region som du utforskar.

   [![utforska händelser – rutnätsvy-datavy](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Markera och filtrera data

1. Redigera dina villkor för att ändra värdena i diagrammet. Lägg till en annan term för att korsa olika typer av värden.

   [![lägga till en term](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Lämna rutan **filter serie** tom om du vill se alla valda sökord eller ange en filter term i rutan **filter serie** för filtrering av Improvised-serier.

   [![filter serie](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   För snabbstarten anger du **Station5** om du vill korskorrelera temperatur och tryck för den stationen.

När du har slutfört snabbstarten kan du experimentera med exempeldata för att skapa olika visualiseringar.

## <a name="clean-up-resources"></a>Rensa resurser

Nu när du har slutfört självstudien rensar du de resurser som du har skapat:

1. På den vänstra menyn i [Azure Portal](https://portal.azure.com)väljer du **alla resurser**, letar reda på Azure Time Series Insights resurs gruppen.
1. Ta antingen bort hela resurs gruppen (och alla resurser som ingår i den) genom att välja **ta bort** eller ta bort varje resurs individuellt.

## <a name="next-steps"></a>Nästa steg

Du är redo att skapa din egen Time Series Insightss miljö:
> [!div class="nextstepaction"]
> [Planera Time Series Insights-miljön](time-series-insights-environment-planning.md)
