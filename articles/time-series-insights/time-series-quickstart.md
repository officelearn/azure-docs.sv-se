---
title: Snabbstart – Azure Time Series Insights-utforskaren | Microsoft Docs
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
ms.date: 11/15/2017
ms.openlocfilehash: 6b2a21cf50c03c8e44794540074dabf90049bc7c
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275524"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Snabbstart: Utforska Azure Time Series Insights
Denna snabbstart visar hur du kommer igång med Azure Time Series Insights-utforskaren i en gratis demonstrationsmiljö. Du lär dig hur du kan använda webbläsaren för att visualisera stora volymer med IoT-data, och du får se huvudfunktionerna i Time Series Insights-utforskaren. 

Azure Time Series Insights är en fullständigt hanterad tjänst för analys, lagring och visualisering som gör det enkelt att utforska och analysera miljarder IoT-händelser samtidigt. Du får en global vy över dina data som hjälper dig att snabbt verifiera din IoT-lösning och undvika kostsamma avbrott på verksamhetskritiska enheter. Du får hjälp med att identifiera dolda trender, upptäcka avvikelser och utföra rotorsaksanalyser i nära realtid.  Om du skapar ett program som behöver lagra eller fråga tidsseriedata kan du utveckla med hjälp av Time Series Insights REST API:erna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Utforska Time Series Insights-utforskaren i en demomiljö

1. Öppna webbläsaren och navigera till [https://insights.timeseries.azure.com/demo](https://insights.timeseries.azure.com/demo). 

2. Logga in på Time Series Insights-utforskaren med dina autentiseringsuppgifter för Azure-kontot om du uppmanas att göra det. 
 
3. Snabbvisningssidan för Time Series Insights visas. Klicka på **Nästa** för att starta snabbvisningen.

   ![Klicka på Nästa](media/quickstart/quickstart1.png)

4. **Panelen för val av tid** visas. Använd den här panelen för att välja en tidsram som ska visualiseras.

   ![Panelen för val av tid](media/quickstart/quickstart2.png)

5. Klicka och dra i regionen och klicka sedan på knappen **Sök**.
 
   ![Välj en tidsram](media/quickstart/quickstart3.png) 

   Time Series Insights visar en diagramvisualisering för den tidsram du angav. Du kan utföra olika åtgärder med linjediagrammet, t.ex. filtrera, fästa, sortera och stapla. 

   Om du vill komma tillbaka till **panelen för val av tid** klickar du på nedåtpilen på det sätt som visas:

   ![Diagram](media/quickstart/quickstart4.png)

6. Klicka på **Lägg till** i **panelen Villkor** för att lägga till en ny sökterm.

   ![Lägga till objekt](media/quickstart/quickstart5.png)

7. I diagrammet kan du välja en region, högerklicka på regionen och välja **Utforska händelser**.
 
   ![Utforska händelser](media/quickstart/quickstart6.png)

   Ett rutnät med dina rådata visas från den region du utforskar:

   ![Rutnätsvy](media/quickstart/quickstart7.png)

8. Redigera villkoren om du vill ändra de värden som visas i diagrammet, och lägg till ytterligare villkor om du vill korskorrelera olika typer av värden:

   ![Lägga till ett villkor](media/quickstart/quickstart8.png)

9. Ange ett filtervillkor i rutan **Filtrera serie...** om du vill använda ad hoc-seriefiltrering. För snabbstarten anger du **Station5** om du vill korskorrelera temperatur och tryck för den stationen.
 
   ![Filtrera serie](media/quickstart/quickstart9.png)

När du har slutfört snabbstarten kan du experimentera med exempeldata för att skapa olika visualiseringar. 

### <a name="next-steps"></a>Nästa steg
Du är redo att skapa en egen Time Series Insights-miljö:
> [!div class="nextstepaction"]
> [Planera Time Series Insights-miljön](time-series-insights-environment-planning.md)
