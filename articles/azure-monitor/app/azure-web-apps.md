---
title: Övervaka prestanda för Azure-tjänster | Microsoft Docs
description: Övervakning av programprestanda för Azure app services. Skapa diagram över inläsnings- och svarstider och beroendeinformation och ställ in aviseringar för prestanda.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 09084f71eda611c4b638a339d0d837e77e2639b9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432319"
---
# <a name="monitor-azure-app-service-performance"></a>Övervaka prestanda i Azure App Service
I den [Azure-portalen](https://portal.azure.com) du kan konfigurera övervakning av programprestanda för dina webbappar, mobila serverdelar och API-appar i [Azure App Service](../../app-service/overview.md). [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) instrumenterar din app så att den skickar telemetri om sina aktiviteter till Application Insights-tjänsten, där informationen lagras och analyseras. Där kan du använda diagram med mätvärden och sökverktyg för att diagnostisera problem, förbättra prestanda och utvärdera användningen.

## <a name="runtime-or-build-time"></a>Runtime eller build tid
Du kan konfigurera övervakning genom att instrumentera appen på något av två sätt:

* **Runtime** – du kan välja ett tillägg för prestandaövervakning när din app service redan är live. Du behöver inte återskapa eller installera om din app. Du får en standarduppsättning med paket som övervakar svarstider, framgångsfrekvens, undantag, beroenden och så vidare.

* **Vid utveckling** – Du kan installera ett paket i din app i samband med utvecklingen. Det här alternativet är mer flexibelt. Förutom motsvarande standardpaket kan du skriva kod för att anpassa telemetrin eller skicka din egen telemetri. Du kan logga specifika aktiviteter eller registrera händelser baserat på semantiken för din appdomän. Detta ger dig också möjligheten att testa den senaste versionen av Application Insights SDK som du kan välja att utvärdera beta SDK: er, medan realtidsövervakning är begränsad till den senaste stabila versionen.

## <a name="runtime-instrumentation-with-application-insights"></a>Instrumentation för körning med Application Insights
Om du för närvarande kör en app service i Azure måste du redan tillgång till viss övervakning: begärande- och Felfrekvens som standard. Lägg till Application Insights för att få mer, till exempel svarstider, övervakning av anrop till beroenden, smart identifiering och åtkomst till kraftfulla Kusto-frågespråket. 

1. **Välj Application Insights** i Azure-Kontrollpanelen för app service.

    ![Välj Application Insights under inställningar](./media/azure-web-apps/settings-app-insights.png)

   * Välja att skapa en ny resurs, såvida inte du redan har konfigurerat en Application Insights-resurs för det här programmet. 

    > [!NOTE]
    > När du klickar på **OK** att skapa den nya resursen som du kommer att uppmanas att **tillämpa övervakningsinställningar**. Att välja **Fortsätt** länkar nya Application Insights-resursen till app service, gör du så kommer också **utlösa en omstart av app service**. 

    ![Instrumentera din webbapp](./media/azure-web-apps/create-resource.png)

2. Du kan välja hur du vill att application insights för att samla in data per plattform för ditt program när du har angett vilken resurs som ska användas. Övervaka ASP.NET-app är på som standard med två olika nivåer för insamling av.

    ![Välj alternativ per plattform](./media/azure-web-apps/choose-options-new.png)

    * .NET **grundläggande samling** nivå erbjuder grundläggande enkelinstanser APM-funktioner.
    
    * .NET **rekommenderas samling** nivå:
        * Lägger till trender för processor, minne och i/o-användning.
        * Korrelerar micro-tjänster över begäran/beroende gränser.
        * Samlar in användningstrender och gör det möjligt för korrelation från tillgänglighetsresultat med transaktioner.
        * Samlar in undantag som ohanterat av värdprocessen.
        * Förbättrar precisionen för APM-måtten under belastning, när sampling används.
    
    .NET core erbjuder **rekommenderas samling** eller **inaktiverad** för .NET Core 2.0 och 2.1.

3. **Instrumentera din apptjänst** när Application Insights har installerats.

   **Aktivera övervakning på klientsidan** för sidan sidvy och användartelemetri.

    (Detta är aktiverat som standard för .NET Core-appar med **rekommenderas samling**, oavsett om appinställningen 'APPINSIGHTS_JAVASCRIPT_ENABLED' är närvarande. Detaljerad UI-baserad stöd för inaktivering av övervakning på klientsidan är inte tillgänglig för .NET Core.)
    
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
Application Insights kan tillhandahålla mer detaljerad telemetri genom installationen av ett SDK i din app. Mer specifikt kan du samla in spårningsloggar, [skriva anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md) och få mer detaljerade undantagsrapporter.

1. **I Visual Studio** (2013 uppdatering 2 eller senare) konfigurerar du Application Insights för ditt projekt.

    Högerklicka på webbprojektet och välj **Lägg till > Application Insights** eller **projekt** > **Programinsikter**  >  **Konfigurera Application Insights**.

    ![Högerklicka på webbprojektet och välj Lägg till Application Insights eller Konfigurera Application Insights](./media/azure-web-apps/03-add.png)

    Om du uppmanas att logga in använder du autentiseringsuppgifterna för ditt Azure-konto.

    Åtgärden har två effekter:

   1. Skapar en Application Insights-resurs i Azure, där telemetri lagras, analyseras och visas.
   2. Lägger till NuGet-paketet för Application Insights i din kod (om det inte redan finns där) och konfigurerar det att skicka telemetri till Azure-resursen.
2. **Testa telemetrin** genom att köra appen på utvecklingsdatorn (F5).
3. **Publicera appen** till Azure som vanligt. 

*Hur växlar jag till att skicka telemetri till en annan Application Insights-resurs?*

* Högerklicka på projektet i Visual Studio, Välj **konfigurera Application Insights**, och väljer önskad resurs. Du får möjlighet att skapa en ny resurs. Återskapa och distribuera igen.

## <a name="automate-monitoring"></a>Automatisera övervakning

Endast programinställningarna måste anges för att aktivera telemetriinsamling av med Application Insights:

   ![App Service-programinställningar med tillgängliga Application Insights-inställningar](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Inställningar för programdefinitioner

|App inställningsnamn |  Definition | Värde |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Viktigaste tillägg som styr realtidsövervakning. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  I är läge, men viktiga funktioner aktiverade för att säkerställa optimala prestanda. | `default` eller `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Styr om motorn för binary-omskrivning `InstrumentationEngine` aktiveras. Den här inställningen har prestanda och påverkar kalla start/starttiden. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Kontrollerar om SQL och Azure table text ska hämtas tillsammans med beroendeanrop. Prestandavarning: Detta kräver den `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Programinställningar för App Service med Azure Resource Manager

Programinställningar för App Services kan hanteras och konfigurerad med [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Den här metoden kan användas när du distribuerar nya App Service-resurser med Azure Resource Manager automation eller för att ändra inställningarna för befintliga resurser.

Den grundläggande strukturen i programinställningarna JSON för en app service är nedan:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Ett exempel på en Azure Resource Manager-mall med inställningar för program konfigurerade för Application Insights detta [mall](https://github.com/Andrew-MSFT/BasicImageGallery) kan vara användbart, särskilt avsnittet från och med [rad 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatisera skapandet av en Application Insights-resurs och en länk till din nya App Service.

Starta processen för att skapa en Azure Resource Manager-mall med alla Application Insights standardinställningarna, som om du tänker skapa en ny Webbapp med Application Insights som aktiveras.

Välj **automatiseringsalternativ**

   ![App Service web app skapas menyn](./media/azure-web-apps/create-web-app.png)

Detta genererar den senaste Azure Resource Manager-mallen med alla nödvändiga inställningar som konfigurerats.

  ![App Service web app-mall](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> Mallen genererar programinställningar i ”standard”-läge. Det här läget är prestandaoptimerad, men du kan ändra mallen för att aktivera de funktioner som du föredrar.

## <a name="more-telemetry"></a>Mer telemetri

* [Data om webbsidesinläsning](../../azure-monitor/app/javascript.md)
* [Anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>Felsökning

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>Jag behöver fortfarande att gå till tillägg – Lägg till – Application Insights-tillägget för nya App Service-appar?

Nej, du behöver inte längre lägga till tillägget manuellt. När Application Insights aktiveras via bladet inställningar, lägger till alla nödvändiga programinställningar för att aktivera övervakning. Detta är nu möjligt, eftersom de filer som tidigare har lagts till av tillägget är nu [förinstallerad](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) som en del av App Service-avbildning. Filerna finns i `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`.

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>Om körning och övervakning av build tid båda är aktiverade jag avslutas med duplicerade data?

Nej, som standard om övervakning av build tid identifieras realtidsövervakning via tillägget slutar att skicka data och endast Byggtiden övervakning konfiguration kommer att användas. Att bestämma om du vill inaktivera realtidsövervakning baserat på identifiering av någon av dessa tre filer:

* Microsoft.ApplicationInsights dll
* Microsoft.ASP.NET.TelemetryCorrelation dll
* System.Diagnostics.DiagnosticSource dll

Det är viktigt att tänka på att i många versioner av Visual Studio kan vissa eller alla de här filerna läggs som standard i ASP.NET och Visual Studio för ASP.NET Core-mallfiler. Om ditt projekt har skapats baserat på någon av mallarna, även om du inte har uttryckligen aktiverat Application Insights, kan förekomsten av fil-beroendet förhindra realtidsövervakning från att aktivera.

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED orsakar ofullständig HTML-svaret i NET CORE-webbprogram.

Aktivera Javascript via App Services kan orsaka html-svar på att stängas.

* Lösning 1: Ange APPINSIGHTS_JAVASCRIPT_ENABLED inställningen till false eller ta bort det helt och starta om
* Lösning 2: Lägg till sdk via kod och ta bort tillägget (Profiler och Snapshot debugger fungerar inte med den här konfigurationen)

För att spåra det här problemet måste du gå till [Azure-tillägget orsakar ofullständig HTML-svaret](https://github.com/Microsoft/ApplicationInsights-Home/issues/277).

För .NET Core följande är för närvarande **stöds inte**:

* Fristående distribution.
* Appar som riktar in sig på .NET Framework.
* 2.2 för .NET core-program.

> [!NOTE]
> .NET core 2.0 och .NET Core 2.1 stöds. När du lägger till stöd för .NET Core 2.2 kommer att uppdateras i den här artikeln.

## <a name="next-steps"></a>Nästa steg
* [Kör profileraren för din live-app](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – övervaka Azure Functions med Application Insights
* [Aktivera Azure-diagnostik](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) så att den skickas till Application Insights.
* [Övervaka mätvärden för tjänstens hälsotillstånd](../../azure-monitor/platform/data-collection.md) för att se till att tjänsten är tillgänglig och svarar.
* [Få aviseringar](../../azure-monitor/platform/alerts-overview.md) när drifthändelser inträffar eller när mätvärden överskrider ett tröskelvärde.
* Använd [Application Insights för JavaScript-appar och webbsidor](../../azure-monitor/app/javascript.md) för att hämta klienttelemetri från webbläsare som besöker en webbsida.
* [Konfigurera tillgänglighetswebbtester](../../azure-monitor/app/monitor-web-app-availability.md) så att du aviseras om webbplatsen inte fungerar.