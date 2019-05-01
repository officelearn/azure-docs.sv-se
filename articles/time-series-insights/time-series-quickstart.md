---
title: 'Snabbstart: Azure Time Series Insights-utforskaren | Microsoft Docs'
description: Denna snabbstart visar hur du kommer igång med Azure Time Series Insights-utforskaren i webbläsaren för att visualisera stora IoT-datavolymer. Titta på huvudfunktionerna i en demomiljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: be663520c9c1afd11ace57cd9dcb8ffb8219b86b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696961"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Snabbstart: Utforska Azure Time Series Insights

En Snabbstart explorer tillhandahålls för att komma igång med Azure Time Series Insights i en gratis demonstrationsmiljö. Via det lär du dig att använda webbläsaren för att visualisera stora volymer med IoT data och rundtur viktiga funktioner för närvarande i allmänt tillgänglig.

Azure Time Series Insights är en helt hanterad tjänst analys, lagring och visualisering som förenklar hur du utforska och analysera miljarder IoT-händelser samtidigt. Det ger dig en global vy över dina data, så att du snabbt verifiera din IoT-lösning och undvika kostsamma avbrott på verksamhetskritiska enheter. Via Azure Time Series Insights kan du identifiera dolda trender, upptäcka avvikelser och utföra rotorsaksanalyser i nära realtid.

För extra flexibilitet Azure Time Series Insights kan läggas till ett redan existerande program via dess kraftfulla [REST API: er](./time-series-insights-update-tsq.md) och [klient-SDK](./tutorial-create-tsi-sample-spa.md). API: erna kan du lagra fråga time series-data och använda time series-data i ett klientprogram som du önskar. Du kan också välja att använda klient-SDK för att lägga till UI-komponenter i ditt befintliga program.

Time Series Insights explorer är en guidad visning av funktioner för närvarande allmänt tillgängliga.

## <a name="prepare-the-demo-environment"></a>Förbereda miljön demo

1. Skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) om något inte har skapats.

1. I din webbläsare, navigerar du till den [allmänt tillgängliga demo](https://insights.timeseries.azure.com/demo).

1. Om du uppmanas logga in i Time Series Insights-Utforskaren med dina autentiseringsuppgifter för Azure-konto.

1. Snabbvisningssidan för Time Series Insights visas. Klicka på **Nästa** för att starta snabbvisningen.

   [![Klicka på nästa](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Utforska demomiljö

1. **Panelen för val av tid** visas. Använd den här panelen för att välja en tidsram som ska visualiseras.

   [![Panelen för val av tid](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Klicka och dra i regionen och klicka sedan på knappen **Sök**.

   [![Välj ett tidsintervall](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   Time Series Insights visar en diagramvisualisering för den tidsram du angav. Du kan utföra olika åtgärder med linjediagrammet, t.ex. filtrera, fästa, sortera och stapla.

   Om du vill komma tillbaka till **panelen för val av tid** klickar du på nedåtpilen på det sätt som visas:

   [![Diagram](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Klicka på **Lägg till** i **panelen Villkor** för att lägga till en ny sökterm.

   [![Lägg till objekt](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. I diagrammet kan du välja en region, högerklicka på regionen och välja **Utforska händelser**.

   [![Utforska händelser](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Ett rutnät med dina rådata visas från den region du utforskar:

   [![Rutnätsvy](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Välja och filtrera data

1. Redigera villkoren om du vill ändra de värden som visas i diagrammet, och lägg till ytterligare villkor om du vill korskorrelera olika typer av värden:

   [![Lägga till ett villkor](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Ange ett filtervillkor i den **filtrera serie...**  kryssrutan för provisoriska spränganordningar seriefiltrering. För snabbstarten anger du **Station5** om du vill korskorrelera temperatur och tryck för den stationen.

   [![Filtrera serie](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

När du har slutfört snabbstarten kan du experimentera med exempeldata för att skapa olika visualiseringar.

## <a name="next-steps"></a>Nästa steg

Du är redo att skapa en egen Time Series Insights-miljö:
> [!div class="nextstepaction"]
> [Planera Time Series Insights-miljön](time-series-insights-environment-planning.md)
