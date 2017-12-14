---
title: "Diagnostisera körning undantag med hjälp av Azure Application Insights | Microsoft Docs"
description: "Självstudiekurs om att hitta och diagnostisera körning undantag i ditt program med hjälp av Azure Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: f6844dd6747854a60ff8eb8be0d913b73ca2bdb2
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Hitta och diagnostisera körning undantag med Azure Application Insights

Azure Application Insights samlar in telemetri från ditt program för att identifiera och diagnostisera körning undantag.  Den här kursen tar dig igenom processen med ditt program.  Lär dig att:

> [!div class="checklist"]
> * Ändra ditt projekt för att aktivera undantagsspårning
> * Identifiera undantag för olika komponenter för programmet
> * Visa information om ett undantag
> * Hämta en ögonblicksbild av undantaget till Visual Studio för felsökning
> * Analysera information av misslyckade begäranden med hjälp av frågespråket
> * Skapa ett nytt arbetsobjekt för att åtgärda den felaktiga koden


## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

- Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling
    - Azure Development
- Hämta och installera den [Visual Studio ögonblicksbild Debugger](http://aka.ms/snapshotdebugger).
- Aktivera [Visual Studio ögonblicksbild felsökare](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)
- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](app-insights-asp-net.md). 
- Kursen spårar identifiering av ett undantag i ditt program, så ändra koden i din miljö för utveckling eller testning att generera ett undantag. 

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Analysera fel
Application Insights samlar in eventuella fel i programmet och kan du visa hur ofta mellan olika åtgärder som hjälper dig att fokusera arbetet på dem med störst påverkan.  Du kan sedan detaljgranska informationen av dessa fel för att identifiera orsaken.   

1. Välj **Programinsikter** och sedan din prenumeration.  
1. Öppna den **fel** panelen Välj antingen **fel** under den **Undersök** -menyn eller klicka på den **misslyckade begäranden** diagram.

    ![Misslyckade förfrågningar](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

2. Den **misslyckade begäranden** panelen visas antalet misslyckade begäranden och antalet användare som påverkas för varje åtgärd för programmet.  Du kan identifiera de fel som påverkar de flesta användare genom att sortera informationen av användaren.  I det här exemplet i **hämta anställda/skapa** och **hämta kunder/detaljer** lämpar sig troligt att undersöka på grund av deras stort antal fel och påverkade användare.  Om du väljer en åtgärd visas ytterligare information om den här åtgärden i den högra panelen.

    ![Det gick inte begäranden panelen](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

3. Minska tidsfönstret Zooma in perioden där fel visar en insamling.

    ![Fönster för misslyckade begäranden](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

4. Klicka på **visa information** att se detaljerna för åtgärden.  Detta inkluderar aktivitetsstaplar som visar två misslyckade beroenden som gemensamt tog nästan hälften av en sekund att slutföra.  Du hittar mer information om hur du analyserar prestandaproblem genom att slutföra kursen [hitta och diagnostisera prestandaproblem med Azure Application Insights](app-insights-tutorial-performance.md).

    ![Det gick inte begäranden information](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

5. Operations information visar också en FormatException som verkar ha orsakat felet.  Klicka på undantaget eller på den **upp 3 undantag typer** antal om du vill visa detaljerad information.  Du kan se att det är på grund av ett ogiltigt postnummer.

    ![Undantagsinformation](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)



## <a name="identify-failing-code"></a>Identifiera misslyckas kod
Ögonblicksbild felsökningsprogrammet samlar in ögonblicksbilder av de vanligaste undantag i ditt program som hjälper dig att lösa den grundläggande orsaken till produktion.  Du kan visa debug ögonblicksbilder i portalen för att se anropet stacken och inspektera variabler vid varje anropsstacken. Du kan sedan felsöka källkoden genom att hämta ögonblicksbilden och öppna den i Visual Studio-2017.

1. Klicka på Egenskaper för undantaget **öppna debug ögonblicksbild**.
2. Den **Debug ögonblicksbild** panelen öppnas med anropsstack för begäran.  Klicka på valfri metod för att visa värden för alla lokala variabler vid tidpunkten för begäran.  Från den översta metoden i det här exemplet kan vi se lokala variabler som har inget värde.

    ![Felsöka ögonblicksbild](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. Det första anropet som har giltiga värden är **ValidZipCode**, och vi kan se att ett postnummer angavs tillsammans med bokstäver som inte kan översättas till ett heltal.  Det verkar vara fel i den kod som behöver åtgärdas.

    ![Felsöka ögonblicksbild](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Om du vill hämta den här ögonblicksbilden i Visual Studio där vi kan hitta den faktiska koden som behöver åtgärdas klickar du på **hämta ögonblicksbild**.
6. Ögonblicksbilden har lästs in i Visual Studio.
7. Du kan nu köra en felsökningssessionen i Visual Studio som identifierar snabbt kodrad som orsakade undantaget.

    ![Undantag i koden](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Använd analysdata
Alla data som samlas in av Application Insights lagras i Azure Log Analytics, vilket ger ett rikt frågespråk som hjälper dig att analysera data i en mängd olika sätt.  Vi kan använda informationen för att analysera begäranden som genererats vi söka information om undantaget. 

8. Klicka på CodeLens informationen ovan kod för att visa telemetri som tillhandahålls av Application Insights.

    ![Kod](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Klicka på **analysera påverkan** att öppna Application Insights Analytics.  Det fylls med flera frågor som innehåller detaljerad information om misslyckade förfrågningar som påverkade användare, webbläsare och regioner.<br><br>![Analys](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Lägg till arbetsobjekt
Om du ansluter Application Insights till en spårningssystemet som Visual Studio Team Services eller GitHub, kan du skapa ett arbetsobjekt direkt från Application Insights.

1. Gå tillbaka till den **egenskaper undantag** panelen i Application Insights.
2. Klicka på **nytt arbetsobjekt**.
3. Den **nytt arbetsobjekt** panelen öppnas med detaljer om undantag som redan har fyllts.  Du kan lägga till ytterligare information innan du sparar den.

    ![Nya arbetsobjekt](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du identifierar körning undantag går du vidare till nästa kurs att lära dig att identifiera och diagnostisera prestandaproblem.

> [!div class="nextstepaction"]
> [Identifiera prestandaproblem](app-insights-tutorial-performance.md)