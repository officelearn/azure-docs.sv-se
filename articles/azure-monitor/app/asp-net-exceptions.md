---
title: Diagnostisera fel och undantag i web apps med Azure Application Insights | Microsoft Docs
description: Fånga undantag från ASP.NET-appar tillsammans med begärandetelemetri.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: 6f534858194719a0d3059808073ae057f150b599
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021024"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnostisera undantag i dina webbappar med Application Insights
Undantag i ditt live-webbapp som rapporteras av [Application Insights](../../application-insights/app-insights-overview.md). Du kan jämföra misslyckade begäranden med undantag och andra händelser på både klienten och servern, så att du kan snabbt diagnostisera orsakerna.

## <a name="set-up-exception-reporting"></a>Konfigurera undantag reporting
* Ha undantag som rapporterats från din serverapp:
  * Installera [Application Insights SDK](../../azure-monitor/app/asp-net.md) i din kod, eller
  * IIS-webbservrar: Kör [Application Insights-agenten](../../azure-monitor/app/monitor-performance-live-website-now.md); eller
  * Azure web apps: Lägg till den [Application Insights-tillägget](../../azure-monitor/app/azure-web-apps.md)
  * Java-webbappar: Installera den [Java-agenten](../../azure-monitor/app/java-agent.md)
* Installera den [JavaScript-kodavsnitt](../../azure-monitor/app/javascript.md) i dina webbsidor för att fånga webbläsarundantag som.
* I vissa programramverk eller med vissa inställningar måste du vidta ytterligare åtgärder för att fånga undantag som mer:
  * [Webbformulär](#web-forms)
  * [MVC](#mvc)
  * [Webb-API 1.*](#web-api-1x)
  * [Webb-API 2.*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnostisera undantag med hjälp av Visual Studio
Öppna app-lösningen i Visual Studio för att hjälpa till med felsökning.

Kör appen, antingen på servern eller på en utvecklingsdator med F5.

Öppnar sökfönstret för Application Insights i Visual Studio och ställas in att visa händelser från din app. Medan du felsöker, kan du göra detta genom att klicka på knappen Application Insights.

![Högerklicka på projektet och välj Application Insights, öppna.](./media/asp-net-exceptions/34.png)

Observera att du kan filtrera rapporten för att visa några undantag.

*Inga undantag som visar? Se [fånga in undantag](#exceptions).*

Klicka på en felrapport att visa dess stackspårning.
Klicka på en radreferens i stackspårningen att öppna filen relevanta koden.

Observera att CodeLens visar data om undantagen i koden:

![CodeLens meddelande om undantag.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnostisera fel med Azure-portalen
Application Insights levereras med en skyddad APM-erfarenhet för att hjälpa dig att diagnostisera fel i dina övervakade program. Börja genom att klicka på alternativet fel i Application Insights-resurs-menyn finns i avsnittet Undersök.
Du bör se en helskärmsläge som visar fel hastighet trender för dina begäranden, hur många av dem misslyckas och hur många användare som påverkas. Till höger visas några av de mest användbara distributionerna som är specifika för den valda misslyckas åtgärden, inklusive topp 3 svarskoder, topp 3 undantagstyper och översta 3 misslyckas Beroendetyper.

![Fel sortera vy (fliken åtgärder)](./media/asp-net-exceptions/FailuresTriageView.png)

Du kan sedan granska representativt exempel för var och en av dessa delmängder av åtgärder i ett enda klick. I synnerhet om du vill diagnostisera undantag, kan du klicka på antalet i ett visst undantag till visas ett undantag informationsbladet som följande:

![Undantag informationsblad](./media/asp-net-exceptions/ExceptionDetailsBlade.png)

**Du kan också** i stället för att titta på undantag för en specifik misslyckad åtgärd, som du kan starta från den övergripande vyn av undantag, genom att växla till fliken undantag:

![Fel sortera vy (fliken undantag)](./media/asp-net-exceptions/FailuresTriageView_Exceptions.png)

Här kan du se alla undantag som samlas in för övervakade appen.

*Inga undantag som visar? Se [fånga in undantag](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Anpassade spårning och loggdata
För att få diagnostikdata som är specifika för din app kan infoga du kod för att skicka din egen telemetridata. Detta visas i diagnostiksökning tillsammans med begäran, Sidvisning och andra automatiskt insamlade data.

Har du flera alternativ:

* [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) används vanligtvis för att övervaka användningsmönster, men den skickar även data visas under anpassade händelser i diagnostiksökning. Händelser är namngivna och kan utföra strängegenskaper och numeriska mått som du kan [filtrera din diagnostiksökningar](../../azure-monitor/app/diagnostic-search.md).
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) kan du skicka längre data, till exempel efter information.
* [TrackException()](#exceptions) skickar Stacka spårningar. [Mer om undantag](#exceptions).
* Om du redan använder ett loggningsramverk som Log4Net eller NLog, kan du [samla in dessa loggar](asp-net-trace-logs.md) och se dem i diagnostiksökning tillsammans med begäran och data.

Om du vill se de här händelserna, öppna [Search](../../azure-monitor/app/diagnostic-search.md), öppna Filter och välj sedan anpassad händelse eller spåra undantag.

![Visa detaljerad information](./media/asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Om din app genererar mycket telemetri minskar den anpassningsbara insamlingsmodulen automatiskt den mängd som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som ingår i samma åtgärd ska markeras eller avmarkeras som en grupp, så att du kan navigera mellan relaterade händelser. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Hur du visar begäran postdata
Information om begäran omfattar inte data som skickas till din app i en POST-anrop. Rapporterade att dessa data:

* [Installera SDK](../../azure-monitor/app/asp-net.md) i programprojektet.
* Infoga kod i ditt program att anropa [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Skicka POST-data i parametern meddelande. Det finns en gräns för den tillåtna storleken, så bör du försöka skicka bara viktiga data.
* När du undersöker en misslyckad begäran att hitta de associerade spårningarna.

![Visa detaljerad information](./media/asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> Fånga undantag och relaterade diagnostikdata
Först visas inte i portalen alla undantag som orsakar fel i din app. Du ser alla webbläsarundantag (om du använder den [JavaScript SDK](../../azure-monitor/app/javascript.md) på dina webbsidor). Men de flesta undantag som fångas av IIS och du måste skriva lite kod för att se dem.

Du kan:

* **Logga undantag uttryckligen** genom att infoga kod i undantagshanterare att rapportera undantagen.
* **Fånga in undantag automatiskt** genom att konfigurera din ASP.NET-ramverket. Av tillägg är olika för olika typer av ramverk.

## <a name="reporting-exceptions-explicitly"></a>Rapportering undantag uttryckligen
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

Egenskaper och mätningar av parametrar är valfria, men är användbara för [filtrering och lägga till](../../azure-monitor/app/diagnostic-search.md) extra information. Om du har en app som kan köra flera spel hitta du till exempel alla undantag rapporter som hör till ett visst spel. Du kan lägga till så många objekt som du vill att varje ordlista.

## <a name="browser-exceptions"></a>Webbläsarundantag
De flesta webbläsarundantag rapporteras.

Om din webbsida omfattar skriptfiler från nätverk för innehållsleverans eller andra domäner, kontrollera din skripttypen har attributet ```crossorigin="anonymous"```, och som skickar servern [CORS-huvuden](https://enable-cors.org/). Detta kan du få ett stackspårning och information för ohanterade JavaScript-undantag från dessa resurser.

## <a name="web-forms"></a>Webbformulär
HTTP-modul kommer att kunna samla in undantag när det finns inga omdirigeringar som konfigurerats med CustomErrors för web forms.

Men om du har active omdirigeringar, lägger du till följande rader till funktionen Application_Error i Global.asax.cs. (Lägg till en Global.asax-fil om du inte redan har en.)

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
Från och med Application Insights Web SDK version 2.6 (beta3 och senare), Application Insights samlar in ett ohanterat undantag i MVC 5 + domänkontrollanter metoder automatiskt. Om du har nyligen lagt till en anpassad hanterare för att spåra sådana undantag (som beskrivs i följande exempel), kan du ta bort den för att förhindra att dubbla spårning av undantag.

Det finns ett antal fall som undantag filter inte kan hantera. Exempel:

* Undantag från domänkontrollanten konstruktorer.
* Undantag från meddelandehanterare.
* Undantag vid routning.
* Undantag vid serialiseringssvar innehåll.
* Ett undantag genererades vid start av program.
* Ett undantag uppstod i bakgrundsaktiviteter.

Alla undantag *hanteras* av program fortfarande måste spåras manuellt.
Ohanterade undantag från domänkontrollanter vanligtvis ger 500 ”Internt serverfel”-svar. Om svaret skapas manuellt till följd av hanterade undantag (eller inget undantag alls) spåras i motsvarande begärandetelemetri med `ResultCode` 500, men Application Insights SDK kan inte spåra motsvarande undantag.

### <a name="prior-versions-support"></a>Stöd för tidigare versioner
Om du använder MVC 4 (och tidigare) för Application Insights Web SDK 2.5 (och tidigare), se följande exempel för att spåra undantag.

Om den [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) konfigurationen är `Off`, och sedan undantag blir tillgänglig för den [HTTP-modul](https://msdn.microsoft.com/library/ms178468.aspx) att samla in. Men om det är `RemoteOnly` (standard), eller `On`, så undantaget att vara avmarkerad och inte tillgänglig för Application Insights för att automatiskt samla in. Du kan åtgärda detta genom att åsidosätta den [System.Web.Mvc.HandleErrorAttribute klass](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx), och tillämpa klassen åsidosatt som visas för de olika MVC-versionerna nedan ([GitHub-källan](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

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
Ersätt attributet HandleError med din nya attribut i dina domänkontrollanter.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Exemplet](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registrera `AiHandleErrorAttribute` som ett globalt filter i Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Exemplet](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

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

[Exemplet](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Webb-API
Från och med Application Insights Web SDK version 2.6 (beta3 och senare), Application Insights samlar in ett ohanterat undantag i kontrollantmetoder automatiskt för WebAPI 2 +. Om du har nyligen lagt till en anpassad hanterare för att spåra sådana undantag (som beskrivs i följande exempel), kan du ta bort den för att förhindra att dubbla spårning av undantag.

Det finns ett antal fall som undantag filter inte kan hantera. Exempel:

* Undantag från domänkontrollanten konstruktorer.
* Undantag från meddelandehanterare.
* Undantag vid routning.
* Undantag vid serialiseringssvar innehåll.
* Ett undantag genererades vid start av program.
* Ett undantag uppstod i bakgrundsaktiviteter.

Alla undantag *hanteras* av program fortfarande måste spåras manuellt.
Ohanterade undantag från domänkontrollanter vanligtvis ger 500 ”Internt serverfel”-svar. Om svaret skapas manuellt till följd av hanterade undantag (eller inget undantag alls) spåras i en motsvarande begärandetelemetri med `ResultCode` 500, men Application Insights SDK kan inte spåra motsvarande undantag.

### <a name="prior-versions-support"></a>Stöd för tidigare versioner
Om du använder WebAPI-1 (och tidigare) Application Insights Web SDK 2.5 (och tidigare), se följande exempel för att spåra undantag.

#### <a name="web-api-1x"></a>Webb-API 1.x
Åsidosätt System.Web.Http.Filters.ExceptionFilterAttribute:

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

Du kan lägga till det här åsidosatt attributet till specifika domänkontrollanter eller lägger till den globala filter-konfigurationen i klassen WebApiConfig:

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

[Exemplet](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

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

Lägg till detta till tjänsterna i WebApiConfig:

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

[Exemplet](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Alternativt kan du:

1. Ersätt den enda ExceptionHandler med en anpassad implementering av IExceptionHandler. Detta är endast anropas när ramverket fortfarande kunna välja vilken svarsmeddelande att skicka (inte när anslutningen avbryts exempelvis)
2. Undantag filter (enligt beskrivningen i avsnittet om webb-API 1.x-styrenheter ovan) - inte anropas i samtliga fall.

## <a name="wcf"></a>WCF
Lägg till en klass som utökar Attribute och implementerar IErrorHandler och IServiceBehavior.

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

[Exemplet](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Prestandaräknare för undantag
Om du har [installerat Application Insights-agenten](../../azure-monitor/app/monitor-performance-live-website-now.md) på servern, du kan få ett diagram över undantagshastighet, mätt av .NET. Detta inkluderar både hanterade och ohanterade undantag i .NET.

Öppna ett Metric Explorer-blad, lägga till ett nytt diagram och välj **antal undantag**har beskrivits under prestandaräknare.

.NET framework beräknar hastigheten genom att räkna antalet undantag i ett intervall och dividera med längden på intervallet.

Detta skiljer sig från antalet ”undantag” beräknas genom att räkna TrackException rapporter Application Insights-portalen. Insamlingsintervallen är olika och SDK: N skickar inte TrackException rapporter för alla hanterade och ohanterade undantag.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
* [Övervaka REST, SQL och andra anrop till beroenden](../../azure-monitor/app/asp-net-dependencies.md)
* [Övervaka sidans belastning, webbläsarundantag och AJAX-anrop](../../azure-monitor/app/javascript.md)
* [Övervaka prestandaräknare](../../azure-monitor/app/performance-counters.md)
