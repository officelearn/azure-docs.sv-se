---
title: Skapa anpassade instrumentpaneler i Azure Application Insights | Microsoft Docs
description: Självstudie för att skapa anpassade KPI-instrumentpaneler med Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: caf8b1899cad95ade6297e78e8f2cf35939ef189
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77661642"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Skapa anpassade KPI-instrumentpaneler med Azure Application Insights

Du kan skapa flera instrumentpaneler i Azure-portalen som var och en inkluderar paneler som visualiserar data från flera Azure-resurser i olika grupper och prenumerationer.  Du kan fästa olika diagram och vyer från Azure Application Insights för att skapa anpassade instrumentpaneler som ger dig en fullständig bild av programmets hälsa och prestanda. Den här självstudien vägleder dig igenom skapandet av en anpassad instrumentpanel som inkluderar fler typer av data och visualiseringar från Azure Application Insights.  Lär dig att:

> [!div class="checklist"]
> * Skapa en anpassad instrumentpanel i Azure
> * Lägga till en panel från panelgalleriet
> * Lägga till standardmått i Application Insights till instrumentpanelen
> * Lägga till ett anpassat måttdiagram i Application Insights till instrumentpanelen
> * Lägg till resultaten av en loggfiler (analys) i instrument panelen



## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](../../azure-monitor/app/asp-net.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Skapa en ny instrumentpanel
En enda instrumentpanel kan innehålla resurser från flera program, resursgrupper och prenumerationer.  Börja självstudien genom att skapa en ny instrumentpanel för ditt program.  

1. I instrumentpanelen väljer du **Ny instrumentpanel**.

   ![Ny instrumentpanel](media/tutorial-app-dashboards/1newdashboard.png)

1. Ange ett namn för instrumentpanelen.
1. Ta en titt i **panelgalleriet** och se olika paneler som du kan lägga till i instrumentpanelen.  Förutom att lägga till paneler från galleriet kan du fästa diagram och andra vyer direkt från Application Insights till instrument panelen.
1. Leta rätt på panelen **Markdown** och dra den till din instrumentpanel.  Med den här panelen kan du lägga till text som är formaterad i markdown, vilket är idealiskt för att lägga till beskrivande text på din instrument panel.
1. Lägg till text i panelens egenskaper och ändra storlek på den på instrumentpanelens arbetsyta.
    
    ![Redigera Markdown-panel](media/tutorial-app-dashboards/2dashboard-text.png)

1. Klicka på **Anpassningen är klar** överst på skärmen för att avsluta läget för panelanpassning.

## <a name="add-health-overview"></a>Lägga till hälsoöversikt
En instrument panel med statisk text är inte mycket intressant, så nu lägger du till en panel från Application Insights för att visa information om ditt program.  Du kan lägga till Application Insights-paneler från panelgalleriet eller fästa dem direkt från skärmarna i Application Insights.  På så sätt kan du konfigurera tabeller och vyer som du redan är bekant med innan du fäster dem på instrumentpanelen.  Börja med att lägga till en standardhälsoöversikt för programmet.  Det kräver ingen konfiguration och gör en minimal anpassning i instrumentpanelen.


1. Välj din **Application Insights**-resurs på startsidan.
2. I **översikts** fönstret klickar du på PIN- ![ikonen PIN](media/tutorial-app-dashboards/pushpin.png) -ikon för att lägga till panelen till den senaste instrument panelen som du visade.  
 
