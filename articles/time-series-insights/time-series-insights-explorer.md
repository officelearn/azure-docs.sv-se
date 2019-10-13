---
title: Utforska data med hjälp av Azure Time Series Insights Explorer | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure Time Series Insights Explorer i webbläsaren för att snabbt se en global vy över dina Big data och verifiera din IoT-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: aa04b2508f89b9658d904624e884bf9133caf3b7
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299528"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights Explorer

I den här artikeln beskrivs de funktioner och alternativ som är allmänt tillgängliga för den Azure Time Series Insights [Explorer-webbappen](https://insights.timeseries.azure.com/). Time Series Insights Explorer visar de kraftfulla funktionerna för data visualisering som tillhandahålls av tjänsten och kan nås i din egen miljö.

Azure Time Series Insights är en fullständigt hanterad tjänst för analys, lagring och visualisering som gör det enkelt att utforska och analysera miljarder IoT-händelser samtidigt. Du får en global vy över dina data, vilket gör att du snabbt kan verifiera din IoT-lösning och undvika kostsamma drift stopp för verksamhets kritiska enheter. Du kan identifiera dolda trender, upptäcka avvikelser och utföra rotor Saks analyser i nära real tid. Time Series Insights Explorer är för närvarande en offentlig för hands version.

> [!TIP]
> En guidad rundtur i demonstrations miljön finns i snabb starten för [Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Lär dig mer om att köra frågor mot data med hjälp av Time Series Insights Explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Se föregående video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"komma igång med Time Series Insights med hjälp av en Azure IoT Solution Accelerator".</a>

## <a name="prerequisites"></a>Krav

Innan du kan använda Time Series Insights Explorer måste du:

- Skapa en Time Series Insightss miljö. Mer information finns i [så här kommer du igång med Time Series Insights](./time-series-insights-get-started.md).
- [Ge åtkomst](time-series-insights-data-access.md) till ditt konto i-miljön.
- Lägg till en händelse källa för [IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md) eller [Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md) .

## <a name="explore-and-query-data"></a>Utforska och fråga efter data

I minuter när du ansluter din händelse källa till din Time Series Insights-miljö kan du utforska och fråga dina tids serie data.

1. Starta genom att öppna [Time Series Insights Explorer](https://insights.timeseries.azure.com/) i webbläsaren. Välj en miljö på vänster sida i fönstret. Alla miljöer som du har åtkomst till visas i alfabetisk ordning.

1. När du har valt en miljö använder du antingen **från** och **till** konfigurationer högst upp eller klickar och drar över det tidsintervall som du vill använda. Välj förstorings glaset i det övre högra hörnet eller högerklicka på det valda TimeSpan och välj **Sök**.

1. Du kan också uppdatera tillgänglighet automatiskt varje minut genom att välja knappen **Automatisk på** . Knappen **automatiskt på** gäller bara tillgänglighets diagrammet, inte innehållet i huvud visualiseringen.

1. Azure Cloud-ikonen tar dig till din miljö i Azure Portal.

   [![Time Series Insights-miljö](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Därefter ser du ett diagram som visar antalet händelser under det valda tidsintervallet. Här har du ett antal kontroller:

    - **Panelen villkors redigerare**: term området är den plats där du frågar din miljö. Den finns på vänster sida av skärmen:
      - **Mått**: den här List rutan visar alla numeriska kolumner (**dubbla**tal).
      - **Dela efter**: den här List rutan visar kategoriska kolumner (**strängar**).
      - Du kan aktivera steg-interpolation, Visa minsta och högsta och justera y-axeln från kontroll panelen bredvid **mått**. Du kan också justera om data som visas är antal, medelvärde eller summan av data.
      - Du kan lägga till upp till fem villkor för att visa på samma x-axel. Använd **kopierings** knappen för att lägga till ytterligare en term, eller Välj **Lägg** till för att lägga till en ny term.

        [![Terms redigerings panel](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Predikat**: Använd predikatet för att snabbt filtrera händelser med hjälp av en uppsättning operander som anges i följande tabell. Om du utför en sökning genom att välja eller klicka på, uppdateras predikatet automatiskt utifrån sökningen. Operands typer som stöds är:

         |Åtgärd  |Typer som stöds  |Anteckningar  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Double, DateTime, TimeSpan       |         |
         |`=`, `!=`, `<>`     | Sträng, bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | Sträng, bool, Double, DateTime, TimeSpan, NULL        |  Alla operander ska vara av samma typ eller vara NULL-konstant.        |
         |HADE     | Sträng        |  Endast strängar med konstanta strängar är tillåtna på höger sida. En tom sträng och NULL är inte tillåtet.       |

      - **Exempel på frågor**

         [![Example frågor](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. Du kan använda Slider-verktyget för **intervall storlek** för att zooma in och ut ur intervall under samma TimeSpan. Skjutreglaget ger en mer exakt kontroll av förflyttningen mellan stora sektorer, som visar jämna trender nedåt till sektorer som är små som i millisekunder, vilket gör att du kan se detaljerade, högupplösta klipp av dina data. Skjutreglagets standard start punkt anges som den mest optimala vyn av data från ditt val för att balansera upplösning, frågans hastighet och granularitet.

1. Verktyget **tids pensel** är det enkelt att navigera från ett TimeSpan till ett annat.

1. Använd **Spara** -kommandot för att spara din aktuella fråga och dela den med andra användare av miljön. När du använder **Open**kan du se alla dina sparade frågor och alla delade frågor till andra användare i miljöer som du har åtkomst till.

   [![Queries](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Visualisera data

1. Använd verktyget **perspektiv vy** för en samtidig visning av upp till fyra unika frågor. Knappen **perspektivvy** visas i det övre högra hörnet i diagrammet.

   [![Perspective vy](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Visa ett diagram för att visuellt utforska dina data och Använd **diagram** verktygen:

    - **Välj** eller **Klicka på** ett visst tidsintervall eller en enskild data serie.
    - I ett TimeSpan-val kan du zooma eller utforska händelser.
    - I en data serie kan du dela serien efter en annan kolumn, lägga till serien som en ny term, Visa endast den valda serien, utesluta den valda serien, pinga serien eller utforska händelser från den valda serien.
    - I filter ytan till vänster i diagrammet kan du se alla visade data serier och sortera efter värde eller namn. Du kan också Visa alla data serier eller fästa eller ej fästa serier. Du kan välja en enskild data serie och dela serien efter en annan kolumn, lägga till serien som en ny term, Visa endast den markerade serien, ta bort den markerade serien, fästa serien eller utforska händelser från den valda serien.
    - När du visar flera villkor samtidigt kan du stapla, avstapla, se ytterligare data om en data serie och använda samma y-axel för alla termer. Använd knapparna i det övre högra hörnet i diagrammet.

    [![Chart-verktyget](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Använd **termisk karta** för att snabbt hitta unika eller avvikande data serier i en specifik fråga. Endast en sökterm kan visualiseras som en termisk karta.

    [![Heatmap](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. När du utforskar händelser genom att markera eller högerklicka på panelen **händelser** blir panelen händelser tillgänglig. Här kan du se alla dina obehandlade händelser och exportera dina händelser som JSON-eller CSV-filer. Time Series Insights lagrar alla rå data.

    [![Events](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Välj fliken **statistik** när du har utforskat händelser för att exponera mönster och kolumn statistik.

    - **Mönster**: den här funktionen utvärderar de mest statistiskt signifikanta mönstren i ett valt data område. Du behöver inte titta på tusentals händelser för att förstå vilka mönster som kräver mest tid och energi. Med Time Series Insights kan du gå direkt till dessa statistiskt betydande mönster för att fortsätta utföra en analys. Den här funktionen är också användbar för obduktions undersökningar i historiska data.
    - **Kolumn statistik**: kolumn statistik innehåller diagram och tabeller som avbryter data från varje kolumn i den valda data serien under det valda tidsintervallet.

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Nu har du sett de olika funktioner och alternativ som är tillgängliga i Time Series Insights Explorer-webbappen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [diagnostiserar och löser problem](time-series-insights-diagnose-and-solve-problems.md) i din Time Series Insightss miljö.

- Ta den guidade guidade visningen av [Azure Time Series Insights](time-series-quickstart.md) .
