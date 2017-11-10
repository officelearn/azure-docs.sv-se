---
title: "Förstå dina kunder i Azure Application Insights | Microsoft Docs"
description: "Självstudiekurs om hur du använder Azure Application Insights för att förstå hur kunder använder ditt program."
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: db61c300ad82270e59d315fa3372d9e4390c7a21
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Använd Azure Application Insights för att förstå hur kunder använder ditt program

Azure Application Insights samlar in användningsinformation för att hjälpa dig att förstå hur användarna samverkar med ditt program.  Den här självstudiekursen vägleder dig genom de olika resurser som är tillgängliga för att analysera informationen.  Du får lära dig hur du:

> [!div class="checklist"]
> * Analysera information om användare som ansluter till ditt program
> * Använd sessionsinformation för att analysera hur kunder använder ditt program
> * Definiera skorstenar som gör att du kan jämföra din önskade användaraktivitet till deras faktiska aktivitet 
> * Skapa en arbetsbok för att konsolidera visualiseringar och frågor till ett dokument
> * Gruppera liknande användare för att analysera dem tillsammans
> * Lär dig vilka användare returnerar till ditt program
> * Kontrollera hur användare navigerar genom ditt program


## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

- Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling
    - Azure Development
- Hämta och installera den [Visual Studio ögonblicksbild Debugger](http://aka.ms/snapshotdebugger).
- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](app-insights-asp-net.md). 
- [Skicka telemetri från ditt program](app-insights-usage-overview.md#send-telemetry-from-your-app) för att lägga till anpassade händelser/sidvisningar
- Skicka [användarkontext](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context) att spåra en användare gör över tid och fullständigt utnyttja funktionerna för användning.

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>Hämta information om dina användare
Den **användare** panelen tillåter dig att förstå viktig information om användarna i en mängd olika sätt. Du kan använda den här panelen för att förstå information som var användarna ansluter från, information om deras klient och vilka områden i ditt program som de försöker komma åt. 

1. Välj **Programinsikter** och sedan välja din prenumeration.
2. Välj **användare** på menyn.
3. Standardvyn visar antalet unika användare som har anslutit till ditt program under de senaste 24 timmarna.  Du kan ändra tidsramen och ange andra villkor att filtrera den här informationen.

    ![Frågebyggaren](media\app-insights-tutorial-users\QueryBuilder.png)

6. Klicka på den **under** listrutan och ändra tidsramen till 7 dagar.  Detta ökar data som ingår i de olika diagrammen i panelen.

    ![Ändra tidsintervall](media\app-insights-tutorial-users\TimeRange.png)

4. Klicka på den **delning efter** listrutan för att lägga till en uppdelning av en användaregenskap i diagrammet.  Välj **land eller region**.  Diagrammet innehåller samma data, men kan du visa en sammanställning av antalet användare för varje land.

    ![Diagram för land eller Region](media\app-insights-tutorial-users\CountryorRegion.png)

5. Placera markören över olika staplarna i diagrammet och Observera att antalet för varje land återspeglar endast tidsfönster som representeras av verktygsfältet.
6. Ta en titt på den **Insights** kolumnen längst till höger som utföra analyser på användardata.  Detta ger information, till exempel antalet unika sessioner under lång tid och poster med gemensamma egenskaper som utgör betydande av användardata 

    ![Insikter kolumn](media\app-insights-tutorial-users\insights.png)


## <a name="analyze-user-sessions"></a>Analysera användarsessioner
Den **sessioner** panelen liknar den **användare** panelen.  Där **användare** hjälper dig att förstå information om de användare som har åtkomst till ditt program **sessioner** hjälper dig att förstå hur dessa användare har använt ditt program.  

1. Välj **sessioner** på menyn.
2. Ta en titt på diagrammet och Observera att du har samma alternativ för att filtrera och dela upp data som i den **användare** panelen.

    ![Frågebyggaren sessioner](media\app-insights-tutorial-users\SessionsBuilder.png)

3. Den **exempel på de här sessionerna** fönstret till höger visas sessioner som innehåller ett stort antal händelser.  Detta är intressanta sessioner att analysera.

    ![Exempel på dessa sessioner](media\app-insights-tutorial-users\SessionsSample.png)

4. Klicka på någon av sessioner att visa dess **Session tidslinjen**, vilket visar varje åtgärd i sessioner.  Detta kan hjälpa dig att identifiera information, till exempel sessioner med ett stort antal undantag.

    ![Tidslinje för sessioner](media\app-insights-tutorial-users\SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Gruppera liknande användare
En **kommittén** är en uppsättning användare groupd på liknande egenskaper.  Du kan använda kohorter att filtrera data i andra paneler så att du kan analysera olika grupper av användare.  Du kanske vill analysera endast användare som slutfört ett inköp.

1.  Välj **kohorter** på menyn.
2.  Klicka på **ny** att skapa en ny kommittén.
3.  Välj den **som används för** listrutan och välj en åtgärd.  Endast användare som utförde åtgärden inom tidsfönstret i rapporten inkluderas.

    ![Kommittén som utföra angivna åtgärder](media\app-insights-tutorial-users\CohortsDropdown.png)

4.  Välj **användare** på menyn.
5.  I den **visa** listrutan, Välj kommittén som du just skapade.  Data för diagrammet är begränsad till dessa användare.

    ![Kommittén i Verktyg för användare](media\app-insights-tutorial-users\UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Jämför önskade aktiviteten verkligheten
Medan panelerna tidigare fokuserar på vad användare av ditt program gjorde, **skorstenar** fokusera på vad du vill att användarna ska göra.  Ett trattdiagram representerar en uppsättning steg i ditt program och procentandelen av användare som flyttas mellan olika steg.  Du kan till exempel skapa en Trattens som mäter procentandelen av användare som ansluter till ditt program som söker produkten.  Du kan sedan se procentandelen av användare och lägga till den i en kundvagn och procentandelen personer slutför ett inköp.

1. Välj **skorstenar** i menyn och klicka sedan på **ny**. 

    ![](media\app-insights-tutorial-users\funnelsnew.png)

2. Ange en **tratt namnet**.
3. Skapa en Trattens med minst två steg genom att välja en åtgärd för varje steg.  En lista över åtgärder bygger från användningsdata som samlas in av Application Insights.

    ![](media\app-insights-tutorial-users\funnelsedit.png)

4. Klicka på **spara** att spara tratten och visa sedan resultaten.  I fönstret till höger om tratten visas de vanligaste händelserna före den första aktiviteten och efter den senaste aktiviteten för att förstå användaren beteenden runt viss sekvens.

    ![](media\app-insights-tutorial-users\funnelsright.png)


## <a name="learn-which-customers-return"></a>Lär dig vilka kunder returnera
**Kvarhållning** hjälper dig att förstå vilka användare kommer tillbaka till ditt program.  

1. Välj **kvarhållning** på menyn.
2. Som standard innehåller informationen analyseras användare utföra alla åtgärder som då returneras om du vill utföra alla åtgärder.  Du kan ändra det här filtret till alla inkludera, till exempel användare som returneras när du har slutfört ett inköp.

    ![](media\app-insights-tutorial-users\retentionquery.png)

3. Returnerar användare som matchar villkor som visas i en grafisk och tabell för olika tidsvaraktigheter.  Det vanliga mönstret är för en gradvis minskning av returnerar användare över tid.  En plötslig släpp från en tidsperiod till nästa kan generera ett problem. 

    ![](media\app-insights-tutorial-users\retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analysera användarnavigering
En **användaren flödet** visualizes hur användarna navigera mellan sidor och funktioner i programmet.  Det hjälper dig besvara frågor som där användarna vanligtvis flyttar från en viss sida, hur de vanligtvis avsluta programmet och om det finns några åtgärder som regelbundet upprepas.

1.  Välj **användaren flöden** på menyn.
2.  Klicka på **ny** skapar du en ny användare och klicka sedan på **redigera** att redigera detaljerna.
3.  Öka den **tidsintervall** till 7 dagar och välj sedan en inledande händelse.  Flödet spårar användarsessioner som börjar med händelsen.

    ![](media\app-insights-tutorial-users\flowsedit.png)

4.  Användaren flödet visas, och du kan se olika användare sökvägar och sin session räknar.  Blå linjer anger en åtgärd som användaren utföras efter den aktuella åtgärden.  En röd linje anger slutet av sessionen.

    ![](media\app-insights-tutorial-users\flows.png)

5.  Om du vill ta bort en händelse från flödet, klickar du på den **x** i hörnet av åtgärden och klicka sedan på **skapa diagram**.  Diagrammet ritas med alla förekomster av händelsen tas bort.  Klicka på **redigera** att se att händelsen läggs nu till **exkluderade händelser**.

    ![](media\app-insights-tutorial-users\flowsexclude.png)

## <a name="consolidate-usage-data"></a>Konsolidera användningsdata
**Arbetsböcker** kombinera datavisualiseringar, Analytics-frågor och text i interaktiva dokument.  Du kan använda arbetsböcker för att gruppera vanliga användningsinformation, konsolidera information från en viss händelse eller rapportera till din grupp på användning av ditt program.

1.  Välj **arbetsböcker** på menyn.
2.  Klicka på **ny** att skapa en ny arbetsbok.
3.  En fråga har redan angetts som inkluderar alla användardata på den sista dagen som visas som ett stapeldiagram.  Du kan använda den här frågan, manuellt redigera den, eller klicka på **exempel frågor** att välja från andra användbara frågor.

    ![](media\app-insights-tutorial-users\samplequeries.png)

4.  Klicka på **är klar med redigeringen**.
5.  Klicka på **redigera** i det övre fönstret Redigera texten längst upp i arbetsboken.  Detta är formaterad med markdown.

    ![](media\app-insights-tutorial-users\markdown.png)

6.  Klicka på **lägga till användare** att lägga till ett diagram med användarinformation.  Redigera information om diagrammet om du vill använda och klicka sedan på **är klar med redigeringen** och spara den.


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du analyserar dina användare går du vidare till nästa kurs att lära dig skapa anpassade instrumentpaneler som kombinerar den här informationen med annan användbar information om ditt program.

> [!div class="nextstepaction"]
> [Skapa anpassade instrumentpaneler](app-insights-tutorial-dashboards.md)
