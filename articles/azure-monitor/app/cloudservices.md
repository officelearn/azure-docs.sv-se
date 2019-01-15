---
title: Application Insights för Azure molntjänster | Microsoft Docs
description: Övervaka webb- och arbetsroller effektivt med Application Insights
services: application-insights
documentationcenter: ''
keywords: WAD2AI, Azure Diagnostics
author: mrbullwinkle
manager: carmonm
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.workload: tbd
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: d27c0e9570959e01267d83a768ead45b48b7cea1
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267240"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights för Azure molntjänster
[Application Insights] [ start] kan övervaka [Azure cloud-tjänstapparnas](https://azure.microsoft.com/services/cloud-services/) för tillgänglighet, prestanda, fel och användning genom att kombinera data från Application Insights SDK: er med [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) data från dina molntjänster. Med den feedback du får om appens prestanda och effektivitet kan du fatta välgrundade beslut om designen i varje utvecklingslivscykel.

![Översikt över instrumentpanelen](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar behöver du:

* En [Azure](https://azure.com) prenumeration. Logga in med ditt Microsoft-konto för Windows, Xbox Live eller andra Microsoft-molntjänster. 
* Microsoft Azure tools 2.9 eller senare.
* Developer Analytics Tools 7.10 eller senare.

## <a name="get-started-quickly"></a>Kom i gång snabbt
Det snabbaste och enklaste sättet att övervaka din molntjänst med Application Insights är att välja det här alternativet när du publicerar din tjänst till Azure.

![Exempelsida för diagnostikinställningar](./media/cloudservices/azure-cloud-application-insights.png)

Det här alternativet instrumenterar din app vid körning, vilket ger dig all telemetri som du behöver övervaka förfrågningar, undantag och beroenden i dina web-roll. Den övervakar även prestandaräknare från dina arbetsroller. Alla spårningar för diagnostik som genereras av appen skickas också till Application Insights.

Om det här alternativet är allt du behöver, är du klar. 

Nästa steg är [visa mätvärden från din app](../../azure-monitor/app/metrics-explorer.md), [frågor till dina data med Analytics](../../azure-monitor/app/analytics.md), och eventuellt att konfigurera en [instrumentpanelen](../../azure-monitor/app/app-insights-dashboards.md). 

För att övervaka prestanda i webbläsaren, du kanske också vill ställa in [tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) och [Lägg till kod till dina webbsidor](../../azure-monitor/app/javascript.md).

I nästa avsnitt beskrivs följande ytterligare alternativ:

* Skicka data från olika komponenter och skapa konfigurationer för att avgränsa resurser.
* Lägg till anpassad telemetri från din app.

## <a name="sample-app-instrumented-with-application-insights"></a>Exempelapp som instrumenteras med Application Insights
I det här [exempelapp](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), Application Insights läggs till en molntjänst med två arbetsroller i Azure. 

Lär dig hur du anpassar ditt eget molntjänstprojekt på samma sätt i nästa avsnitt.

## <a name="plan-resources-and-resource-groups"></a>Planera resurser och resursgrupper
Telemetrin från din app lagras, analyseras och visas i en Azure-resurs av typen Application Insights. 

Varje resurs tillhör en resursgrupp. Resursgrupper används för att hantera kostnaderna för att bevilja åtkomst till gruppmedlemmar och för att distribuera uppdateringar i en enda samordnad transaktion. Du kan till exempel [skriva ett skript för att distribuera](../../azure-resource-manager/resource-group-template-deploy.md) en Azure-molntjänst och dess Application Insights-övervakningsresurser i en enda åtgärd.

### <a name="resources-for-components"></a>Resurser för komponenter
Vi rekommenderar att du skapar en separat resurs för varje komponent i din app. Det vill säga skapar du en resurs för varje webbroll och arbetsroll. Du kan analysera varje komponent separat, men du skapar en [instrumentpanelen](../../azure-monitor/app/app-insights-dashboards.md) som sammanför de viktigaste diagrammen från alla komponenter, så att du kan jämföra och övervaka dem tillsammans i en enda vy. 

En annan metod är att skicka telemetrin från mer än en roll till samma resurs, men [lägga till en dimensionsegenskap till varje telemetriobjekt](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer) som identifierar dess källroll. Med den här metoden visar måttdiagram som till exempel undantag, visar normalt en sammanställning av antalen från olika roller, men du kan segmentera diagrammet efter rollidentifieraren vid behov. Du kan också filtrera sökningar efter samma dimension. Detta alternativ gör det lite enklare att visa allt på samma gång, men det kan även leda till viss förvirring mellan rollerna.

Webbläsartelemetri ingår vanligtvis i samma resurs som dess webbroll på serversidan.

Placera Application Insights-resurser för de olika komponenterna i en resursgrupp. Den här metoden gör det enkelt att hantera dem tillsammans. 

### <a name="separate-development-test-and-production"></a>Avgränsa utveckling, testning och produktion
Om du utvecklar anpassade händelser för din nästa funktion medan den tidigare versionen är aktiv, skickar du utvecklingstelemetrin till en separat Application Insights-resurs. I annat fall kan det vara svårt att hitta testtelemetrin bland all trafik från live-webbplatsen.

Om du vill undvika detta kan skapa separata resurser för varje versionskonfiguration eller ”stämpel” (utveckling, testning, produktion och så vidare) för datorn. Placera resurserna för varje versionskonfiguration i en separat resursgrupp. 

Om du vill skicka telemetrin till lämpliga resurser, kan du ställa in Application Insights SDK så att det använder en annan instrumentation nyckeln, beroende på versionskonfiguration. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Skapa en Application Insights-resurs för varje roll

Om du har valt att skapa en separat resurs för varje roll, och kanske en separat uppsättning för varje versionskonfiguration, är det enklast att skapa alla Application Insights-portalen. Om du ofta skapar resurser kan du [automatisera processen](../../azure-monitor/app/powershell.md).

1. I den [Azure-portalen][portal]väljer **New** > **Utvecklartjänster**  >   **Application Insights**.  

    ![Application Insights-fönstret](./media/cloudservices/01-new.png)

1. I den **programtyp** listrutan, väljer **ASP.NET-webbprogram**.  
    Varje resurs identifieras av en instrumenteringsnyckel. Du kanske behöver den här nyckeln senare om du vill manuellt konfigurera eller verifiera konfigurationen av SDK.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Konfigurera Azure Diagnostics för varje roll
Ange det här alternativet om du vill övervaka din app med Application Insights. För webbroller tillhandahåller det här alternativet prestandaövervakning, aviseringar, diagnostik och användningsanalys. För andra roller kan du söka efter och övervaka Azure Diagnostics som omstart, prestandaräknare och anrop till System.Diagnostics.Trace. 

1. I Visual Studio Solution Explorer under  **\<YourCloudService >** > **roller**, öppnar du egenskaperna för varje roll.

1. I **Configuration**väljer den **skicka diagnostikdata till Application Insights** kryssrutan och välj sedan den Application Insights-resurs som du skapade tidigare.

Om du har valt att använda en separat Application Insights-resurs för varje versionskonfiguration väljer du konfigurationen först.

![Konfigurera Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

Detta innebär att infoga din Application Insights-instrumenteringsnycklar i filerna med namnet *ServiceConfiguration.\*. cscfg*. Här är den [exempelkoden](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Om du vill variera mängden diagnostikinformation som skickas till Application Insights kan du göra detta [genom att redigera den *.cscfg* filerna direkt](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="sdk"></a>Installera SDK i varje projekt
Du kan använda det här alternativet för att lägga till anpassad telemetri till valfri roll. Alternativet ger en närmare analys av hur din app används och fungerar.

I Visual Studio konfigurerar du Application Insights SDK för varje molnapprojekt.

1. Konfigurera **webbroller**, högerklicka på projektet och välj sedan **konfigurera Application Insights** eller **Lägg till > Application Insights telemetry**.

1. Konfigurera **arbetsroller**: 

    a. Högerklicka på projektet och välj sedan **hantera NuGet-paket**.

    b. Lägg till [Application Insights för Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Sök efter ”Application Insights”](./media/cloudservices/04-ai-nuget.png)

1. Konfigurera SDK för att skicka data till Application Insights-resursen:

    a. I en lämplig startfunktion ange instrumenteringsnyckeln från Konfigurationsinställningen i den *.cscfg* fil:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Upprepa ”steg en” för varje roll i din app. Se exemplen:
   
    * [Webbroll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Arbetsroll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [För webbsidor](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Ange den *ApplicationInsights.config* filen alltid ska kopieras till utdatakatalogen.  
    Ett meddelande i den *.config* filen ber dig att placera instrumenteringsnyckeln där. Men för molnappar, är det bättre att ställa in den från den *.cscfg* fil. Den här metoden gör att rollen identifieras korrekt i portalen.

#### <a name="run-and-publish-the-app"></a>Köra och publicera appen

1. Kör appen och logga in på Azure. 

1. Öppna Application Insights-resurser som du skapade.  
    Enskilda datapunkter visas i [Search](../../azure-monitor/app/diagnostic-search.md), och sammanställda data visas i [Metric Explorer](../../azure-monitor/app/metrics-explorer.md). 

1. Lägg till mer telemetri (se nästa avsnitt) och sedan publicera din app för att få live diagnostik- och användningsdata feedback. 

Om det finns inga data, gör du följande:
1. Om du vill visa enskilda händelser, öppna den [Search] [ diagnostic] panelen.
1. Öppna olika sidor i appen, så att det genererar telemetri.
1. Vänta några sekunder och klicka sedan på **uppdatera**.  
    Mer information finns i [felsökning][qna].

## <a name="view-azure-diagnostics-events"></a>Visa Azure Diagnostics-händelser
Du hittar den [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) i Application Insights på följande platser:

* Prestandaräknare visas som anpassade mått. 
* Windows-händelseloggar visas som spårningar och anpassade händelser.
* Programloggar, ETW-loggar och diagnostikinfrastrukturloggar visas som spårningar.

Om du vill visa prestandaräknare och antalet händelser, öppna [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) och Lägg till följande diagram:

![Azure Diagnostics-data](./media/cloudservices/23-wad.png)

Om du vill söka i de olika spårningsloggarna som skickas av Azure Diagnostics, använda [Search](../../azure-monitor/app/diagnostic-search.md) eller en [analysfråga](../../azure-monitor/log-query/get-started-portal.md). Anta exempelvis att du har ett ohanterat undantag som gör att en roll kraschade och återanvändes. Den här informationen skulle visas i kanalen Program i Windows-händelseloggen. Du kan använda Sök för att visa felet Windows-händelseloggen och hämta den fullständiga stackspårningen för undantaget. Detta hjälper dig att hitta den bakomliggande orsaken till problemet.

![Söka i Azure Diagnostics](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mer telemetri
I nästa avsnitt beskrivs hur du hämtar ytterligare telemetri från olika aspekter av din app.

## <a name="track-requests-from-worker-roles"></a>Spåra begäranden från arbetsroller
I webbroller samlar modulen för begäranden automatiskt in data om HTTP-förfrågningar. Exempel på hur du kan åsidosätta standardinsamlingsbeteendet finns i [exempel MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Du kan samla in prestanda från anrop till arbetsroller genom att spåra dem på samma sätt som HTTP-förfrågningar. I Application Insights mäter telemetritypen Begäran en enhet av det namngivna arbetet på serversidan som kan tidsbestämmas och som kan lyckas eller misslyckas separat. Även om HTTP-förfrågningar kan samlas in automatiskt av SDK, kan du infoga egen kod för att spåra begäranden till arbetsroller.

Se de två exempelarbetsrollerna som instrumenteras till rapportförfrågningar: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Undantag
Information om hur du samlar in ohanterade undantag från olika typer av web app finns i [övervakning undantag i Application Insights](../../azure-monitor/app/asp-net-exceptions.md).

Exempelwebbrollen har MVC5- och Web API 2-styrenheter. De ohanterade undantagen från de två samlas in med följande hanterare:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) konfigureras för MVC5-styrenheter [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) konfigureras för Web API 2-styrenheter [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

För arbetsroller kan spåra du undantag på två sätt:

* Använd TrackException(ex).
* Om du har lagt till Application Insights trace listener NuGet-paketet kan du använda System.Diagnostics.Trace logga undantag [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Prestandaräknare
Följande räknare samlas in som standard:

    * \Process(??APP_WIN32_PROC??)\% Processortid
    * \Memory\Tillgängliga byte
    * \.NET CLR-undantag(??APP_CLR_PROC??)\# undantag som utlöses/sekund
    * \Process(??APP_WIN32_PROC??)\Privata byte
    * \Process(??APP_WIN32_PROC??)\Byte i I/O-data per sekund
    * \Processor(_Total)\% processortid

För webbroller samlas även dessa räknare in:

    * \ASP.NET-program(??APP_W3SVC_PROC??)\Begäranden/sek
    * \ASP.NET-program(??APP_W3SVC_PROC??)\Körningstid för begäran
    * \ASP.NET-program(??APP_W3SVC_PROC??)\Begäranden i tillämpningskö

Du kan ange fler anpassade prestandaräknare eller andra Windows-prestandaräknare genom att redigera *ApplicationInsights.config* [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Prestandaräknare](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Korrelerad telemetri för arbetsroller
Du kan se vad som ledde till en misslyckad eller hög fördröjning-begäran för en omfattande diagnostik-upplevelse. Med webbroller konfigurerar SDK automatiskt en sambandet mellan relaterad telemetri. 

För att uppnå den här vyn för arbetsroller kan använda du en anpassad telemetriinitierare för att ange ett gemensamt Operation.Id-kontextattribut för all telemetri. Då kan du visa en snabb överblick över om problem svarstider eller fel orsakades av ett beroende eller din kod. 

Så här gör du:

* Ange Korrelations-ID i en CallContext [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). I det här fallet använder vi ID för begäran som correlationId.
* Lägg till en anpassad TelemetryInitializer-implementering och ange Operation.Id till det correlationId som angavs tidigare. Ett exempel finns i [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Lägg till den anpassade telemetriinitieraren. Du kan göra i den *ApplicationInsights.config* filen eller i koden [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Klienttelemetri
Att hämta webbläsarbaserad telemetri, till exempel sidvisningar, sidinläsningstider eller skriptundantag, samt att skriva anpassad telemetri i dina Sidskript finns i [Lägg till JavaScript-SDK till dina webbsidor][client].

## <a name="availability-tests"></a>Tillgänglighetstester
Att vara säker din app är aktivt och effektivt, [konfigurera webbtester][availability].

## <a name="display-everything-together"></a>Visa allt tillsammans
För en övergripande bild av systemet kan du visa de viktigaste Övervakningsdiagrammen på en [instrumentpanelen](../../azure-monitor/app/app-insights-dashboards.md). Du kan till exempel fästa antalet begäranden och fel för varje roll. 

Om systemet använder andra Azure-tjänster som Stream Analytics, inkludera deras Övervakningsdiagrammen. 

Om du har en klientmobilapp använder du [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). Skapa frågor i [Analytics](../../azure-monitor/app/analytics.md) för att visa antalet händelser och fäst dem på instrumentpanelen.

## <a name="example"></a>Exempel
[Exemplet](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) övervakar en tjänst som har en webbroll och två arbetsroller.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Undantaget ”metoden hittades inte” vid körning i Azure-molntjänster
Utvecklade du för .NET 4.6? .NET 4.6 stöds inte automatiskt i Azure cloud services-roller. [Installera .NET 4.6 för varje roll](../../cloud-services/cloud-services-dotnet-install-dotnet.md) innan du kör din app.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg
* [Konfigurera Azure Diagnostics-överföring till Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automatiskt skapa Application Insights-resurser](../../azure-monitor/app/powershell.md)
* [Automatisera Azure-diagnostik](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
