---
title: Övervaka prestanda för Azure-webbappar | Microsoft Docs
description: Övervakning av programprestanda för Azure-webbappar. Skapa diagram över inläsnings- och svarstider och beroendeinformation och ställ in aviseringar för prestanda.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: 00af4cc67dc7490727af46e12c387fb0f147371d
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339849"
---
# <a name="monitor-azure-web-app-performance"></a>Övervaka prestanda för Azure-webbappar
På [Azure Portal](https://portal.azure.com) kan du konfigurera övervakning av programprestanda för dina [Azure-webbappar](../app-service/app-service-web-overview.md). [Azure Application Insights](app-insights-overview.md) instrumenterar din app så att den skickar telemetri om sina aktiviteter till Application Insights-tjänsten, där informationen lagras och analyseras. Där kan du använda diagram med mätvärden och sökverktyg för att diagnostisera problem, förbättra prestanda och utvärdera användningen.

## <a name="run-time-or-build-time"></a>Vid körning eller utveckling
Du kan konfigurera övervakning genom att instrumentera appen på något av två sätt:

* **Vid körning** – Du kan välja ett tillägg för prestandaövervakning när din webbapp redan är live. Du behöver inte återskapa eller installera om appen. Du får en standarduppsättning med paket som övervakar svarstider, framgångsfrekvens, undantag, beroenden och så vidare. 
* **Vid utveckling** – Du kan installera ett paket i din app i samband med utvecklingen. Det här alternativet är mer flexibelt. Förutom motsvarande standardpaket kan du skriva kod för att anpassa telemetrin eller skicka din egen telemetri. Du kan logga specifika aktiviteter eller registrera händelser baserat på semantiken för din appdomän. 

## <a name="run-time-instrumentation-with-application-insights"></a>Instrumentering i samband med körning med Application Insights
Om du redan kör en webbapp i Azure har du redan tillgång till viss övervakning: begärande- och felfrekvens. Lägg till Application Insights om du vill få tillgång till mer, till exempel svarstider, övervakning av anrop till beroenden, smart identifiering och det kraftfulla Log Analytics-frågespråket. 

1. **Välj Application Insights** på Azure-kontrollpanelen för din webbapp.

    ![Välj Application Insights under inställningar](./media/app-insights-azure-web-apps/settings-app-insights.png)

   * Välja att skapa en ny resurs, såvida inte du redan har konfigurerat en Application Insights-resurs för det här programmet. 

    > [!NOTE]
    > När du klickar på **OK** att skapa den nya resursen som du kommer att uppmanas att **tillämpa övervakningsinställningar**. Att välja **Fortsätt** länkar nya Application Insights-resursen till din webbapp, gör du så kommer också **utlösa en omstart av webbappen**. 

    ![Instrumentera din webbapp](./media/app-insights-azure-web-apps/create-resource.png)

2. Du kan välja hur du vill att application insights för att samla in data per plattform för ditt program när du har angett vilken resurs som ska användas.

    ![Välj alternativ per plattform](./media/app-insights-azure-web-apps/choose-options.png)

3. **Instrumentera din webbapp** när Application Insights har installerats.

   **Aktivera övervakning på klientsidan** för sidvy och användartelemetri.

   * Välj Inställningar > Programinställningar
   * Under Appinställningar lägger du till ett nytt nyckel/värde-par:

    Nyckel: `APPINSIGHTS_JAVASCRIPT_ENABLED`

    Värde:`true`
   * **Spara** inställningarna och **starta om** din app.
4. Utforska övervakningsdata för din app genom att välja **inställningar** > **Programinsikter** > **visa mer i Application Insights**.

Senare kan du skapa appen med Application Insights om du vill.

*Hur tar jag bort Application Insights eller växlar till att skicka telemetri till en annan resurs?*

* Öppna kontrollbladet för webbappen i Azure, och under inställningar, öppna **Application Insights**. Du kan stänga av Application Insights genom att klicka på **inaktivera** överst, eller välj en ny resurs i den **ändra din resurs** avsnittet.

## <a name="build-the-app-with-application-insights"></a>Skapa appen med Application Insights
Application Insights kan tillhandahålla mer detaljerad telemetri genom installationen av ett SDK i din app. Mer specifikt kan du samla in spårningsloggar, [skriva anpassad telemetri](app-insights-api-custom-events-metrics.md) och få mer detaljerade undantagsrapporter.

1. **I Visual Studio** (2013 uppdatering 2 eller senare) konfigurerar du Application Insights för ditt projekt.

    Högerklicka på webbprojektet och välj **Lägg till > Application Insights** eller **projekt** > **Programinsikter**  >  **Konfigurera Application Insights**.

    ![Högerklicka på webbprojektet och välj Lägg till Application Insights eller Konfigurera Application Insights](./media/app-insights-azure-web-apps/03-add.png)

    Om du uppmanas att logga in använder du autentiseringsuppgifterna för ditt Azure-konto.

    Åtgärden har två effekter:

   1. Skapar en Application Insights-resurs i Azure, där telemetri lagras, analyseras och visas.
   2. Lägger till NuGet-paketet för Application Insights i din kod (om det inte redan finns där) och konfigurerar det att skicka telemetri till Azure-resursen.
2. **Testa telemetrin** genom att köra appen på utvecklingsdatorn (F5).
3. **Publicera appen** till Azure som vanligt. 

*Hur växlar jag till att skicka telemetri till en annan Application Insights-resurs?*

* I Visual Studio högerklickar du på projektet, väljer **Konfigurera Application Insights** och väljer önskad resurs. Du får möjlighet att skapa en ny resurs. Återskapa och distribuera igen.

## <a name="more-telemetry"></a>Mer telemetri

* [Data om webbsidesinläsning](app-insights-javascript.md)
* [Anpassad telemetri](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg
* [Kör profileraren för din live-app](app-insights-profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – övervaka Azure Functions med Application Insights
* [Aktivera Azure-diagnostik](../azure-monitor/platform/diagnostics-extension-to-application-insights.md) så att den skickas till Application Insights.
* [Övervaka mätvärden för tjänstens hälsotillstånd](../azure-monitor/platform/data-collection.md) för att se till att tjänsten är tillgänglig och svarar.
* [Få aviseringar](../azure-monitor/platform/alerts-overview.md) när drifthändelser inträffar eller när mätvärden överskrider ett tröskelvärde.
* Använd [Application Insights för JavaScript-appar och webbsidor](app-insights-javascript.md) för att hämta klienttelemetri från webbläsare som besöker en webbsida.
* [Konfigurera tillgänglighetswebbtester](app-insights-monitor-web-app-availability.md) så att du aviseras om webbplatsen inte fungerar.

