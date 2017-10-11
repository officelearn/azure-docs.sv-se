---
title: "Diagnostik för web app prestanda förändringar i Azure Application Insights för smartkort | Microsoft Docs"
description: "Automatisk diagnos av toppar eller stegen i prestanda telemetri från ditt webbprogram."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: cfreeman
ms.openlocfilehash: 5e53bc714d89bf6204681349e7890e0b8fbc7046
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>Diagnostisera plötsliga förändringar i din app telemetri

*Den här funktionen är i förhandsgranskningen.*

Diagnostisera plötsliga ändringar i ditt webbprogram prestanda eller med en enda klickning! Smart diagnostik-funktionen är tillgänglig när du skapar en diagrammets i [Analytics](app-insights-analytics.md) i [Programinsikter](app-insights-overview.md). Då det finns en ovanlig ändring från resultaten, till exempel en topp eller en dip trend identifierar Smart diagnostik ett mönster av dimensioner och relaterade värden som kan förklara ändringen. Detta hjälper dig att diagnostisera problemet snabbt. 

Smart diagnostik i det här exemplet har identifierat ett mönster av egenskapsvärden som är associerade med ändringen och visar skillnaden mellan resultat med och utan att mönster:

![exempel analytics diagnostik resultat](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>Diagnostisera dataändringar

1.  Kör en fråga i Analytics och återges som en diagrammets. 
2.  Klicka på helst markerade belastning om det finns en.
 
    ![belastning punkt](./media/app-insights-analytics-diagnostics/peak.png)

    Diagnostik tar några sekunder att identifiera ett mönster.

3. Fliken Diagnostikresultat visar ett mönster som kan förklara data-avvikelse.

    ![Resultat](./media/app-insights-analytics-diagnostics/result.png)
 
    Texten som visar de värden som visas för att korrelera med SKIFT. I det här exemplet associeras det med en viss begäran och en viss webbläsarversion.

    Observera också två komponenter i diagrammet med filtret true och false. Komponenten FALSKT visar en oförändrad trend. Med andra ord påverkas inte i resultaten telemetri om vi undantar problematiska kombinationen av dimensioner som har identifierats av diagnostik. Däremot visas resultat inom den kombinationen en dramatisk förändring i det markerade området undersökning. Det visar att Diagnostics har hittat en kombination av egenskaper som beskriver ändringen.

4.  Om mönstret är komplex, behöver du hovrar över **visa alla** att visa dimensioner.

    ![visa alla](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  Om diagnostik hittar några betydande mönster för att meddela om, visas sidan inga resultat. Nu kan du ändra din fråga. Du kan till exempel begränsa tidsintervallet och diskretisering i Analytics-fråga för ytterligare analys och potentiellt bättre resultat.

Tillsammans med vetskapen om att en viss sida för webbplatsen finns ett fel på en viss webbläsare, kan du nu gå direkt till sidan problem och undersök nyligen gjorda ändringar.

## <a name="try-the-demo"></a>Prova demonstrationen

[Klicka här om du vill se en demonstration](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H) på exempeldata.

## <a name="how-it-works"></a>Hur det fungerar

Smart diagnostik använder en avancerad oövervakade maskininlärningsalgoritmen baserat på de [DiffPatterns](app-insights-analytics-reference.md) igen. Det ser ut för kandidat mönster som kan förklara data ändras. Det analyser effekten av varje kandidat på måttet och visar mönstret som bäst korrelerar med ändringen.

## <a name="no-diagnostic-points"></a>Inga diagnostiska poäng?

Smart diagnostik fungerar endast när följande villkor uppfylls:

 * Smart diagnostik-inställningen är påslagen. Titta under inställningsikonen i Analytics.
 * Alternativet Smart diagnostik i inställningar är markerad. 
 * Tidsaxeln: på x-axeln i diagrammet måste vara av typen `datetime`.
 * Linje eller område: diagnostik fungerar endast dessa typer av diagram. Använd `| render timechart` eller `| render areachart` i slutet av din fråga, eller Välj raden eller ytdiagram från listrutan Väljaren.
 * Avvikelse: Det måste finnas en betydande avvikelse i data.
 * Tillräckligt pekar på analysera.
 * Fler än en sammanfatta-sats i frågan.
 * Inga projekt-sats som innehåller en definition av namn före sammanfatta-sats.

 
 ## <a name="related-articles"></a>Relaterade artiklar

 * [Analytics-självstudier](app-insights-analytics-tour.md)
 * [Identifiering för smartkort](app-insights-proactive-diagnostics.md) automatiskt aviserar prestandaproblem.