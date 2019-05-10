---
title: Utforska data med hjälp av Azure Time Series Insights explorer | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure Time Series Insights-Utforskaren i webbläsaren för att snabbt se en global vy över dina stordata och verifiera din IoT-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 0f22a0245d002b94d9fc0004214c37944350e262
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412975"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights explorer

Den här artikeln beskriver funktionerna och alternativ i allmänhet tillgänglighet för Azure Time Series Insights [explorer webbapp](https://insights.timeseries.azure.com/). Time Series Insights explorer visar kraftfulla visualiseringsfunktioner tillhandahålls av tjänsten och kan nås i din egen miljö.

Azure Time Series Insights är en fullständigt hanterad tjänst för analys, lagring och visualisering som gör det enkelt att utforska och analysera miljarder IoT-händelser samtidigt. Det ger dig en global vy över dina data, vilket gör att du snabbt verifiera din IoT-lösning och undvika kostsamma avbrott på verksamhetskritiska enheter. Du kan identifiera dolda trender, upptäcka avvikelser och utföra rotorsaksanalyser i nära realtid. Time Series Insights explorer är för närvarande i offentlig förhandsversion.

> [!TIP]
> En guidad visning via demonstrationsmiljö läsa den [Azure Time Series Insights Quickstart](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-using-the-time-series-insights-explorer-br"></a>Läs mer om att köra frågor mot data med hjälp av Time Series Insights explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Se föregående videon <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">”komma igång med TSI med en Azure IoT-lösning Accelerator”</a>.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan använda Time Series Insights explorer, måste du:

- Skapa en Time Series Insights-miljö. Mer information finns i [hur du kommer igång med Time Series Insights](./time-series-insights-get-started.md).
- [Ge åtkomst](time-series-insights-data-access.md) till ditt konto i miljön.
- Lägg till en [IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md) eller [Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md) händelsekälla till den.

## <a name="explore-and-query-data"></a>Utforska och fråga efter data

Inom några minuter för att ansluta din händelsekälla till Time Series Insights-miljön kan du utforska och fråga din time series-data.

1. Börja genom att öppna den [Time Series Insights explorer](https://insights.timeseries.azure.com/) i din webbläsare och välj en miljö på vänster sida av fönstret. Alla miljöer som du har åtkomst till visas i alfabetisk ordning.

1. När du väljer en miljö måste antingen använda den **FROM** och **till** konfigurationer överst, eller klicka och dra över din önskade tidsintervall.  Klicka på förstoringsglaset längst upp till höger, eller högerklicka på över valda timespan och välj **Search**.  

1. Du kan också uppdatera tillgänglighet automatiskt varje minut genom att välja den **automatiskt på** knappen. Den **automatiskt på** knappen gäller endast för Tillgänglighetsdiagrammet, inte innehållet i den huvudsakliga visualiseringen.

1. Observera att Azure-molnet-ikonen tar dig till din miljö i Azure-portalen.

   [![Time Series Insights-miljö](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Nu visas ett diagram som visar en uppräkning av alla händelser under det valda tidsintervallet.  Här har du ett antal kontroller:

    **Villkor redigeraren Panel**:  Termen utrymmet är där du fråga din miljö.  Det visar sig till vänster på skärmen:
      - **Måttet**:  Detta nedrullningsbar listruta visar alla numeriska kolumner (**Double-värden**)
      - **Dela efter**: Den här nedrullningsbara visar kategoriska kolumner (**strängar**)
      - Du kan aktivera stegvis interpolering, visa lägsta och högsta och justera y-axeln från Kontrollpanelen nästa för att mäta.  Du kan dessutom justera om data som visas är ett antal, genomsnitt eller summan av data.
      - Du kan lägga till upp till fem villkoren för att visa på samma x-axeln.  Använd den **kopiera ned** knappen för att lägga till ytterligare en term eller på den **Lägg till** knappen för att lägga till ett nytt villkor.

        [![Redigeraren termpanel](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predikatet**:  Predikatet kan du snabbt filtrera händelser med hjälp av uppsättningen operander som anges nedan. Om du gör en sökning genom att välja/att klicka på predikat som automatiskt uppdateras baserat på sökningen. Operandtyperna som stöds är:

         |Åtgärd  |Typer som stöds  |Anteckningar  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | Sträng, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | Sträng, Bool, Double, DateTime, TimeSpan, NULL        |  Alla operander bör vara av samma typ eller vara konstanten NULL.        |
         |HAS     | String        |  Endast konstant stränglitteraler tillåts på höger sida. Tom sträng och NULL är inte tillåtna.       |

      - **Exempel på frågor**

         [![Exempelfrågor](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Den **Intervallstorlek** skjutreglaget gör att du kan zooma in och ut intervall över samma tidsintervall.  Detta ger mer exakt kontroll över flödet mellan stora sektorer tid som visar smooth trender ned till segment som är så litet som Millisekunden, så att du kan se detaljerad, högupplösta rabatter för dina data. Skjutreglagets standard startpunkt som har angetts som den mest optimala vy över data från markeringen. belastningsutjämning upplösning, fråga hastighet och granularitet.

1. Den **tid pensel** verktyget gör det enkelt att navigera från ett tidsspann till en annan, placera intuitiv UX främst och i led för smidig flytt mellan tidsintervall.

1. Den **spara** kommandot kan du spara din aktuella frågan och aktivera den för att dela med andra användare av miljön. Med hjälp av **öppna**, du kan se alla dina sparade frågor och alla delade frågor för andra användare i miljöer som du har åtkomst till.

   [![Frågor](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualisera data

1. Den **perspektiv visa** verktyget ger en samtidig vy av upp till fyra unika frågor. Du hittar knappen Visa perspektiv i det övre högra hörnet av diagrammet.  

   [![Visa perspektiv](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Den **diagram** kan du utforska dina data visuellt. Diagramverktyg är:

    - **Välj/Klicka**, vilket gör att ett val för ett specifikt tidsintervall eller en dataserie.  
    - Sträcker sig över val inom en tid kan du zooma eller utforska händelser.  
    - I en serie, kan du dela serien efter en annan kolumn, Lägg till serien som en ny term, visa endast valda serie, undanta den markerade serien, pinga serien eller utforska händelser från den markerade serien.
    - I området för filter till vänster om diagrammet, du se alla data som visas serier och ändra ordningen på värde eller namn, visa alla dataserier eller alla fästa eller ej fästa serier.  Du kan också markera en dataserie och dela serien efter en annan kolumn, Lägg till serien som en ny term, visa endast valda serie, undanta den markerade serien, fästa serien eller utforska händelser från den markerade serien.
    - När du visar flera villkor samtidigt kan du stack, avgruppera, se ytterligare information om en serie och använder samma y-axeln över alla villkoren med hjälp av knapparna i det övre högra hörnet av diagrammet.

    [![Diagramverktyget](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Den **termisk karta** kan användas för att snabbt upptäcka unikt eller avvikande dataserien i en viss fråga. Endast en sökterm kan visualiseras som en termisk karta.

    [![Termisk karta](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. **Händelser**:  När du väljer utforska händelser när du väljer eller högerklicka ovanför panelen Händelser är tillgänglig.  Här kan du se alla dina rådata händelser och exportera händelser som JSON- eller CSV-filer. Time Series Insights lagrar alla rådata.

    [![händelser](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Klicka på den **STATS** fliken efter utforska händelser om du vill exponera mönster och kolumnstatistik.  

    - **Mönster**: den här funktionen proaktivt mest statistiskt signifikanta mönster i en region för valda data. Detta frigör du slipper att titta på händelser för att förstå vilka mönster garanterar mest tid och energi. Dessutom kan Time Series Insights du hoppa direkt till dessa statistiskt signifikanta mönster för att fortsätta utföra en analys. Den här funktionen är också bra för utvärdering undersökningar dina historiska data.

    - **Kolumnstatistik**:  Kolumnstatistik ger diagram och tabeller som dela upp data från varje kolumn i den markerade dataserien över det valda tidsintervallet.  

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Nu har du sett de olika funktioner och alternativ som är tillgängliga i Time Series Insights explorer webbapp.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [diagnostisera och lösa problem](time-series-insights-diagnose-and-solve-problems.md) i din Time Series Insights-miljö.

- Ta den guidade [Azure Time Series Insights Quickstart](time-series-quickstart.md) tour.
