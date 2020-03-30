---
title: Programinsikter för Azure-molntjänster | Microsoft-dokument
description: Övervaka webb- och arbetsroller effektivt med Application Insights
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: ce794a7bd18635fddfa30056ab2d675dc138097d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276197"
---
# <a name="application-insights-for-azure-cloud-services"></a>Programinsikter för Azure-molntjänster
[Application Insights][start] kan övervaka [Azure cloud service-appar](https://azure.microsoft.com/services/cloud-services/) för tillgänglighet, prestanda, fel och användning genom att kombinera data från Application Insights SDK:er med [Azure Diagnostics-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) från dina molntjänster. Med den feedback du får om appens prestanda och effektivitet kan du fatta välgrundade beslut om designen i varje utvecklingslivscykel.

![Översikt instrumentpanel](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Krav
Innan du börjar behöver du:

* En [Azure-prenumeration.](https://azure.com) Logga in med ditt Microsoft-konto för Windows, Xbox Live eller andra Microsoft-molntjänster. 
* Microsoft Azure-verktyg 2.9 eller senare.
* Developer Analytics Tools 7.10 eller senare.

## <a name="get-started-quickly"></a>Kom igång snabbt
Det snabbaste och enklaste sättet att övervaka din molntjänst med Application Insights är att välja det här alternativet när du publicerar din tjänst till Azure.

![Sidan Inställningar för exempeldiagnostik](./media/cloudservices/azure-cloud-application-insights.png)

Det här alternativet instrumenterar din app vid körning, vilket ger dig all telemetri som du behöver för att övervaka begäranden, undantag och beroenden i webbrollen. Den övervakar också prestandaräknare från dina arbetsroller. Alla diagnostikspårningar som genereras av din app skickas också till Application Insights.

Om det här alternativet är allt du behöver är du klar. 

Nästa steg är [att visa mått från din app](../../azure-monitor/app/metrics-explorer.md)och fråga dina data med [Analytics](../../azure-monitor/app/analytics.md). 

Om du vill övervaka prestanda i webbläsaren kanske du också vill ställa in [tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) och [lägga till kod på webbsidorna](../../azure-monitor/app/javascript.md).

I nästa avsnitt beskrivs följande ytterligare alternativ:

* Skicka data från olika komponenter och skapa konfigurationer för att separera resurser.
* Lägg till anpassad telemetri från din app.

## <a name="sample-app-instrumented-with-application-insights"></a>Exempel på app instrumenterad med Application Insights
I den här [exempelappen](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)läggs Application Insights till i en molntjänst med två arbetarroller som finns i Azure. 

I nästa avsnitt får du lära dig hur du anpassar ditt eget molntjänstprojekt på samma sätt.

## <a name="plan-resources-and-resource-groups"></a>Planera resurser och resursgrupper
Telemetrin från din app lagras, analyseras och visas i en Azure-resurs av typen Application Insights. 

Varje resurs tillhör en resursgrupp. Resursgrupper används för att hantera kostnader, bevilja åtkomst till gruppmedlemmar och för att distribuera uppdateringar i en enda samordnad transaktion. Du kan till exempel [skriva ett skript för att distribuera](../../azure-resource-manager/templates/deploy-powershell.md) en Azure-molntjänst och dess application insights-övervakningsresurser i en och samma åtgärd.

### <a name="resources-for-components"></a>Resurser för komponenter
Vi rekommenderar att du skapar en separat resurs för varje komponent i appen. Det vill säga du skapar en resurs för varje webbroll och arbetsroll. Du kan analysera varje komponent separat, men du skapar en [instrumentpanel](../../azure-monitor/app/overview-dashboard.md) som sammanför nyckeldiagrammen från alla komponenter, så att du kan jämföra och övervaka dem tillsammans i en enda vy. 

En alternativ metod är att skicka telemetrin från mer än en roll till samma resurs, men [lägga till en dimensionsegenskap till varje telemetriobjekt](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) som identifierar dess källroll. I den här metoden visar måttdiagram, till exempel undantag, normalt en aggregering av antalet från de olika rollerna, men du kan segmentera diagrammet efter rollidentifieraren efter behov. Du kan också filtrera sökningar efter samma dimension. Detta alternativ gör det lite lättare att se allt på samma gång, men det kan också leda till viss förvirring mellan rollerna.

Webbläsartelemetri ingår vanligtvis i samma resurs som dess webbroll på serversidan.

Placera application insights-resurserna för de olika komponenterna i en resursgrupp. Detta tillvägagångssätt gör det enkelt att hantera dem tillsammans. 

### <a name="separate-development-test-and-production"></a>Avgränsa utveckling, testning och produktion
Om du utvecklar anpassade händelser för din nästa funktion medan den tidigare versionen är aktiv, skickar du utvecklingstelemetrin till en separat Application Insights-resurs. Annars kan det vara svårt att hitta din testtelemetri bland all trafik från live-webbplatsen.

Undvik den här situationen genom att skapa separata resurser för varje byggkonfiguration eller "stämpel" (utveckling, test, produktion och så vidare) i systemet. Placera resurserna för varje versionskonfiguration i en separat resursgrupp. 

Om du vill skicka telemetrin till lämpliga resurser kan du ställa in SDK för Programinsikter så att den hämtar en annan instrumenteringsnyckel, beroende på byggkonfigurationen. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Skapa en Application Insights-resurs för varje roll

Om du har bestämt dig för att skapa en separat resurs för varje roll, och kanske en separat uppsättning för varje byggkonfiguration, är det enklast att skapa dem alla i application insights-portalen. Om du skapar resurser mycket kan du [automatisera processen](../../azure-monitor/app/powershell.md).

1. I [Azure-portalen][portal]väljer du **Nya** > **utvecklartjänster** > **Application Insights**.  

    ![Fönstret Programinsikter](./media/cloudservices/01-new.png)

1. Välj **ASP.NET webbprogram**i listrutan **Programtyp** .

Varje resurs identifieras med en instrumenteringsnyckel. Du kan behöva den här nyckeln senare om du vill konfigurera eller verifiera konfigurationen av SDK manuellt.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Konfigurera Azure Diagnostics för varje roll
Ange det här alternativet om du vill övervaka din app med Application Insights. För webbroller tillhandahåller det här alternativet prestandaövervakning, aviseringar, diagnostik och användningsanalys. För andra roller kan du söka efter och övervaka Azure Diagnostics, till exempel omstart, prestandaräknare och anrop till System.Diagnostics.Trace. 

1. Öppna egenskaperna för varje roll under ** \<DinCloudService **  >  **>-roller**i Utforskaren för Visual Studio Solution Explorer.

1. Markera kryssrutan **Skicka diagnostikdata till Application Insights** i **Konfiguration**och markera sedan den application insights-resurs som du skapade tidigare.

Om du har valt att använda en separat Application Insights-resurs för varje versionskonfiguration väljer du konfigurationen först.

![Konfigurera programinsikter](./media/cloudservices/configure-azure-diagnostics.png)

Detta innebär att instrumenteringsnycklarna Application Insights infogas i filerna Som heter *\*ServiceConfiguration. . cscfg*. Här är [exempelkoden](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Om du vill variera diagnostiknivån som skickas till Application Insights kan du göra det genom att [redigera *CSCFG-filerna* direkt](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Installera SDK i varje projekt
Med det här alternativet kan du lägga till anpassad affärstelemetri i valfri roll. Alternativet ger en närmare analys av hur din app används och fungerar.

I Visual Studio konfigurerar du Application Insights SDK för varje molnapprojekt.

1. Om du vill konfigurera **webbroller**högerklickar du på projektet och väljer sedan **Konfigurera programstatistik** eller **Lägg till > programinsiktstema**.

1. Så här konfigurerar du **arbetarroller:** 

    a. Högerklicka på projektet och välj sedan **Hantera NuGet-paket**.

    b. Lägg till [Application Insights för Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Sök efter ”Application Insights”](./media/cloudservices/04-ai-nuget.png)

1. Så här konfigurerar du SDK för att skicka data till application insights-resursen:

    a. I en lämplig startfunktion ställer du in instrumenteringsnyckeln från konfigurationsinställningen i *CSCFG-filen:*
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Upprepa "steg a" för varje roll i appen. Se exemplen:
   
    * [Webbroll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Arbetsroll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [För webbsidor](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Ange att filen *ApplicationInsights.config* ska kopieras till utdatakatalogen.

   Ett meddelande i *config-filen* ber dig att placera instrumenteringsnyckeln där. Men för molnappar är det bättre att ställa in den från *CSCFG-filen.* Den här metoden säkerställer att rollen identifieras korrekt i portalen.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Konfigurera statusövervakaren för att samla in fullständiga SQL-frågor (valfritt)

Det här steget behövs bara om du vill fånga fullständiga SQL-frågor på .NET Framework. 

1. I `\*.csdef` fil Lägg till [startuppgift](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks) för varje roll som liknar 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Ladda ner [InstallAgent.bat](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) och [InstallAgent.ps1](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1), lägg dem i `AppInsightsAgent` mappen på varje rollprojekt. Se till att kopiera dem till utdatakatalogen via Visual Studio-filegenskaper eller skapa skript.

3. Lägg till miljövariabler på alla arbetsroller: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Köra och publicera appen

1. Kör din app och logga in på Azure. 

1. Öppna de application insights-resurser som du har skapat.

   Enskilda datapunkter visas i [Sök][diagnostic]och aggregerade data visas i [Metric Explorer](../../azure-monitor/app/metrics-explorer.md).

1. Lägg till mer telemetri (se nästa avsnitt) och publicera sedan appen för att få livediagnostik och användningsfeedback. 

Om det inte finns några data gör du följande:

1. Om du vill visa [Search][diagnostic] enskilda händelser öppnar du sökpanelen.
1. Öppna olika sidor i appen så att den genererar viss telemetri.
1. Vänta några sekunder och klicka sedan på **Uppdatera**.  

Mer information finns i [Felsökning][qna].

## <a name="view-azure-diagnostics-events"></a>Visa Azure Diagnostics-händelser
Du hittar [Azure Diagnostics-informationen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) i Application Insights på följande platser:

* Prestandaräknare visas som anpassade mått. 
* Windows-händelseloggar visas som spårningar och anpassade händelser.
* Programloggar, ETW-loggar och diagnostikinfrastrukturloggar visas som spårningar.

Om du vill visa prestandaräknare och antal händelser öppnar du [Statistikutforskaren](../../azure-monitor/app/metrics-explorer.md) och lägger till följande diagram:

![Azure Diagnostics-data](./media/cloudservices/23-wad.png)

Om du vill söka i de olika spårningsloggarna som skickas av Azure Diagnostics använder du [Sök](../../azure-monitor/app/diagnostic-search.md) eller en [Analytics-fråga](../../azure-monitor/log-query/get-started-portal.md). Anta till exempel att du har ett ohanterat undantag som har orsakat en roll att krascha och återvinna. Den här informationen skulle visas i kanalen Program i Windows-händelseloggen. Du kan använda Sök för att visa Windows-händelseloggfelet och hämta den fullständiga stackspårningen för undantaget. Detta hjälper dig att hitta orsaken till problemet.

![Azure Diagnostics-sökning](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mer telemetri
I nästa avsnitt beskrivs hur du får ytterligare telemetri från olika aspekter av appen.

## <a name="track-requests-from-worker-roles"></a>Spåra begäranden från arbetarroller
I webbroller samlar modulen för begäranden automatiskt in data om HTTP-förfrågningar. Exempel på hur du kan åsidosätta standardinsamlingsbeteendet finns i [exemplet MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Du kan samla in prestanda från anrop till arbetsroller genom att spåra dem på samma sätt som HTTP-förfrågningar. I Application Insights mäter telemetritypen Begäran en enhet av det namngivna arbetet på serversidan som kan tidsbestämmas och som kan lyckas eller misslyckas separat. Även om HTTP-begäranden hämtas automatiskt av SDK kan du infoga din egen kod för att spåra begäranden till arbetarroller.

Se de två exempelarbetsrollerna som instrumenteras för att rapportera begäranden: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [ArbetarrolleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Undantag
Information om hur du samlar in ohanterade undantag från olika webbapptyper finns [i Övervaka undantag i Application Insights](../../azure-monitor/app/asp-net-exceptions.md).

Exempelwebbrollen har MVC5- och Web API 2-styrenheter. De ohanterade undantagen från de två samlas in med följande hanterare:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) konfigureras för MVC5-styrenheter [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) konfigureras för Web API 2-styrenheter [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

För arbetsroller kan du spåra undantag på två sätt:

* Använd TrackException(ex).
* Om du har lagt till Application Insights trace listener NuGet-paketet kan du använda System.Diagnostics.Trace för att logga undantag [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Prestandaräknare
Följande räknare samlas in som standard:

* \Process(?? APP_WIN32_PROC??) \% Processortid
* \Memory\Tillgängliga byte
* \.NET CLR-undantag(??APP_CLR_PROC??)\# undantag som utlöses/sekund
* \Process(??APP_WIN32_PROC??)\Privata byte
* \Process(??APP_WIN32_PROC??)\Byte i I/O-data per sekund
* \Processor(_Total)\% processortid

För webbroller samlas även dessa räknare in:

* \ASP.NET-program(??APP_W3SVC_PROC??)\Begäranden/sek
* \ASP.NET-program(??APP_W3SVC_PROC??)\Körningstid för begäran
* \ASP.NET-program(??APP_W3SVC_PROC??)\Begäranden i tillämpningskö

Du kan ange ytterligare anpassade eller andra Prestandaräknare för Windows genom att redigera *ApplicationInsights.config* [enligt det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Prestandaräknare](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Korrelerad telemetri för arbetarroller
För en omfattande diagnostikupplevelse kan du visa vad som ledde till en misslyckad begäran eller begäran om hög latens. Med webbroller ställer SDK automatiskt in en korrelation mellan relaterad telemetri. 

Om du vill uppnå den här vyn för arbetarroller kan du använda en anpassad telemetriinitierare för att ange ett gemensamt Operation.Id kontextattribut för all telemetri. På så sätt kan du snabbt se om svarstiden eller felproblemet orsakades av ett beroende eller din kod. 

Så här gör du:

* Ange correlationId till en CallContext [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). I det här fallet använder vi request-ID som correlationId.
* Lägg till en anpassad TelemetryInitializer-implementering för att ställa in Operation.Id till correlationId som angavs tidigare. Ett exempel finns i [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Lägg till den anpassade telemetriinitieraren. Du kan göra det i filen *ApplicationInsights.config* eller i kod [som visas i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Klienttelemetri
Information om hur du hämtar webbläsarbaserad telemetri, till exempel antal sidvyer, sidinläsningstider eller skriptundantag, och för att skriva anpassad telemetri i sidskripten finns [i Lägga till JavaScript SDK på dina webbsidor][client].

## <a name="availability-tests"></a>Tillgänglighetstester
Konfigurera [webbtester][availability]för att se till att appen förblir live och svarar.

## <a name="display-everything-together"></a>Visa allt tillsammans
För en övergripande bild av systemet kan du visa de viktigaste övervakningsdiagrammen tillsammans på en [instrumentpanel](../../azure-monitor/app/overview-dashboard.md). Du kan till exempel fästa antalet begäranden och fel för varje roll. 

Om ditt system använder andra Azure-tjänster, till exempel Stream Analytics, inkluderar även deras övervakningsdiagram. 

Om du har en klientmobilapp använder du [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). Skapa frågor i [Analytics](../../azure-monitor/app/analytics.md) för att visa antalet händelser och fäst dem på instrumentpanelen.

## <a name="example"></a>Exempel
[Exemplet](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) övervakar en tjänst som har en webbroll och två arbetsroller.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Undantag "metod hittades inte" för att köra i Azure molntjänster
Utvecklade du för .NET 4.6? .NET 4.6 stöds inte automatiskt i Azure-molntjänstroller. [Installera .NET 4.6 på varje roll](../../cloud-services/cloud-services-dotnet-install-dotnet.md) innan du kör appen.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg
* [Konfigurera Azure Diagnostics-överföring till Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Skapa automatiskt application insights-resurser](../../azure-monitor/app/powershell.md)
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