3. I det övre högra hörnet visas ett meddelande om att din panel har fästs på instrument panelen. Klicka på **Fäst på instrumentpanelen** i meddelandet för att återgå till instrumentpanelen eller använda fönstret instrumentpanelen.
4. Panelen läggs nu till i instrumentpanelen. Välj **Redigera** för att ändra placeringen av panelen. Klicka och dra den på plats och klicka sedan på **Anpassningen är klar**. Instrumentpanelen har nu en panel med lite användbar information.

    ![Instrumentpanel med översiktens tidslinje](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Lägga till anpassade måttdiagram
I panelen **Mått** kan du visa ett diagram över ett mått som samlas in av Application Insights över tid med valfritt filter och valfri gruppering.  Du kan lägga till det här diagrammet på instrumentpanelen, precis som med allt annat i Application Insights.  Det kräver lite anpassning först.

1. Välj din **Application Insights**-resurs på startsidan.
1. Välj **Mått**.  
2. Ett tomt diagram har redan skapats, och du uppmanas att lägga till ett mått.  Lägg till ett mått i diagrammet och lägg eventuellt till ett filter och en gruppering.  I exemplet nedan visas antalet serverbegäranden grupperade efter framgång.  Då får du en löpande vy över slutförda och misslyckade begäranden.

    ![Lägga till mått](media/tutorial-app-dashboards/metrics.png)

4. Välj **Fäst på instrumentpanelen** till höger. Då läggs vyn till i instrumentpanelen som du arbetade med senast.

3.  I det övre högra hörnet visas ett meddelande om att din panel har fästs på instrument panelen. Klicka på **Fäst på instrumentpanelen** i meddelandet för att återgå till instrumentpanelen eller använda instrumentpanelsbladet.

4. Panelen läggs nu till i instrumentpanelen. Välj **Redigera** för att ändra placeringen av panelen. Klicka och dra den på plats och klicka sedan på **Anpassningen är klar**.

## <a name="add-logs-analytics-query"></a>Lägg till loggar (analys) fråga
Azure Application Insights-loggar (analyser) tillhandahåller ett omfattande frågespråk som gör att du kan analysera alla data som samlas in Application Insights. Precis som med diagram och andra vyer kan du lägga till utdata från en loggad fråga på din instrument panel.

Eftersom Azure Applications Insights-loggar (Analytics) är en separat tjänst måste du dela din instrument panel för att den ska kunna innehålla en loggfils fråga. När du delar en Azure-instrumentpanel publicerar du den som en Azure-resurs, som kan göra den tillgänglig för andra användare och resurser.  

1. Överst i instrumentpanelens skärm klickar du på **Dela**.

    ![Publicera instrumentpanel](media/tutorial-app-dashboards/8dashboard-share.png)

2. Behåll samma **namn på instrumentpanelen** och välj **prenumerationens namn** för att dela instrumentpanelen.  Klicka på **Publicera**.  Nu är instrumentpanelen tillgänglig för andra tjänster och prenumerationer.  Om du vill kan du definiera specifika användare som ska ha åtkomst till instrumentpanelen.
1. Välj din **Application Insights**-resurs på startsidan.
2. Klicka på **loggar (analys)** till vänster under övervakning för att öppna loggar-portalen (Analytics).
3. Skriv följande fråga som returnerar de 10 mest efterfrågade sidorna och antal förfrågningar:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Klicka på **Kör** för att validera frågans resultat.
5. Klicka på ikonen fäst ![Fästikon](media/tutorial-app-dashboards/pushpin.png) och välj namnet på din instrument panel. Anledningen till att det här alternativet innebär att du väljer en instrument panel till skillnad från föregående steg där den senaste instrument panelen användes beror på att logg konsolen (Analytics) är en separat tjänst och måste väljas från alla tillgängliga delade instrument paneler.

5. Innan du går tillbaka till instrument panelen ska du lägga till en annan fråga, men den här gången återger den som ett diagram så att du kan se olika sätt att visualisera en loggfils fråga på en instrument panel. Börja med följande fråga som summerar de 10 vanligaste åtgärderna med flest undantag.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Välj **Diagram** och byt till en **ring** för att visualisera resultatet.

    ![Loggar (analys) diagram](media/tutorial-app-dashboards/11querychart.png)

6. Klicka på ikonen fäst ![Fästikon](media/tutorial-app-dashboards/pushpin.png) längst upp till höger för att fästa diagrammet på instrument panelen och den här gången väljer du länken för att återgå till instrument panelen.
4. Resultatet av frågorna läggs nu till i instrumentpanelen i det format du har valt.  Klicka och dra var och en på plats och klicka sedan på **Anpassningen är klar**.
5. Välj Penn ikonen ![Pennikon](media/tutorial-app-dashboards/pencil.png) på varje rubrik för att ge dem ett beskrivande namn.

5. Välj **Dela** för att återpublicera ändringarna i instrumentpanelen som nu innehåller många olika diagram och visualiseringar från Application Insights.


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig att skapa anpassade instrumentpaneler kan du ta en titt på resten av Application Insights-dokumentationen, som innehåller en fallstudie.

> [!div class="nextstepaction"]
> [Djupgående diagnostik](../../azure-monitor/app/devops.md)
