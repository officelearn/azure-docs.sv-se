---
title: Diagnostisera fel och undantag med Azure Application Insights
description: Samla in undantag från ASP.NET appar tillsammans med begärandetelemetri.
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 9f24f09e7d2ef0a3e5f3a8f6546a9115118473ab
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892350"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnostisera undantag i dina webbappar med Application Insights
Undantag i din live-webbapp rapporteras av [Application Insights](../../azure-monitor/app/app-insights-overview.md). Du kan korrelera misslyckade begäranden med undantag och andra händelser på både klienten och servern, så att du snabbt kan diagnostisera orsakerna.

## <a name="set-up-exception-reporting"></a>Ställ in undantagsrapportering
* Så här har du rapporterat undantag från serverappen:
  * Azure-webbappar: Lägg till [tillägget Application Insights](../../azure-monitor/app/azure-web-apps.md)
  * Azure VM och Azure virtual machine scale set IIS-värdappar: Lägg till [tillägget programövervakning](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * Installera [Application Insights SDK](../../azure-monitor/app/asp-net.md) i din appkod, eller
  * IIS-webbservrar: Kör [Application Insights Agent](../../azure-monitor/app/monitor-performance-live-website-now.md); Eller
  * Java-webbappar: Aktivera [Java-agenten](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* Installera [JavaScript-kodavsnittet](../../azure-monitor/app/javascript.md) på webbsidorna för att fånga upp webbläsarundantag.
* I vissa programramverk eller med vissa inställningar måste du vidta några extra åtgärder för att fånga fler undantag:
  * [Webbformulär](#web-forms)
  * [MVC](#mvc)
  * [Webb-API 1.*](#web-api-1x)
  * [Webb-API 2.*](#web-api-2x)
  * [WCF](#wcf)

  Den här artikeln är särskilt inriktad på .NET Framework-appar från ett kodexempelperspektiv. Vissa av de metoder som fungerar för .NET Framework är föråldrade i .NET Core SDK. Läsa [.NET Core SDK-dokumentationen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) om du har en .NET Core-app.

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnostisera undantag med Visual Studio
Öppna applösningen i Visual Studio för att hjälpa till med felsökning.

Kör appen, antingen på servern eller på utvecklingsdatorn med hjälp av F5.

Öppna fönstret Sök i Application Insights i Visual Studio och ställ in det så att händelser från din app visas. Medan du felsöker kan du göra detta bara genom att klicka på knappen Programstatistik.

![Högerklicka på projektet och välj Application Insights, Open.](./media/asp-net-exceptions/34.png)

Observera att du kan filtrera rapporten så att den bara visar undantag.

*Inga undantag visar? Se [Fånga undantag](#exceptions).*

Klicka på en undantagsrapport om du vill visa dess stackspårning.
Klicka på en radreferens i stackspårningen för att öppna relevant kodfil.

I koden märker du att CodeLens visar data om undantagen:

![CodeLens anmälan av undantag.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnostisera fel med Azure-portalen
Application Insights levereras med en kurerad APM-upplevelse som hjälper dig att diagnostisera fel i dina övervakade program. Du vill starta genom att klicka på alternativet Fel på resursmenyn Programinsikter i avsnittet Undersök.
Du bör se en helskärmsvy som visar felfrekvenstrenderna för dina begäranden, hur många av dem som misslyckas och hur många användare som påverkas. Till höger visas några av de mest användbara distributionerna som är specifika för den valda misslyckade åtgärden, inklusive de tre översta svarskoderna, de tre översta undantagstyperna och de tre översta beroendetyperna.

![Triage-vyn för fel (fliken åtgärder)](./media/asp-net-exceptions/failures0719.png)

Med ett enda klick kan du sedan granska representativa exempel för var och en av dessa delmängder av åtgärder. För att diagnostisera undantag kan du särskilt klicka på antalet ett visst undantag som ska visas med fliken Heltäckande transaktionsinformation, till exempel den här:

![Fliken Fullständig transaktionsinformation](./media/asp-net-exceptions/end-to-end.png)

Alternativt kan **du,** i stället för att titta på undantag för en viss misslyckad åtgärd, utgå från den övergripande vyn av undantag genom att växla till fliken Undantag högst upp. Här kan du se alla undantag som samlats in för din övervakade app.

*Inga undantag visar? Se [Fånga undantag](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Anpassade spårnings- och loggdata
Om du vill få diagnostikdata som är specifika för din app kan du infoga kod för att skicka egna telemetridata. Detta visas i diagnostiksökning tillsammans med begäran, sidvisning och andra automatiskt insamlade data.

I det syftet har du flera alternativ:

* [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) används vanligtvis för att övervaka användningsmönster, men de data som skickas visas också under Anpassade händelser i diagnostiksökning. Händelser namnges och kan innehålla strängegenskaper och numeriska mått som du kan [filtrera diagnostiksökningar](../../azure-monitor/app/diagnostic-search.md)på .
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) kan du skicka längre data som POST-information.
* [TrackException()](#exceptions) skickar stackspårningar. [Mer om undantag](#exceptions).
* Om du redan använder ett loggningsramverk som Log4Net eller NLog kan du [samla in dessa loggar](asp-net-trace-logs.md) och se dem i diagnostiksökning tillsammans med begäran- och undantagsdata.

Om du vill se dessa händelser öppnar du [Sök](../../azure-monitor/app/diagnostic-search.md) på den vänstra menyn, väljer den nedrullningsbara menyn **Händelsetyper**och väljer sedan Anpassad händelse, Spårning eller Undantag.

![Visa detaljerad information](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Om din app genererar mycket telemetri minskar den anpassningsbara insamlingsmodulen automatiskt den mängd som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som ingår i samma åtgärd väljs eller avmarkeras som en grupp, så att du kan navigera mellan relaterade händelser. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Så här visar du post-data för begäran
Begär information inkluderar inte de data som skickas till din app i ett POST-samtal. Så här gör du om du vill att dessa data ska rapporteras:

* [Installera SDK](../../azure-monitor/app/asp-net.md) i ditt programprojekt.
* Infoga kod i programmet för att anropa [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Skicka POST-data i meddelandeparametern. Det finns en gräns för den tillåtna storleken, så du bör försöka skicka bara de viktigaste uppgifterna.
* När du undersöker en misslyckad begäran hittar du de associerade spårningarna.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a>Samla in undantag och relaterade diagnostikdata
Först ser du inte i portalen alla undantag som orsakar fel i din app. Du ser eventuella webbläsarundantag (om du använder [JavaScript SDK](../../azure-monitor/app/javascript.md) på dina webbsidor). Men de flesta server undantag fångas av IIS och du måste skriva lite kod för att se dem.

Du kan:

* **Logga uttryckligen undantag** genom att infoga kod i undantagshanterare för att rapportera undantagen.
* **Samla in undantag automatiskt** genom att konfigurera ASP.NET ramverk. De nödvändiga tilläggen är olika för olika typer av ramar.

## <a name="reporting-exceptions-explicitly"></a>Rapporteringsund undantag uttryckligen
Det enklaste sättet är att infoga ett anrop till TrackException() i en undantagshanterare.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

Parametrarna för egenskaper och mått är valfria, men är användbara för [filtrering och extra](../../azure-monitor/app/diagnostic-search.md) information. Om du till exempel har en app som kan köra flera spel kan du hitta alla undantagsrapporter som är relaterade till ett visst spel. Du kan lägga till så många objekt som du vill i varje ordlista.

## <a name="browser-exceptions"></a>Webbläsarundantag
De flesta webbläsarundantag rapporteras.

Om webbsidan innehåller skriptfiler från innehållsleveransnätverk eller andra domäner ```crossorigin="anonymous"```kontrollerar du att skripttaggen har attributet och att servern skickar [CORS-huvuden](https://enable-cors.org/). Detta gör att du kan få en stack spårning och detalj för ohanterade JavaScript undantag från dessa resurser.

## <a name="reuse-your-telemetry-client"></a>Återanvända telemetriklienten

> [!NOTE]
> TelemetriClient rekommenderas att instansieras en gång och återanvändas under hela livslängden för ett program.

Nedan finns ett exempel med TelemetryClient korrekt.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Webbformulär
För webbformulär kan HTTP-modulen samla in undantagen när det inte finns några omdirigeringar konfigurerade med CustomErrors.

Men om du har aktiva omdirigeringar lägger du till följande rader i Application_Error funktion i Global.asax.cs. (Lägg till en Global.asax-fil om du inte redan har en.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```
## <a name="mvc"></a>MVC
Från och med Application Insights Web SDK version 2.6 (beta3 och senare), samlar Application Insights unhandled undantag som genereras i MVC 5 + styrenheter metoder automatiskt. Om du tidigare har lagt till en anpassad hanterare för att spåra sådana undantag (enligt beskrivningen i följande exempel) kan du ta bort den för att förhindra dubbel spårning av undantag.

Det finns ett antal fall som undantagsfiltren inte kan hantera. Ett exempel:

* Undantag från styrenhetskonstruktorer.
* Undantag som genereras från meddelandehanterare.
* Undantag som genereras under routning.
* Undantag som genereras under serialisering av svarsinnehåll.
* Undantag som genereras vid start av program.
* Undantag som genereras i bakgrundsuppgifter.

Alla undantag *som hanteras* av programmet måste fortfarande spåras manuellt.
Ohanterade undantag från styrenheter resulterar vanligtvis i 500 "Internal Server Error" svar. Om ett sådant svar konstrueras manuellt som ett resultat av hanterat undantag (eller inget `ResultCode` undantag alls) spåras det i motsvarande begärantelemetri med 500, men Application Insights SDK kan inte spåra motsvarande undantag.

### <a name="prior-versions-support"></a>Stöd för tidigare versioner
Om du använder MVC 4 (och tidigare) i Application Insights Web SDK 2.5 (och föregående) läser du följande exempel för att spåra undantag.

Om [CustomErrors-konfigurationen](https://msdn.microsoft.com/library/h0hfz6fc.aspx) är `Off`kommer undantag att vara tillgängliga för [HTTP-modulen](https://msdn.microsoft.com/library/ms178468.aspx) att samla in. Men om det `RemoteOnly` är (standard), eller `On`, kommer undantaget att rensas och inte vara tillgängligt för Application Insights att automatiskt samla in. Du kan åtgärda det genom att åsidosätta [klassen System.Web.Mvc.HandleErrorAttribute](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)och tillämpa den åsidosatta klassen som visas för de olika MVC-versionerna nedan ([GitHub-källa):](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Ersätt HandleError-attributet med det nya attributet i handkontrollerna.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Prov](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registrera `AiHandleErrorAttribute` dig som ett globalt filter i Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Prov](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Registrera AiHandleErrorAttribute som ett globalt filter i FilterConfig.cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[Prov](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Webb-API
Från och med Application Insights Web SDK version 2.6 (beta3 och senare), samlar Application Insights ohanterade undantag som genereras i styrenheten metoder automatiskt för WebAPI 2 +. Om du tidigare har lagt till en anpassad hanterare för att spåra sådana undantag (enligt beskrivningen i följande exempel) kan du ta bort den för att förhindra dubbel spårning av undantag.

Det finns ett antal fall som undantagsfiltren inte kan hantera. Ett exempel:

* Undantag från styrenhetskonstruktorer.
* Undantag som genereras från meddelandehanterare.
* Undantag som genereras under routning.
* Undantag som genereras under serialisering av svarsinnehåll.
* Undantag som genereras vid start av program.
* Undantag som genereras i bakgrundsuppgifter.

Alla undantag *som hanteras* av programmet måste fortfarande spåras manuellt.
Ohanterade undantag från styrenheter resulterar vanligtvis i 500 "Internal Server Error" svar. Om ett sådant svar konstrueras manuellt som ett resultat av hanterat undantag (eller inget undantag `ResultCode` alls) spåras det i en motsvarande begärantelemetri med 500, men Application Insights SDK kan inte spåra motsvarande undantag.

### <a name="prior-versions-support"></a>Stöd för tidigare versioner
Om du använder WebAPI 1 (och tidigare) av Application Insights Web SDK 2.5 (och föregående) läser du följande exempel för att spåra undantag.

#### <a name="web-api-1x"></a>Webb-API 1.x
Åsidosätt system.web.http.filters.exceptionfilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Du kan lägga till det här åsidosatta attributet till specifika styrenheter eller lägga till det i den globala filterkonfigurationen i klassen WebApiConfig:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[Prov](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Webb-API 2.x
Lägg till en implementering av IExceptionLogger:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Lägg till detta i tjänsterna i WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[Prov](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Som alternativ kan du:

1. Ersätt den enda ExceptionHandler med en anpassad implementering av IExceptionHandler. Detta anropas bara när ramverket fortfarande kan välja vilket svarsmeddelande som ska skickas (inte när anslutningen avbryts till exempel)
2. Undantagsfilter (enligt beskrivningen i avsnittet om Web API 1.x-styrenheter ovan) – anropas inte i alla fall.

## <a name="wcf"></a>WCF
Lägg till en klass som utökar attributet och implementerar IErrorHandler och IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[Prov](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Prestandaräknare för undantag
Om du har [installerat Application Insights Agent](../../azure-monitor/app/monitor-performance-live-website-now.md) på servern kan du få ett diagram över undantagsfrekvensen, mätt med .NET. Detta inkluderar både hanterade och ohanterade .NET-undantag.

Öppna fliken Metric Explorer, lägg till ett nytt diagram och välj **Undantagsfrekvens**, som visas under Prestandaräknare.

.NET-ramverket beräknar hastigheten genom att räkna antalet undantag i ett intervall och dividera med intervallets längd.

Detta skiljer sig från antalet undantag som beräknas av Application Insights-portalen som räknar TrackException-rapporter. Samplingsintervallen är olika och SDK skickar inte TrackException-rapporter för alla hanterade och ohanterade undantag.

## <a name="next-steps"></a>Nästa steg
* [Övervaka REST, SQL och andra anrop till beroenden](../../azure-monitor/app/asp-net-dependencies.md)
* [Övervaka sidans laddningstider, webbläsarundantag och AJAX-anrop](../../azure-monitor/app/javascript.md)
* [Övervaka prestandaräknare](../../azure-monitor/app/performance-counters.md)
