---
title: "Diagnostisera prestandaproblem med hjälp av Azure Application Insights | Microsoft Docs"
description: "Självstudiekurs om att hitta och diagnostisera prestandaproblem i programmet med hjälp av Azure Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 1176e6ac33db5b9428a323c3a6271818807afc72
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Hitta och diagnostisera prestandaproblem med Azure Application Insights

Azure Application Insights samlar in telemetri från ditt program för att analysera dess drift och prestanda.  Du kan använda informationen för att identifiera problem som kan uppstå eller för att identifiera förbättringar av programmet som de flesta påverkan användare.  Den här kursen hjälper dig att analysera prestandan för både serverkomponenter för ditt program och klientens perspektiv.  Lär dig att:

> [!div class="checklist"]
> * Identifiera prestanda för serversidan åtgärder
> * Analysera åtgärder för att fastställa orsaken till dåliga prestanda
> * Anger den långsammaste klientsidan åtgärder
> * Analysera information om sidvisningar med frågespråket


## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

- Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling
    - Azure Development
- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](app-insights-asp-net.md).
- [Aktivera Application Insights profileraren](app-insights-profiler.md#enable-the-profiler) för ditt program.

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identifiera långsamma åtgärder
Application Insights samlar in prestandainformation för olika åtgärder i ditt program.  Genom att identifiera dessa åtgärder med den längsta varaktigheten kan diagnostisera problem eller bäst mål din pågående utveckling för att förbättra prestandan för programmet.

1. Välj **Programinsikter** och sedan välja din prenumeration.  
1. Öppna den **prestanda** panelen Välj antingen **prestanda** under den **Undersök** -menyn eller klicka på den **serversvarstid** diagram .

    ![Prestanda](media/app-insights-tutorial-performance/performance.png)

2. Den **prestanda** panelen visas antalet och genomsnittlig varaktighet för varje åtgärd för programmet.  Du kan använda den här informationen för att identifiera dessa åtgärder som påverkar de flesta användare. I det här exemplet den **hämta kunder/detaljer** och **GET-Home/Index** lämpar sig troligt att undersöka på grund av deras relativt hög varaktighet och antalet anrop.  Andra åtgärder kan ha en högre varaktighet men har sällan kallas, så att effekten av deras improvement skulle vara minimal.  

    ![Panelen prestanda](media/app-insights-tutorial-performance/performance-blade.png)

3. Diagrammet visar för närvarande Genomsnittlig varaktighet för alla åtgärder över tid.  Lägg till de åtgärder som du är intresserad av genom att fästa dem till diagrammet.  Det visar att det inte finns några toppar värt att undersöka.  Isolera detta ytterligare genom att minska tidsfönstret för diagrammet.

    ![Åtgärder för PIN-kod](media/app-insights-tutorial-performance/pin-operations.png)

4.  Klicka på en åtgärd för att visa dess prestanda panel till höger. Detta visar fördelningen av för olika begäranden.  Användare märka vanligtvis långsam prestanda på ungefär en halv sekund, så Förminskar fönstret till begäranden via 500 millisekunder.  

    ![Tid för distribution](media/app-insights-tutorial-performance/duration-distribution.png)

5.  I det här exemplet ser du att ett stort antal begäranden tar över en andra att bearbeta. Du kan se information om den här åtgärden genom att klicka på **driftsinformation**.

    ![Åtgärdsinformation](media/app-insights-tutorial-performance/operation-details.png)

6.  Den information som du har samlat in hittills endast bekräftar att det är långsam prestanda, men den visas lite att hämta roten.  Den **Profiler** kan hjälpa dig med detta genom att visa den faktiska koden som körde för åtgärden och den tid som krävs för varje steg. Vissa åtgärder kan inte ha en spårning eftersom profileraren körs regelbundet.  Fler åtgärder ska ha spårningar med tiden.  Starta profiler för åtgärden, klicka på **Profiler-spårningar**.
5.  Spårningen visar enskilda händelser för varje åtgärd så att du kan diagnostisera orsaken till varaktigheten för övergripande igen.  Klicka på någon av de översta exemplen som har den längsta tid.
6.  Klicka på **visa varm sökväg** att markera specifika sökvägen för händelser som de flesta bidra till den totala varaktigheten för åtgärden.  I det här exemplet ser du att långsammaste anropet kommer från *FabrikamFiberAzureStorage.GetStorageTableData* metod. Den del som tar de flesta tid är den *CloudTable.CreateIfNotExist* metod. Om kod körs varje gång som funktionen anropas, används onödiga nätverket samtals- och processoranvändning. Det bästa sättet att åtgärda koden är att placera den här raden i vissa startmetod som bara utföras för en gång. 

    ![Profiler-information](media/app-insights-tutorial-performance/profiler-details.png)

7.  Den **prestanda tips** stöder bedömningen överdriven varaktighet beror på att vänta överst på skärmen.  Klicka på den **väntar på** länk dokumentation om tolkning av olika typer av händelser.

    ![Tips för prestanda](media/app-insights-tutorial-performance/performance-tip.png)

8.  För ytterligare analys, kan du klicka på **hämta etl trace** att ladda ned spårningen i Visual Studio.

## <a name="use-analytics-data-for-server"></a>Använda analysdata för servern
Application Insights Analytics ger ett rikt frågespråk som hjälper dig att analysera alla data som samlas in av Application Insights.  Du kan använda den för att utföra djupanalys på begäran-och prestandadata.

1. Tillbaka till panelen åtgärden detaljer och klicka på knappen Analytics.

    ![Knappen Analytics](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Application Insights Analytics öppnas med en fråga för var och en av vyerna i panelen.  Du kan köra dessa frågor som de är eller ändra dem för dina behov.  Den första frågan visar varaktighet för den här åtgärden över tid.

    ![Analys](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identifiera långsamma Klientåtgärder
Förutom att identifiera serverprocesser för att optimera analysera Application Insights klientwebbläsare perspektiv.  Detta kan hjälpa dig att identifiera möjliga förbättringar av klientkomponenter och även identifierar problem med olika webbläsare eller olika platser.

1. Välj **webbläsare** under **Undersök** att öppna webbläsaren sammanfattning.  Detta ger en visuell översikt över olika telemetries för ditt program för webbläsaren.

    ![Översikt över webbläsare](media/app-insights-tutorial-performance/browser-summary.png)

2.  Rulla ned till **vilka är de långsammaste sidorna?**.  Detta visar en lista över sidorna i ditt program som har tagit den längsta tid för klienter att läsa in.  Du kan använda den här informationen för att prioritera de sidor som har störst inverkan på användaren.
3.  Klicka på någon av sidorna för att öppna den **sidvy** panelen.  I det här exemplet kan den **/FabrikamProd** sidan visas en orimlig Genomsnittlig varaktighet.  Den **sidvy** panelen innehåller information om den här sidan, inklusive en uppdelning av annan varaktighet intervall.

    ![Vyn sida](media/app-insights-tutorial-performance/page-view.png)

4.  Klicka på den högsta varaktigheten om du vill granska information om dessa begäranden.  Klicka på enskilda begäran om att visa information om klienten begär inklusive typ av webbläsare och dess plats.  Den här informationen kan hjälpa dig att avgöra om det finns prestanda problem relaterade till specifika typer av klienter.

    ![Information om begäran](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Använd analysdata för klient
Som data samlas in för serverprestanda tillgängliggör Application Insights alla Klientdata för djupgående analys med hjälp av Analytics.

1. Tillbaka till webbläsaren sammanfattning och klicka på ikonen Analytics.

    ![Analytics-ikon](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Application Insights Analytics öppnas med en fråga för var och en av vyerna i panelen. Den första frågan visar hur länge olika visningslägen över tid.

    ![Analys](media/app-insights-tutorial-performance/client-analytics.png)

3.  Smart Diagnostics är en funktion i Application Insights Analytics som identifierar unikt mönster i data.  När du klickar på Smart diagnostik punkt i linjediagrammet körs samma fråga utan de poster som orsakade avvikelseidentifiering.  Information om de posterna som visas i avsnittet kommentar för frågan så att du kan identifiera egenskaper för dessa sidvisningar som orsakar långa varaktighet.

    ![Smart diagnostik](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du identifierar körning undantag går du vidare till nästa kurs att lära dig hur du skapar aviseringar som svar på fel.

> [!div class="nextstepaction"]
> [Varning i programmets hälsotillstånd](app-insights-tutorial-alert.md)
