---
title: "Application Insights för Azure Cloud Services | Microsoft Docs"
description: "Övervaka webb- och arbetsroller effektivt med Application Insights"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
editor: alancameronwills
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.devlang: na
ms.tgt_pltfrm: ibiza
ms.topic: get-started-article
ms.workload: tbd
ms.date: 05/05/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bfae0fcf992c38d7afef6140fdd79d87ab0ecb4f
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights för Azure Cloud Services
Du kan övervaka [Microsoft Azure Cloud-tjänstapparnas](https://azure.microsoft.com/services/cloud-services/) tillgänglighet, prestanda, fel och användning med [Application Insights][start]. Med den feedback du får om appens prestanda och effektivitet kan du fatta välgrundade beslut om designen i varje utvecklingslivscykel.

![Exempel](./media/app-insights-cloudservices/sample.png)

## <a name="before-you-start"></a>Innan du börjar
Du behöver:

* En prenumeration med [Microsoft Azure](http://azure.com). Logga in med ett Microsoft-konto som du kanske har för Windows, XBox Live eller andra Microsoft-molntjänster. 
* Microsoft Azure Tools 2.9 eller senare
* Developer Analytics Tools 7.10 eller senare

## <a name="quick-start"></a>Snabbstart
Det snabbaste och enklaste sättet att övervaka din molntjänst med Application Insights är att välja det här alternativet när du publicerar din tjänst till Azure.

![Exempel](./media/app-insights-cloudservices/azure-cloud-application-insights.png)

Det här alternativet instrumenterar appen vid körtillfället, och du har all telemetri som du behöver för att övervaka förfrågningar, undantag och beroenden i din webbroll, samt prestandaräknare från dina arbetsroller. Alla diagnostikspårningar som genereras av din app skickas också till Application Insights.

Om det är allt du behöver så är du klar! Nästa steg är [att visa mätvärden från appen](app-insights-metrics-explorer.md), [att köra frågor mot dina data med Analytics](app-insights-analytics.md) och eventuellt att konfigurera en [instrumentpanel](app-insights-dashboards.md). Du kanske vill konfigurera [tillgänglighetstester](app-insights-monitor-web-app-availability.md) och [lägga till kod till dina webbsidor](app-insights-javascript.md) för att övervaka prestanda i webbläsaren.

Men du har fler alternativ:

* Skicka data från olika komponenter och skapa konfigurationer för att avgränsa resurser.
* Lägg till anpassad telemetri från din app.

Läs vidare om du är intresserad av dessa alternativ.

## <a name="sample-application-instrumented-with-application-insights"></a>Exempelprogram som instrumenteras med Application Insights
Ta en titt på det här [exempelprogrammet](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) där Application Insights läggs till i en molntjänst med två arbetsroller som finns i Azure. 

Nedan följer en beskrivning av hur du anpassar ditt eget molntjänstprojekt på samma sätt.

## <a name="plan-resources-and-resource-groups"></a>Planera resurser och resursgrupper
Telemetrin från din app lagras, analyseras och visas i en Azure-resurs av typen Application Insights. 

Varje resurs tillhör en resursgrupp. Resursgrupper används för att hantera kostnader, för att bevilja åtkomst till gruppmedlemmar och för att distribuera uppdateringar i en enda samordnad transaktion. Du kan till exempel [skriva ett skript för att distribuera](../azure-resource-manager/resource-group-template-deploy.md) en Azure-molntjänst och dess Application Insights-övervakningsresurser i en enda åtgärd.

### <a name="resources-for-components"></a>Resurser för komponenter
Det rekommenderade schemat är att skapa en separat resurs för varje komponent i ditt program, dvs. varje webbroll och arbetsroll. Du kan analysera varje komponent separat, men du kan också skapa en [instrumentpanel](app-insights-dashboards.md) som sammanför de viktigaste diagrammen från alla komponenter, så att du kan jämföra och övervaka dem tillsammans. 

Ett alternativt schema är att skicka telemetrin från mer än en roll till samma resurs, men [lägga till en dimensionsegenskap till varje telemetriobjekt](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer) som identifierar dess källroll. I det här schemat visar måttdiagram som till exempel undantag normalt en sammanställning av antalen från olika roller, men du kan segmentera diagrammet efter rollidentifieraren vid behov. Sökningar kan också filtreras efter samma dimension. Detta alternativ gör det lite enklare att visa allt på samma gång, men kan även leda till viss förvirring mellan rollerna.

Webbläsartelemetri ingår vanligtvis i samma resurs som dess webbroll på serversidan.

Placera Application Insights-resurserna för de olika komponenterna i en enda resursgrupp. På så sätt blir det enkelt att hantera dem tillsammans. 

### <a name="separating-development-test-and-production"></a>Avgränsa utveckling, testning och produktion
Om du utvecklar anpassade händelser för din nästa funktion medan den tidigare versionen är aktiv, skickar du utvecklingstelemetrin till en separat Application Insights-resurs. Annars blir det svårt att hitta testtelemetrin bland all trafik från live-webbplatsen.

Du kan undvika den här situationen genom att skapa separata resurser för varje versionskonfiguration eller ”stämpel” (utveckling, testning, produktion …) för systemet. Placera resurserna för varje versionskonfiguration i en separat resursgrupp. 

Du kan skicka telemetrin till lämpliga resurser genom att konfigurera Application Insights SDK så att det använder olika instrumenteringsnycklar beroende på versionskonfiguration. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Skapa en Application Insights-resurs för varje roll
Om du har valt att skapa en separat resurs för varje roll – och kanske en separat uppsättning för varje versionskonfiguration – är det enklast att skapa alla på Application Insights-portalen. Om du ofta skapar resurser kan du [automatisera processen](app-insights-powershell.md).

1. Skapa en ny Application Insights-resurs på [Azure Portal][portal]. Välj ASP.NET-app för programtyp. 

    ![Klicka på Nytt, Application Insights](./media/app-insights-cloudservices/01-new.png)
2. Observera att varje resurs identifieras av en instrumenteringsnyckel. Du kanske behöver den senare om du vill konfigurera eller verifiera konfigurationen för SDK manuellt.

    ![Klicka på Egenskaper, markera nyckeln och tryck på CTRL + C.](./media/app-insights-cloudservices/02-props.png) 

## <a name="set-up-azure-diagnostics-for-each-role"></a>Konfigurera Azure Diagnostics för varje roll
Ange det här alternativet om du vill övervaka din app med Application Insights. För webbroller tillhandahåller det här alternativet prestandaövervakning, aviseringar och diagnostik, samt användningsanalys. För andra roller kan du söka efter och övervaka Azure-diagnostik, till exempel omstart, prestandaräknare och anrop till System.Diagnostics.Trace. 

1. I Visual Studio Solution Explorer öppnar du egenskaperna för varje roll under &lt;YourCloudService&gt;, Roller.
2. I **Konfiguration** anger du **Skicka diagnostisk data till Application Insights** och väljer lämplig Application Insights-resurs som du skapade tidigare.

Om du har valt att använda en separat Application Insights-resurs för varje versionskonfiguration väljer du konfigurationen först.

![Konfigurera Application Insights i egenskaperna för varje Azure-roll](./media/app-insights-cloudservices/configure-azure-diagnostics.png)

Detta gör att dina Application Insights-instrumenteringsnycklar infogas i filerna med namnet `ServiceConfiguration.*.cscfg`. ([Exempelkod](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).

Om du vill ändra nivån för hur mycket diagnostikinformation som skickas till Application Insights kan du göra det [genom att redigera `.cscfg`-filerna direkt](app-insights-azure-diagnostics.md).

## <a name="sdk"></a>Installera SDK i varje projekt
Det här alternativet gör det möjligt att lägga till anpassad telemetri till valfri roll för en närmare analys av hur programmet används och fungerar.

I Visual Studio konfigurerar du Application Insights SDK för varje molnapprojekt.

1. **Webbroller**: Högerklicka på projektet och välj **Konfigurera Application Insights** eller **Lägg till > Application Insights Telemetry**.

2. **Arbetsroller**: 
 * Högerklicka på projektet och välj **Hantera Nuget-paket**.
 * Lägg till [Application Insights för Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Sök efter ”Application Insights”](./media/app-insights-cloudservices/04-ai-nuget.png)

3. Konfigurera SDK att skicka data till Application Insights-resursen.

    Ange instrumenteringsnyckeln från konfigurationsinställningen i .cscfg-filen i en lämplig startfunktion:
 
    ```C#
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    Gör detta för varje roll i programmet. Se exemplen:
   
   * [Webbroll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
   * [Arbetsroll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
   * [För webbsidor](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 
4. Ange att filen ApplicationInsights.config alltid ska kopieras till utdatakatalogen. 
   
    (I .config-filen visas meddelanden som uppmanar dig att placera instrumenteringsnyckeln där. Men för molnprogram är det dock bättre att ställa in den från .cscfg-filen. Detta säkerställer att rollen identifieras korrekt på portalen.)

#### <a name="run-and-publish-the-app"></a>Köra och publicera appen
Kör appen och logga in i Azure. Öppna de Application Insights-resurser som du skapade så ser du enskilda datapunkter som visas i [Sök](app-insights-diagnostic-search.md), och aggregerade data i [Metric Explorer](app-insights-metrics-explorer.md). 

Lägg till mer telemetri – se avsnitten nedan – och publicera sedan appen för att få live-diagnostik och feedback om användningen. 

#### <a name="no-data"></a>Ser du inga data?
* Öppna panelen [Sök][diagnostic] om du vill visa enskilda händelser.
* Använd programmet och öppna olika sidor så att det genererar telemetri.
* Vänta några sekunder och klicka på Uppdatera.
* Mer information finns i [Felsökning][qna].

## <a name="view-azure-diagnostic-events"></a>Visa Azure-diagnostikhändelser
Var du hittar diagnostiken:

* Prestandaräknare visas som anpassade mått. 
* Windows-händelseloggar visas som spårningar och anpassade händelser.
* Programloggar, ETW-loggar och diagnostikinfrastrukturloggar visas som spårningar.

Om du vill visa prestandaräknare och antalet händelser öppnar du [Metrics Explorer](app-insights-metrics-explorer.md) och lägger till ett nytt diagram:

![Azure-diagnostikdata](./media/app-insights-cloudservices/23-wad.png)

Använd [Sök](app-insights-diagnostic-search.md) eller en [Analytics-fråga](app-insights-analytics-tour.md) för att söka igenom de olika spårningsloggarna som skickas av Azure Diagnostics. Anta exempelvis att du har ett ohanterat undantag som gjorde att en roll kraschade och återanvändes. Den här informationen skulle visas i kanalen Program i Windows-händelseloggen. Du kan använda Sök för att granska felet i Windows-händelseloggen och visa den fullständiga stackspårningen för undantaget. Det gör det lättare att hitta grundorsaken till problemet.

![Söka i Azure Diagnostics](./media/app-insights-cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mer telemetri
I avsnitten nedan ser du hur du kan hämta ytterligare telemetri från olika aspekter av ditt program.

## <a name="track-requests-from-worker-roles"></a>Spåra begäranden från arbetsroller
I webbroller samlar modulen för begäranden automatiskt in data om HTTP-förfrågningar. Exempel på hur du kan åsidosätta standardinsamlingsbeteendet finns i [MVCWebRole-exemplet](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Du kan samla in prestanda från anrop till arbetsroller genom att spåra dem på samma sätt som HTTP-förfrågningar. I Application Insights mäter telemetritypen Begäran en enhet av det namngivna arbetet på serversidan som kan tidsbestämmas och som kan lyckas eller misslyckas separat. HTTP-förfrågningar kan samlas in automatiskt av SDK, men du kan också infoga egen kod för att spåra begäranden till arbetsroller.

Se de två exempelarbetsrollerna som instrumenteras till rapportförfrågningar: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) och [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Undantag
Information om hur du kan samla in ohanterade undantag från olika webbprogramtyper finns i [Monitoring Exceptions in Application Insights](app-insights-asp-net-exceptions.md) (Övervaka undantag i Application Insights).

Exempelwebbrollen har MVC5- och Web API 2-styrenheter. De ohanterade undantagen från de två samlas in med följande hanterare:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs)-konfigurationen finns [här](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) för MVC5-styrenheter
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs)-konfigurationen finns [här](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) för Web API 2-styrenheter

För arbetsroller finns det två sätt att spåra undantag:

* TrackException(ex)
* Om du har lagt till NuGet-paketet för Application Insights-spårningslyssnare kan du använda **System.Diagnostics.Trace** för att logga undantag. [Kodexempel.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

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

Du kan ange fler anpassade prestandaräknare eller andra Windows-prestandaräknare genom att redigera ApplicationInsights.config [som i det här exemplet](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Prestandaräknare](./media/app-insights-cloudservices/OLfMo2f.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Korrelerad telemetri för arbetsroller
Det är en omfattande diagnostikupplevelse där du kan se vad som ledde till en misslyckad begäran eller en begäran med lång svarstid. Med webbroller konfigurerar SDK automatiskt sambandet mellan relaterad telemetri. För arbetsroller kan du uppnå detta genom att använda en anpassad telemetriinitierare och ange ett gemensamt Operation.Id-kontextattribut för all telemetri. På så sätt kan du snabbt se om fördröjningen/felet orsakades på grund av ett beroende eller din kod. 

Så här gör du:

* Ange korrelations-ID:t i en CallContext som du ser [här](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). I detta fall använder vi ID:t för begäran som korrelations-ID
* Lägg till en anpassad TelemetryInitializer-implementering och ange Operation.Id till det correlationId som du angav ovan. Ett exempel finns här: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Lägg till den anpassade telemetriinitieraren. Du kan göra det i filen applicationinsights.config eller i koden som du ser [här](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)

Klart! Portalmiljön är redan utformad så att du snabbt ser all telemetri:

![Korrelerad telemetri](./media/app-insights-cloudservices/bHxuUhd.png)

## <a name="client-telemetry"></a>Klienttelemetri
[Lägg till JavaScript SDK till dina webbsidor][client] om du vill visa webbläsarbaserad telemetri, till exempel antal sidvisningar, inläsningstider för sidor och skriptundantag, samt om du vill skriva anpassad telemetri i dina sidskript.

## <a name="availability-tests"></a>Tillgänglighetstester
[Konfigurera webbtester][availability] för att se till att ditt program är aktivt och effektivt.

## <a name="display-everything-together"></a>Visa allt tillsammans
Du kan få en överblick över systemet genom att placera alla de viktigaste övervakningsdiagrammen på en enda [instrumentpanel](app-insights-dashboards.md). Du kan till exempel fästa antalet begäranden och fel för varje roll. 

Om systemet använder andra Azure-tjänster, till exempel Stream Analytics, kan du även lägga till övervakningsdiagrammen för dem. 

Om du har en mobilapp för klientsidan infogar du kod för att skicka anpassade händelser vid viktiga användaråtgärder och skapar en [HockeyApp-brygga](app-insights-hockeyapp-bridge-app.md). Skapa frågor i [Analytics](app-insights-analytics.md) för att visa antalet händelser och fäst dem på instrumentpanelen.

## <a name="example"></a>Exempel
[Exemplet](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) övervakar en tjänst som har en webbroll och två arbetsroller.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Undantaget ”metoden hittades inte” vid körning i Azure Cloud Services
Utvecklade du för .NET 4.6? 4.6 stöds inte automatiskt i Azure Cloud Services-roller. [Installera 4.6 för varje roll](../cloud-services/cloud-services-dotnet-install-dotnet.md) innan du kör din app.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg
* [Konfigurera Azure Diagnostics-överföring till Application Insights](app-insights-azure-diagnostics.md)
* [Automatisera genereringen av Application Insights-resurser](app-insights-powershell.md)
* [Automatisera Azure-diagnostik](app-insights-powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md 

