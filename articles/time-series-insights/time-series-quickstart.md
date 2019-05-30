---
title: 'Snabbstart: Azure Time Series Insights-utforskaren | Microsoft Docs'
description: Den här snabbstarten visar hur du kommer igång med Azure Time Series Insights-Utforskaren i webbläsaren för att visualisera stora volymer med IoT-data. Titta på huvudfunktionerna i en demomiljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 17aee2555a0d0364ca5eb8e260ae10432a19bce6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244048"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Snabbstart: Utforska Azure Time Series Insights

Den här snabbstarten för Azure Time Series Insights explorer hjälper dig att komma igång med Time Series Insights i en gratis demonstrationsmiljö. Lär dig hur du använder webbläsaren för att visualisera stora volymer med IoT data och rundtur viktiga funktioner som är allmänt tillgängliga via den.

Azure Time Series Insights är en helt hanterad tjänst analys, lagring och visualisering som förenklar hur du utforska och analysera miljarder IoT-händelser samtidigt. Det ger dig en global vy över dina data så att du kan snabbt verifiera din IoT-lösning och undvika kostsamma avbrott på verksamhetskritiska enheter. Azure Time Series Insights hjälper dig att identifiera dolda trender, upptäcka avvikelser och utföra rotorsaksanalyser i nära realtid.

För extra flexibilitet kan du lägga till Azure Time Series Insights till ett befintligt program via dess kraftfulla [REST API: er](./time-series-insights-update-tsq.md) och [klient-SDK](./tutorial-create-tsi-sample-spa.md). Du kan använda API: erna för att lagra, fråga, och använda time series-data i ett klientprogram som du önskar. Du kan också använda klient-SDK för att lägga till UI-komponenter i ditt befintliga program.

Den här snabbstarten för Time Series Insights explorer erbjuder en guidad visning av funktioner som är allmänt tillgängliga.

## <a name="prepare-the-demo-environment"></a>Förbereda miljön demo

1. Skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) om du inte redan har skapat en.

1. I webbläsaren går du till den [allmänt tillgängliga demo](https://insights.timeseries.azure.com/demo).

1. Om du uppmanas logga Time Series Insights explorer med hjälp av dina Azure-autentiseringsuppgifter.

1. Snabbvisningssidan för Time Series Insights visas. Välj **nästa** att starta snabbvisningen.

   [![Klicka på nästa](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Utforska demomiljö

1. Den **panelen för val av tid** visar. Använd den här panelen för att välja en tidsram som ska visualiseras.

   [![Panelen för val av tid](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Välj ett tidsintervall och dra i regionen. Välj sedan **Search**.

   [![Välj ett tidsintervall](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   Time Series Insights visar en diagramvisualisering för den tidsram du angav. Du kan utföra olika åtgärder i linjediagrammet. Du kan till exempel filtrera, fästa, sortera och stapla.

   Att återgå till den **panelen för val av tid**, väljer du på pilen så som visas:

   [![Diagram](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Välj **Lägg till** i den **termpanel** att lägga till en ny sökterm.

   [![Lägg till objekt](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. I diagrammet kan du välja en region, högerklicka på regionen och välja **Utforska händelser**.

   [![Utforska händelser](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Ett rutnät med dina rådata visas från regionen du utforskar.

   [![Rutnätsvy](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Välja och filtrera data

1. Redigera villkoren om du vill ändra värdena i diagrammet. Lägg till ytterligare en period om du vill korskorrelera olika typer av värden.

   [![Lägga till ett villkor](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Ange ett filtervillkor i den **filtrera serie** kryssrutan för provisoriska spränganordningar seriefiltrering. För snabbstarten anger du **Station5** om du vill korskorrelera temperatur och tryck för den stationen.

   [![Filtrera serie](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

När du har slutfört snabbstarten kan du experimentera med exempeldata för att skapa olika visualiseringar.

## <a name="next-steps"></a>Nästa steg

Är du redo att skapa en egen Time Series Insights-miljö:
> [!div class="nextstepaction"]
> [Planera Time Series Insights-miljön](time-series-insights-environment-planning.md)
