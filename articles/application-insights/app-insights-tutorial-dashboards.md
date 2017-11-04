---
title: Skapa anpassade instrumentpaneler i Azure Application Insights | Microsoft Docs
description: "Självstudiekurs om att skapa anpassade KPI instrumentpaneler med hjälp av Azure Application Insights."
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 0d2f98ca2fb39289b2916ddd24590924856507d6
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Skapa anpassade KPI instrumentpaneler med hjälp av Azure Application Insights

Du kan skapa flera instrumentpaneler i Azure-portalen som varje inkludera paneler visualisera data från flera Azure-resurser mellan olika resursgrupper och prenumerationer.  Du kan fästa olika tabeller och vyer från Azure Application Insights för att skapa anpassade instrumentpaneler som ger dig fullständig bild av hälsan och prestanda för ditt program.  Den här självstudiekursen vägleder dig genom att skapa en anpassad instrumentpanel som innehåller flera typer av data och visualiseringar från Azure Application Insights.  Lär dig att:

> [!div class="checklist"]
> * Skapa en anpassad instrumentpanel i Azure
> * Lägg till en panel från galleriet sida vid sida
> * Lägg till standard mått i Application Insights på instrumentpanelen 
> * Lägga till ett anpassat mått diagram Application Insights på instrumentpanelen
> * Lägga till resultatet av en Analytics-fråga på instrumentpanelen 



## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](app-insights-asp-net.md). 

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Skapa en ny instrumentpanel
En enda instrumentpanel kan innehålla resurser från flera program, resursgrupper och prenumerationer.  Starta guiden genom att skapa en ny instrumentpanel för ditt program.  

