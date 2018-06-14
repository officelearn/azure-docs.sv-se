---
title: Diagnostisera prestandaproblem med hjälp av Azure Application Insights | Microsoft Docs
description: Självstudie om att hitta och diagnostisera prestandaproblem i dina program med hjälp av Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 8489992303425cc00c15994b55ade958d77549e4
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
ms.locfileid: "29969142"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Hitta och diagnostisera prestandaproblem med Azure Application Insights

Azure Application Insights samlar in telemetri från ditt program för att analysera dess drift och prestanda.  Du kan använda den här informationen till att identifiera problem som kan uppstå eller till att identifiera vilka förbättringar av programmet som skulle gynna flest användare.  Den här kursen hjälper dig att analysera prestandan både för programmets serverkomponenter och ur klientens perspektiv.  Lär dig att:

> [!div class="checklist"]
> * identifiera prestandan för åtgärder på serversidan
> * analysera åtgärder för att fastställa grundorsaken till den dåliga prestandan
> * identifiera de långsammaste åtgärderna på klientsidan
> * analysera information om sidvisningar med frågespråk.


## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

- Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling
    - Azure Development
- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](app-insights-asp-net.md).
- [Aktivera Application Insights-profileraren](app-insights-profiler.md#installation) för ditt program.

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identifiera långsamma serveråtgärder
Application Insights samlar in prestandainformation för de olika åtgärderna i ditt program. Genom att identifiera vilka åtgärder som tar längst tid kan du diagnostisera potentiella problem och fatta välgrundade beslut kring din pågående utveckling för att förbättra programmets prestanda.

1. Välj **Application Insights** och sedan din prenumeration.  
1. Du öppnar panelen **Prestanda** genom att antingen välja **Prestanda** på menyn **Undersök** eller klicka på diagrammet **Serversvarstid**.

    ![Prestanda](media/app-insights-tutorial-performance/performance.png)

2. På panelen **Prestanda** visas antal och genomsnittlig varaktighet för varje åtgärd i programmet.  Du kan använda den här informationen till att identifiera vilka åtgärder som påverkar användarna mest. I det här exemplet är **GET Customers/Details** och **GET Home/Index** lämpliga kandidater att undersöka på grund av deras relativt långa varaktighet och antalet anrop.  Andra åtgärder kan ha en längre varaktighet men anropas sällan, så att förbättra dem skulle knappt märkas.  

    ![Panelen Prestanda](media/app-insights-tutorial-performance/performance-blade.png)

3. I diagrammet visas den genomsnittliga varaktigheten för alla åtgärder över tid.  Lägg till de åtgärder du är intresserad av genom att fästa dem vid diagrammet.  Det här visar att det finns några toppar värda att undersöka.  Isolera ytterligare genom att minska tidsfönstret för diagrammet.

    ![Fäståtgärder](media/app-insights-tutorial-performance/pin-operations.png)

4.  Om du klickar på en åtgärd visas dess prestandapanel till höger. Detta visar fördelningen av varaktigheterna för olika förfrågningar.  Användare märker vanligtvis av långsam prestanda på ungefär en halv sekund, så minska fönstret till att visa förfrågningar längre än 500 millisekunder.  

    ![Varaktighetsfördelning](media/app-insights-tutorial-performance/duration-distribution.png)

5.  I det här exemplet kan du se att ett stort antal förfrågningar tagit över en sekund att bearbeta. Du kan se information om den här åtgärden genom att klicka på **Åtgärdsinformation**.

    ![Åtgärdsinformation](media/app-insights-tutorial-performance/operation-details.png)

    > [!NOTE]
    Aktivera [förhandsgranskningsupplevelsen](app-insights-previews.md) Unified details: E2E Transaction Diagnostics (Enhetlig information: E2E-transaktionsdiagnostik) om du vill se telemetriliknande förfrågningar, beroenden, undantag, spårningar, händelser och så vidare på serversidan i en enda helskärmsvy. 

    När den här förhandsgranskningen är aktiverad kan du se hur lång tid som tillbringats i beroendeanrop tillsammans med eventuella fel eller undantag i en enda enhetlig vy. När det gäller transaktioner mellan olika komponenter kan du snabbt se vilken komponent, vilket beroende eller undantag som är rotorsak i Gannt-diagrammet och informationsfönstret. Du kan expandera den undre delen om du vill se tidssekvensen för spårningar eller händelser som samlats in för den valda komponentåtgärden. [Läs mer om den nya upplevelsen](app-insights-transaction-diagnostics.md)  

    ![Transaktionsdiagnostik](media/app-insights-tutorial-performance/e2e-transaction-preview.png)


6.  Den information du har samlat in hittills bekräftar bara att programmet har långsam prestanda, men den är inte till så stor nytta när det gäller grundorsaken.  I **Profiler** kan du få hjälp med det här genom att du ser den faktiska kod som körts för åtgärden och hur lång tid som krävdes för varje steg. Vissa åtgärder kanske inte har någon spårning eftersom Profiler körs med jämna mellanrum.  Med tiden bör fler åtgärder ha spårningar.  Du startar Profiler för åtgärden genom att klicka på **Profiler traces** (Profiler-spårningar).
5.  I spårningen ser du enskilda händelser för varje åtgärd så att du kan diagnostisera vad som gör att åtgärden tar så lång tid att utföra.  Klicka på något av de översta exemplen som har den längsta varaktigheten.
6.  Klicka på **Show Hot Path** (Visa frekvent sökväg) om du vill framhäva sökvägen till de händelser som mest bidrar till åtgärdens totala varaktighet.  I det här exemplet ser du att det långsammaste anropet kommer från metoden *FabrikamFiberAzureStorage.GetStorageTableData*. Den del som tar mest tid är metoden *CloudTable.CreateIfNotExist*. Om den här raden med kod körs varje gång som funktionen anropas förbrukas onödigt många nätverksanrop och processorresurser. Det bästa sättet att åtgärda koden är att placera den här raden i någon startmetod som bara utförs en gång. 

    ![Profiler-information](media/app-insights-tutorial-performance/profiler-details.png)

7.  **Prestandatipset** längst upp på skärmen underbygger antagandet att den långa varaktigheten beror på väntetider.  Klicka på länken **Väntar** om du vill läsa om att tolka olika typer av händelser.

    ![Prestandatips](media/app-insights-tutorial-performance/performance-tip.png)

8.  För ytterligare analys kan du klicka på **Download .etl trace** (Ladda ned .etl-spårning) och ladda ned spårningen till Visual Studio.

## <a name="use-analytics-data-for-server"></a>Använda analysdata för servern
Application Insights Analytics har ett funktionsrikt frågespråk som gör att du kan analysera alla data som samlas in av Application Insights.  Du kan använda det till att utföra djupanalys på data om förfrågningar och prestanda.

1. Återgå till panelen med åtgärdsinformation och klicka på knappen Analytics.

    ![Knappen Analytics](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Application Insights Analytics öppnas med en fråga för var och en av vyerna i panelen.  Du kan köra dessa frågor som de är eller ändra dem efter dina behov.  Den första frågan visar åtgärdens varaktighet över tid.

    ![Analys](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identifiera långsamma klientåtgärder
Förutom att identifiera serverprocesser att optimera så kan Application Insights även analysera ur ett klientperspektiv.  Detta kan hjälpa dig att identifiera möjliga förbättringar av klientkomponenter och att identifiera problem med olika webbläsare eller platser.

1. Välj **Webbläsare** under **Undersök** för att öppna webbläsarsammanfattningen.  Här ges en visuell översikt över olika telemetrivärden i ditt program ur ett webbläsarperspektiv.

    ![Webbläsarsammanfattning](media/app-insights-tutorial-performance/browser-summary.png)

2.  Bläddra ned till **Vilka är mina långsammaste sidor?**  Här visas en lista med de sidor i ditt program som har tagit längst tid för klienterna att läsa in.  Du kan använda den här informationen till att prioritera de sidor som påverkar användarna mest.
3.  Klicka på någon av sidorna för att öppna panelen **Sidvisning**.  I det här exemplet har sidan **/FabrikamProd** en längre varaktighet än genomsnittet.  I panelen **Sidvisning** visas information om den här sidan, inklusive en uppdelning av olika varaktighetsintervall.

    ![Sidvisning](media/app-insights-tutorial-performance/page-view.png)

4.  Klicka på den längsta varaktigheten om du vill se mer information om dessa förfrågningar.  Klicka på enskilda förfrågningar om du vill visa information om klienten som begärde sidan, inklusive typ av webbläsare och plats.  Den här informationen kan hjälpa dig att avgöra om det finns prestandaproblem för specifika typer av klienter.

    ![Information om förfrågan](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Använda analysdata för klienten
Precis som de data som samlas in kring serverprestanda så gör Application Insights alla klientdata tillgängliga för djupanalys med Analytics.

1. Återgå till webbläsarsammanfattningen och klicka på ikonen Analytics.

    ![Ikonen Analytics](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Application Insights Analytics öppnas med en fråga för var och en av vyerna i panelen. Den första frågan visar varaktigheten för olika sidvisningar över tid.

    ![Analys](media/app-insights-tutorial-performance/client-analytics.png)

3.  Smart Diagnostics är en funktion i Application Insights Analytics som identifierar unika mönster i data.  När du klickar på punkten Smart Diagnostics i linjediagrammet körs samma fråga utan de poster som orsakade avvikelsen.  Information om de posterna visas i frågans kommentarsavsnitt så att du kan identifiera egenskaperna för de sidvisningar som orsakar den långa varaktigheten.

    ![Smart Diagnostics](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du identifierar körningsundantag går du vidare till nästa självstudie, där du får lära dig hur du skapar aviseringar som skickas när fel inträffar.

> [!div class="nextstepaction"]
> [Aviseringar om programmets hälsotillstånd](app-insights-tutorial-alert.md)
