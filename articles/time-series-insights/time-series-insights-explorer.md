---
title: Utforska data med hjälp av Azure Time Series Insights explorer | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure Time Series Insights-Utforskaren i webbläsaren för att snabbt se en global vy över dina stordata och verifiera din IoT-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: cbb6d75b2ed6b73ce7e3421596520f6a3f1bf3b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399808"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights explorer

Den här artikeln beskriver funktionerna och alternativ i allmänhet tillgänglighet för Azure Time Series Insights [explorer webbapp](https://insights.timeseries.azure.com/). Time Series Insights explorer visar kraftfulla visualiseringsfunktioner tillhandahålls av tjänsten och kan nås i din egen miljö.

Azure Time Series Insights är en fullständigt hanterad tjänst för analys, lagring och visualisering som gör det enkelt att utforska och analysera miljarder IoT-händelser samtidigt. Det ger dig en global vy över dina data, vilket gör att du snabbt verifiera din IoT-lösning och undvika kostsamma avbrott på verksamhetskritiska enheter. Du kan identifiera dolda trender, upptäcka avvikelser och utföra rotorsaksanalyser i nära realtid. Time Series Insights explorer är för närvarande i offentlig förhandsversion.

> [!TIP]
> En guidad visning via demonstrationsmiljö läsa den [Snabbstart för Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Läs mer om att köra frågor mot data med hjälp av Time Series Insights explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Se föregående videon <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">”komma igång med Time Series Insights med hjälp av en Azure IoT-lösningsaccelerator”.</a>

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan använda Time Series Insights explorer, måste du:

- Skapa en Time Series Insights-miljö. Mer information finns i [hur du kommer igång med Time Series Insights](./time-series-insights-get-started.md).
- [Ge åtkomst](time-series-insights-data-access.md) till ditt konto i miljön.
- Lägg till en [IoT-hubb](time-series-insights-how-to-add-an-event-source-iothub.md) eller [händelsehubb](time-series-insights-how-to-add-an-event-source-eventhub.md) händelsekälla till den.

## <a name="explore-and-query-data"></a>Utforska och fråga efter data

Inom några minuter för att ansluta din händelsekälla till Time Series Insights-miljön kan du utforska och fråga din time series-data.

1. Börja genom att öppna den [Time Series Insights explorer](https://insights.timeseries.azure.com/) i webbläsaren. På vänster sida av fönstret, väljer du en miljö. Alla miljöer som du har åtkomst till visas i alfabetisk ordning.

1. När du har valt en miljö kan antingen använda den **från** och **till** konfigurationer överst, eller klicka och dra över tidsintervall som önskas. Välj på förstoringsglaset i det övre högra hörnet, eller högerklicka på den valda timespan och välj **Search**.

1. Du också kan uppdatera tillgänglighet automatiskt varje minut genom att välja den **automatiskt på** knappen. Den **automatiskt på** knappen gäller endast för Tillgänglighetsdiagrammet, inte innehållet i den huvudsakliga visualiseringen.

1. Azure-molnet-ikonen tar dig till din miljö i Azure-portalen.

   [![Time Series Insights-miljö](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Nu visas ett diagram som visar en uppräkning av alla händelser under det valda tidsintervallet. Här har du ett antal kontroller:

    - **Redigeraren termpanel**: Termen utrymmet är där du fråga din miljö. Det hittas på vänster sida av skärmen:
      - **Måttet**: Den här listan visar alla numeriska kolumner (**Double-värden**).
      - **Dela efter**: Den här listan visar kategoriska kolumner (**strängar**).
      - Du kan aktivera stegvis interpolering, visa lägsta och högsta och justera y-axeln från Kontrollpanelen bredvid **mått**. Du kan också justera om data som visas är ett antal, genomsnitt eller summan av data.
      - Du kan lägga till upp till fem villkoren för att visa på samma x-axeln. Använd den **kopiera ned** för att lägga till ytterligare en term eller välj **Lägg till** att lägga till ett nytt villkor.

        [![Redigeraren termpanel](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predikatet**: Använd predikatet för att snabbt filtrera händelser med hjälp av uppsättningen operander som visas i följande tabell. Om du gör en sökning genom att välja eller klicka på predikatet automatiskt uppdateringar baserat på sökningen. Operandtyperna som stöds är:

         |Åtgärd  |Typer som stöds  |Anteckningar  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | Sträng, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | Sträng, Bool, Double, DateTime, TimeSpan, NULL        |  Alla operander bör vara av samma typ eller vara konstanten NULL.        |
         |HAS     | String        |  Endast konstant stränglitteraler tillåts på höger sida. Tom sträng och NULL är inte tillåtna.       |

      - **Exempel på frågor**

         [![Exempelfrågor](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Du kan använda den **Intervallstorlek** skjutreglaget verktyg för att zooma in och ut intervall över samma tidsintervall. Skjutreglaget ger mer exakt kontroll över flödet mellan stora sektorer tid som visar smooth trender ned till segment som är så litet som Millisekunden, där du kan se detaljerad, högupplösta rabatter för dina data. Skjutreglagets standard startpunkt anges som den mest optimala vy av data från ditt val att balansera upplösning, fråga hastighet och granularitet.

1. Den **tid pensel** verktyget gör det enkelt att navigera från en timespan till en annan.

1. Använd den **spara** kommando för att spara din aktuella frågan och dela den med andra användare av miljön. När du använder **öppna**, du kan se alla dina sparade frågor och alla delade frågor för andra användare i miljöer som du har åtkomst till.

   [![Frågor](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualisera data

1. Använd den **perspektiv visa** verktyget för en samtidig vy av upp till fyra unika frågor. Den **perspektiv visa** är knappen i det övre högra hörnet i diagrammet.

   [![Visa perspektiv](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Visa ett diagram för att visuellt Utforska dina data och använda den **diagram** verktyg:

    - **Välj** eller **klickar du på** timespan specifika eller en dataserie.
    - Du kan zooma eller utforska händelser inom en timespan-markering.
    - I en serie, kan du dela serien efter en annan kolumn, Lägg till serien som en ny term, visa endast valda serie, undanta den markerade serien, pinga serien eller utforska händelser från den markerade serien.
    - I området för filter till vänster om diagrammet, kan du se alla data som visas serier och ändra ordningen på värde eller namn. Du kan också visa alla data eller alla fästa eller ej fästa serien. Du kan markera en dataserie och dela serien efter en annan kolumn, Lägg till serien som en ny term, visa endast valda serie, undanta den markerade serien, fästa serien eller utforska händelser från den markerade serien.
    - När du visar flera villkor samtidigt kan du stack, avgruppera, se ytterligare information om en serie och använder samma y-axeln över alla villkor. Använd knapparna i det övre högra hörnet i diagrammet.

    [![Diagramverktyget](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Använd den **termisk karta** att snabbt hitta unikt eller avvikande dataserien i en viss fråga. Endast en sökterm kan visualiseras som en termisk karta.

    [![Termisk karta](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. När du utforskar händelser genom att välja eller högerklicka, **händelser** panelen är tillgänglig. Här kan du se alla dina rådata händelser och exportera händelser som JSON- eller CSV-filer. Time Series Insights lagrar alla rådata.

    [![händelser](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Välj den **STATS** fliken när du utforskar händelser om du vill exponera mönster och kolumnstatistik.

    - **Mönster**: Den här funktionen proaktivt mest statistiskt signifikanta mönster i en region för valda data. Du behöver titta på händelser för att förstå vilka mönster kräver mest tid och energi. Med Time Series Insights kan du gå direkt till dessa statistiskt signifikanta mönster för att fortsätta utföra en analys. Den här funktionen är också bra för utvärdering undersökningar dina historiska data.
    - **Kolumnstatistik**: Kolumnstatistik ger diagram och tabeller som bryter ned data från varje kolumn i den markerade dataserien över det valda tidsintervallet.

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Nu har du sett de olika funktioner och alternativ som är tillgängliga i Time Series Insights explorer webbapp.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [diagnostisera och lösa problem](time-series-insights-diagnose-and-solve-problems.md) i din Time Series Insights-miljö.
- Ta den guidade [Snabbstart för Azure Time Series Insights](time-series-quickstart.md) tour.