2.  Huvudfönstret i portalen och välj **ny instrumentpanel**.

    ![Ny instrumentpanel](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. Ange ett namn för instrumentpanelen.
4. Ta en titt på den **panelen galleriet** för olika paneler som du kan lägga till din instrumentpanel.  Förutom att lägga till paneler från galleriet kan du fästa diagram och andra vyer direkt från Application Insights på instrumentpanelen.
5. Leta upp den **Markdown** panelen och dra in på din instrumentpanel.  Den här panelen kan du lägga till text som har formaterats i markdown idealisk för att lägga till beskrivande text i instrumentpanelen.
6. Lägga till text i på panelen Egenskaper och storlek på arbetsytan för instrumentpanelen.
    
    ![Redigera markdown sida vid sida](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. Klicka på **klar anpassa** överst på skärmen för att avsluta panelen anpassning läge och sedan **publicera ändringar** att spara ändringarna.

    ![Instrumentpanel med markdown sida vid sida](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>Lägg till hälsa översikt
En instrumentpanel med bara statisk text inte mycket intressanta, så nu lägga till en panel från Application Insights för att visa information om ditt program.  Du kan lägga till Application Insights brickor från galleriet panelen eller du kan fästa dem direkt från Application Insights skärmar.  På så sätt kan du konfigurera tabeller och vyer som du redan är bekant med innan du fäster dem på instrumentpanelen.  Starta genom att lägga till standard hälsa översikt för ditt program.  Det krävs ingen konfiguration och gör minimal anpassning på instrumentpanelen.


1. Välj **Programinsikter** i Azure-menyn och välj sedan dina program.
2. I den **översikt över tidslinjen**, Välj snabbmenyn och klickar på **fäst på instrumentpanelen**.  Detta lägger till panelen till den senaste instrumentpanel som du tittade.  

    ![Översikt över tidslinjen för PIN-kod](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. Överst på skärmen klickar du på **visa instrumentpanelen** att återgå till instrumentpanelen.
4. Översikt över tidslinjen läggs nu till din instrumentpanel.  Klicka och dra den på plats och sedan på **klar anpassa** och **publicera ändringar i**.  Din instrumentpanel har nu en panel med användbar information.

    ![Instrumentpanel med översikt över tidslinjen](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>Lägga till anpassade mått diagram
Den **mått** panelen kan du ett diagram över ett mått som samlas in av Application Insights över tid med valfritt filter och gruppering.  Du kan lägga till det här diagrammet på instrumentpanelen som allt annat i Application Insights.  Detta kräver att du ska göra lite anpassning först.

1. Välj **Programinsikter** i Azure-menyn och välj sedan dina program.
1. Välj **mått**.  
2. Ett tomt diagram redan har skapats och du uppmanas att lägga till ett mått.  Lägga till ett mått i diagrammet och eventuellt lägga till ett filter och en gruppering.  Exemplet nedan visar antalet serverbegäranden grupperade efter lyckades.  Detta ger en körs vy av lyckade och misslyckade förfrågningar.

    ![Lägg till mått](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. Välj snabbmenyn för diagrammet och välj **fäst på instrumentpanelen**.  Vyn läggs till i den senaste instrumentpanel som du arbetade med.

    ![PIN-kod mått diagram](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. Överst på skärmen klickar du på **visa instrumentpanelen** att återgå till instrumentpanelen.

4. Översikt över tidslinjen läggs nu till din instrumentpanel.  Klicka och dra den på plats och sedan på **klar anpassa** och sedan **publicera ändringar i**. 

    ![instrumentpanel med mått](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>Metrics Explorer
**Metrics Explorer** liknar mått trots att den tillåter betydligt mer anpassning när de läggs till på instrumentpanelen.  Där du kurva dina beror på dina specifika önskemål och krav.

1. Välj **Programinsikter** i Azure-menyn och välj sedan dina program.
1. Välj **Metrics Explorer**. 
2. Klicka om du vill redigera diagrammet och välj ett eller flera mått och eventuellt en detaljerad konfiguration.  Exemplet visar ett linjediagram spåra genomsnittlig sidan svarstid.
3. Klicka på ikonen PIN-kod i övre behörighet att lägga till diagrammet på din instrumentpanel och dra den på plats.

    ![Metrics Explorer](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. Panelen Metrics Explorer tillåter anpassning av mer när den har lagts till på instrumentpanelen.  Högerklicka på panelen och välj **redigera titel** att lägga till en anpassad rubrik.  Gå vidare och göra andra anpassningar om du vill.

    ![Instrumentpanel med metrics explorer](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. Nu har du Metrics Explorer-diagram lagts till på instrumentpanelen.

    ![Instrumentpanel med metrics explorer](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Lägg till Analytics-fråga
Azure Application Insights Analytics ger ett rikt frågespråk som hjälper dig att analysera alla data som samlas in Application Insights.  Du kan lägga till utdata från en Analytics-fråga på instrumentpanelen precis som diagram och andra vyer.   

Eftersom Azure program insikter Analytics är en separat tjänst, behöver du dela instrumentpanelen för att den ska innehålla en Analytics-fråga.  När du delar en Azure instrumentpanel kan publicera du den som en Azure-resurs som kan göra den tillgänglig för andra användare och resurser.  

1. Överst på skärmen instrumentpanelen klickar du på **resursen**.

    ![publicera instrumentpanelen](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. Behåll den **instrumentpanelens namn** samma och väljer den **prenumerationsnamn** att dela instrumentpanelen.  Klicka på **Publicera**.  Instrumentpanelen är nu tillgänglig för andra tjänster och prenumerationer.  Alternativt kan du definiera specifika användare som ska ha åtkomst till instrumentpanelen.
1. Välj **Programinsikter** i Azure-menyn och välj sedan dina program.
2. Klicka på **Analytics** överst på skärmen för att öppna Analytics-portalen.

    ![Starta Analytics](media/app-insights-tutorial-dashboards/start-analytics.png)

3. Skriv följande fråga som returnerar de översta 10 mest efterfrågade sidorna och antal begäran:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Klicka på **Gå** kontrollera resultatet av frågan.
5. Klicka på ikonen PIN-kod och välj namnet på instrumentpanelen.  Orsaken till att det här alternativet har du väljer en instrumentpanel till skillnad från föregående steg där senaste instrumentpanelen användes är eftersom konsolen Analytics är en separat tjänst och behöver välja från alla tillgängliga delade instrumentpaneler.

    ![PIN-kod Analytics-fråga](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. Innan du går tillbaka till instrumentpanelen, lägga till en annan fråga, men nu visa den som ett schema så att du kan se de olika sätten att visualisera Analytics-fråga på en instrumentpanel.  Börja med följande fråga som sammanfattar de översta 10 åtgärderna med de flesta undantag.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Välj **diagram** och ändra till en **ringdiagram** visualisera utdata.

    ![Analytics-diagram](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. Klicka på ikonen PIN-kod för att fästa diagrammet på din instrumentpanel och nu Klicka på länken om du vill gå tillbaka till instrumentpanelen.
4. Resultatet av frågorna läggs nu till din instrumentpanel i det format som du har valt.  Klicka och dra varje på plats och klicka sedan på **är klar med redigeringen**.
5. Högerklicka på var och en av de paneler och välj **redigera rubrik** att ge dem en beskrivande rubrik.

    ![Instrumentpanel med Analytics](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. Klicka på **publicera ändringar** att genomföra ändringarna till din instrumentpanel som innehåller nu en mängd olika diagram och visualiseringar från Application Insights.


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du skapar anpassade instrumentpaneler, ta en titt på resten av Application Insights dokumentationen inklusive en fallstudie.

> [!div class="nextstepaction"]
> [Djupgående diagnostik](app-insights-devops.md)