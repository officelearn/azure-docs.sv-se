---
title: "Övervaka prestanda för Azure-webbappar | Microsoft Docs"
description: "Övervakning av programprestanda för Azure-webbappar. Skapa diagram över inläsnings- och svarstider och beroendeinformation och ställ in aviseringar för prestanda."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: b25f3dd41c8dc381258bb42d8802ba4b56e05903
ms.contentlocale: sv-se
ms.lasthandoff: 08/02/2017

---
# <a name="monitor-azure-web-app-performance"></a>Övervaka prestanda för Azure-webbappar
På [Azure Portal](https://portal.azure.com) kan du konfigurera övervakning av programprestanda för dina [Azure-webbappar](../app-service-web/app-service-web-overview.md). [Azure Application Insights](app-insights-overview.md) instrumenterar din app så att den skickar telemetri om sina aktiviteter till Application Insights-tjänsten, där informationen lagras och analyseras. Där kan du använda diagram med mätvärden och sökverktyg för att diagnostisera problem, förbättra prestanda och utvärdera användningen.

## <a name="run-time-or-build-time"></a>Vid körning eller utveckling
Du kan konfigurera övervakning genom att instrumentera appen på något av två sätt:

* **Vid körning** – Du kan välja ett tillägg för prestandaövervakning när din webbapp redan är live. Du behöver inte återskapa eller installera om appen. Du får en standarduppsättning med paket som övervakar svarstider, framgångsfrekvens, undantag, beroenden och så vidare. 
* **Vid utveckling** – Du kan installera ett paket i din app i samband med utvecklingen. Det här alternativet är mer flexibelt. Förutom motsvarande standardpaket kan du skriva kod för att anpassa telemetrin eller skicka din egen telemetri. Du kan logga specifika aktiviteter eller registrera händelser baserat på semantiken för din appdomän. 

## <a name="run-time-instrumentation-with-application-insights"></a>Instrumentering i samband med körning med Application Insights
Om du redan kör en webbapp i Azure har du redan tillgång till viss övervakning: begärande- och felfrekvens. Lägg till Application Insights om du vill få tillgång till mer, till exempel svarstider, övervakning av anrop till beroenden, smart identifiering och det kraftfulla Log Analytics-frågespråket. 

1. **Välj Application Insights** på Azure-kontrollpanelen för din webbapp.
   
    ![Välj Application Insights under Övervakning](./media/app-insights-azure-web-apps/05-extend.png)
   
   * Välj att skapa en ny resurs, såvida du inte redan har angett en Application Insights-resurs för den här appen på ett annat sätt.
2. **Instrumentera din webbapp** när Application Insights har installerats. 
   
    ![Instrumentera din webbapp](./media/app-insights-azure-web-apps/restart-web-app-for-insights.png)

   **Aktivera övervakning på klientsidan** för sidvy och användartelemetri.

   * Välj Inställningar > Programinställningar
   * Under Appinställningar lägger du till ett nytt nyckel/värde-par: 
   
    Nyckel: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Värde:`true`

   * **Spara** inställningarna och **starta om** din app.
3. **Övervaka din app**.  [Utforska data](#explore-the-data).

Senare kan du skapa appen med Application Insights om du vill.

*Hur tar jag bort Application Insights eller växlar till att skicka telemetri till en annan resurs?*

* Öppna kontrollbladet för webbappen i Azure och öppna **Tillägg** under Utvecklingsverktyg. Ta bort Application Insights-tillägget. Välj Application Insights och skapa eller välj önskad resurs under Övervakning.

## <a name="build-the-app-with-application-insights"></a>Skapa appen med Application Insights
Application Insights kan tillhandahålla mer detaljerad telemetri genom installationen av ett SDK i din app. Mer specifikt kan du samla in spårningsloggar, [skriva anpassad telemetri](app-insights-api-custom-events-metrics.md) och få mer detaljerade undantagsrapporter.

1. **I Visual Studio** (2013 uppdatering 2 eller senare) konfigurerar du Application Insights för ditt projekt.

    Högerklicka på webbprojektet och välj **Lägg till > Application Insights** eller **Konfigurera Application Insights**.
   
    ![Högerklicka på webbprojektet och välj Lägg till Application Insights eller Konfigurera Application Insights](./media/app-insights-azure-web-apps/03-add.png)
   
    Om du uppmanas att logga in använder du autentiseringsuppgifterna för ditt Azure-konto.
   
    Åtgärden har två effekter:
   
   1. Skapar en Application Insights-resurs i Azure, där telemetri lagras, analyseras och visas.
   2. Lägger till NuGet-paketet för Application Insights i din kod (om det inte redan finns där) och konfigurerar det att skicka telemetri till Azure-resursen.
2. **Testa telemetrin** genom att köra appen på utvecklingsdatorn (F5).
3. **Publicera appen** till Azure som vanligt. 

*Hur växlar jag till att skicka telemetri till en annan Application Insights-resurs?*

* I Visual Studio högerklickar du på projektet, väljer **Konfigurera Application Insights** och väljer önskad resurs. Du får möjlighet att skapa en ny resurs. Återskapa och distribuera igen.

## <a name="explore-the-data"></a>Utforska data
1. På bladet Application Insights på kontrollpanelen för webbappen ser du Live Metrics, som visar begäranden och fel inom en sekund eller två efter att de inträffat. Detta är praktiskt när du publicera om din app, så att du genast ser eventuella problem.
2. Klicka dig framåt till den fullständiga Application Insights-resursen.

    ![Klicka dig framåt](./media/app-insights-azure-web-apps/view-in-application-insights.png)

    Du kan också gå dit direkt från Azure-resursnavigeringen.

1. Klicka dig igenom valfritt diagram om du vill visa mer information:
   
    ![Klicka på ett diagram på översiktsbladet för Application Insights](./media/app-insights-azure-web-apps/07-dependency.png)
   
    Du kan [anpassa bladen med mätvärden](app-insights-metrics-explorer.md).
2. Klicka dig vidare om du vill visa enskilda händelser och deras egenskaper:
   
    ![Klicka på en händelsetyp för att öppna en sökning som filtrerats baserat på den typen](./media/app-insights-azure-web-apps/08-requests.png)
   
    Observera länken ”...” för att öppna alla egenskaper.
   
    Du kan [anpassa sökningar](app-insights-diagnostic-search.md).

För mer kraftfulla sökningar över din telemetri kan du använda [Log Analytics-frågespråket](app-insights-analytics-tour.md).

## <a name="more-telemetry"></a>Mer telemetri

* [Data om webbsidesinläsning](app-insights-javascript.md)
* [Anpassad telemetri](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg
* [Kör profileraren för din live-app](app-insights-profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – övervaka Azure Functions med Application Insights
* [Aktivera Azure-diagnostik](app-insights-azure-diagnostics.md) så att den skickas till Application Insights.
* [Övervaka mätvärden för tjänstens hälsotillstånd](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) för att se till att tjänsten är tillgänglig och svarar.
* [Få aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) när drifthändelser inträffar eller när mätvärden överskrider ett tröskelvärde.
* Använd [Application Insights för JavaScript-appar och webbsidor](app-insights-javascript.md) för att hämta klienttelemetri från webbläsare som besöker en webbsida.
* [Konfigurera tillgänglighetswebbtester](app-insights-monitor-web-app-availability.md) så att du aviseras om webbplatsen inte fungerar.


