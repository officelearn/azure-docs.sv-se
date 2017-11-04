---
title: Diagnostisera fel och undantag i web apps med Azure Application Insights | Microsoft Docs
description: "Fånga undantag från ASP.NET appar tillsammans med begärandetelemetri."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: cb87b166a32c47395f99c9cd59442a7ccd65b7ed
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnostisera undantag i ditt webbprogram med Application Insights
Undantag i livewebbappar rapporteras av [Programinsikter](app-insights-overview.md). Du kan jämföra misslyckade begäranden med undantag och andra händelser på både klienten och servern, så att du snabbt kan diagnostisera orsaker.

## <a name="set-up-exception-reporting"></a>Konfigurera undantag reporting
* Ha undantag som rapporterats från din server:
  * Installera [Application Insights SDK](app-insights-asp-net.md) i din Appkod eller
  * IIS-webbservrar: kör [Application Insights Agent](app-insights-monitor-performance-live-website-now.md); eller
  * Azure-webbappar: lägga till den [Application Insights Extension](app-insights-azure-web-apps.md)
  * Java-webbappar: installera den [Java-agent](app-insights-java-agent.md)
* Installera den [JavaScript-kodfragment](app-insights-javascript.md) på webbsidorna för att fånga undantag från webbläsaren.
* I vissa ramverk för programmet eller med vissa inställningar måste du vidta ytterligare åtgärder för att fånga flera undantag:
  * [Webbformulär](#web-forms)
  * [MVC](#mvc)
  * [Webb-API-1.*](#web-api-1x)
  * [Webb-API 2.*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnostisera undantag med Visual Studio
Öppna appen lösningen i Visual Studio för att hjälpa dig med felsökning.

Kör appen på din server eller på utvecklingsdatorn med F5.

Öppna fönstret Application Insights i Visual Studio och ställas in att visa händelser från din app. När du felsöker kan du göra detta genom att klicka på knappen Application Insights.

![Högerklicka på projektet och välj Application Insights, öppna.](./media/app-insights-asp-net-exceptions/34.png)

Observera att du kan filtrera rapporten ska visa bara undantag.

*Inga undantag visar? Se [fånga undantag](#exceptions).*

Klicka på en felrapport att visa dess stack-spårning.
Klicka på en radreferens i stackspårningen att öppna filen relevant kod.  

Observera att CodeLens visar information om undantagen i koden:

![CodeLens meddelande om undantag.](./media/app-insights-asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnostisera fel i Azure Portal
Application Insights levereras med en granskad APM-upplevelse som hjälper dig att diagnostisera fel i övervakade program. Klicka på alternativet fel i menyn Application Insights-resursen finns i avsnittet Undersök. Du bör se en helskärmsläge som visar fel hastighet trender för dina begäranden, hur många av dem misslyckas och hur många användare påverkas. Till höger visas några av de mest användbara distributioner som är specifika för den valda misslyckas åtgärden, inklusive upp 3 svarskoder, top 3 undantag typer och översta 3 misslyckas depedency typer. 

![Fel prioritering vy (operations fliken)](./media/app-insights-asp-net-exceptions/FailuresTriageView.png)

Du kan sedan granska representativa prover för var och en av dessa delar av operations i ett enda klick. I synnerhet kan du klicka på antalet i ett visst undantag till visas med ett undantag informationsbladet som den här för att diagnostisera undantag:

![Undantag informationsbladet](./media/app-insights-asp-net-exceptions/ExceptionDetailsBlade.png)

**Du kan också** i stället för att titta på undantag för en specifik misslyckas åtgärden som du kan starta från den övergripande vy av undantag, genom att växla till fliken undantag:

![Fel prioritering vy (undantag fliken)](./media/app-insights-asp-net-exceptions/FailuresTriageView_Exceptions.png)

Här kan du se alla undantag som samlas in för övervakade appen.

*Inga undantag visar? Se [fånga undantag](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Anpassade spårning och loggdata
Du kan infoga kod för att skicka telemetridata för att få diagnostiska uppgifter som är specifika för din app. Detta visas i diagnostiska tillsammans med begäran, vyn sida och andra automatiskt insamlade data.

Har du flera alternativ:

* [Trackevent ()](app-insights-api-custom-events-metrics.md#trackevent) används vanligtvis för att övervaka användningsmönster, men data som skickas också visas under Anpassad händelser i diagnostiska sökningen. Händelser är namngivna och kan utföra strängegenskaper och numeriska mått som du kan [filtrera sökningen diagnostiska](app-insights-diagnostic-search.md).
* [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) kan du skicka längre data, till exempel efter information.
* [TrackException()](#exceptions) skickar Stacka spårningar. [Mer information om undantag](#exceptions).
* Om du redan använder ett loggningsramverk som Log4Net eller NLog, kan du [samla in dessa loggar](app-insights-asp-net-trace-logs.md) och se dem i diagnostiska sökningen tillsammans med data för begäran och undantag.

Om du vill se händelserna [Sök](app-insights-diagnostic-search.md), öppna Filter och välj sedan Custom Event, spårning eller undantag.

![Detaljvisning](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Om din app genererar mycket telemetri minskar den anpassningsbara insamlingsmodulen automatiskt den mängd som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som ingår i samma åtgärd ska markeras eller avmarkeras som en grupp, så att du kan navigera mellan relaterade händelser. [Läs mer om sampling.](app-insights-sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Hur man ser begäran postdata
Information om begäran innehåller inte data som skickas till din app i en POST-anrop. Rapporterade att dessa data:

* [Installera SDK](app-insights-asp-net.md) i projektet program.
* Infoga kod i ditt program att anropa [Microsoft.ApplicationInsights.TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace). Skicka efter data i parametern meddelandet. Det finns en gräns för den tillåtna storleken du ska försöka skicka bara viktiga data.
* När du undersöker en misslyckad begäran att hitta de associerade spårningar.  

![Detaljvisning](./media/app-insights-asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a>Fånga undantag och relaterade diagnostikdata
Först visas inte i portalen alla undantag som orsakar fel i din app. Du ser alla webbläsarundantag (om du använder den [JavaScript SDK](app-insights-javascript.md) på webbsidorna). Men de flesta undantag fångas av IIS och du måste skriva koden för att se dem lite.

Du kan:

* **Logga undantag uttryckligen** genom att infoga kod i undantagshanterare att rapportera undantagen.
* **Fånga undantag automatiskt** genom att konfigurera ASP.NET framework. De nödvändiga tilläggen är olika för olika typer av framework.

## <a name="reporting-exceptions-explicitly"></a>Rapportering undantag uttryckligen
Det enklaste sättet är att infoga ett anrop till TrackException() i en undantagshanterare.

JavaScript

    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }

C#

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

VB

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

Egenskaper och mätningar parametrar är valfria, men är användbara för [filtrering och lägga till](app-insights-diagnostic-search.md) extra information. Om du har en app som kan köra flera spel hitta du till exempel alla undantag rapporter som hör till ett visst spel. Du kan lägga till så många objekt som du vill att varje ordlista.

## <a name="browser-exceptions"></a>Webbläsarundantag
De flesta webbläsarundantag rapporteras.

Om din webbsida innehåller skriptfiler från nätverk för innehållsleverans eller andra domäner, kontrollera din skripttypen har attributet ```crossorigin="anonymous"```, och att servern skickar [CORS huvuden](http://enable-cors.org/). Detta kan du få ett stack-spårning och detaljer för ohanterade JavaScript-undantag från dessa resurser.

## <a name="web-forms"></a>Webbformulär
HTTP-modul kommer att kunna samla in undantag när det finns inga omdirigeringar som konfigurerats med CustomErrors för web forms.

Men om du har active omdirigeringar, lägger du till följande rader funktionen Application_Error i Global.asax.cs. (Lägg till en Global.asax-fil om du inte redan har ett.)

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## <a name="mvc"></a>MVC
Om den [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) konfigurationen är `Off`, undantag ska vara tillgängliga för den [HTTP-modul](https://msdn.microsoft.com/library/ms178468.aspx) att samla in. Men om det är `RemoteOnly` (standard), eller `On`, undantaget ska vara avmarkerad och inte tillgängliga för Application Insights att automatiskt samla in. Du kan åtgärda detta genom att åsidosätta den [System.Web.Mvc.HandleErrorAttribute klassen](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx), och tillämpa åsidosatt klassen som visas för de olika MVC-versionerna nedan ([github-källan](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

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

#### <a name="mvc-2"></a>MVC 2
Ersätt attributet HandleError med din nya attribut i dina domänkontrollanter.

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[Exempel](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registrera `AiHandleErrorAttribute` som ett globalt filter i Global.asax.cs:

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[Exempel](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Registrera AiHandleErrorAttribute som ett globalt filter i FilterConfig.cs:

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[Exempel](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api-1x"></a>Webb-API 1.x
Åsidosätt System.Web.Http.Filters.ExceptionFilterAttribute:

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

Du kan lägga till den här åsidosatt attribut till specifika domänkontrollanter eller lägga till den globala filterkonfiguration i klassen WebApiConfig:

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

[Exempel](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

Det finns ett antal fall som undantagsfilter inte kan hantera. Exempel:

* Undantag från domänkontrollanten konstruktorer.
* Undantag från meddelandehanterare.
* Undantag under routning.
* Undantag under innehåll serialiseringssvar.

## <a name="web-api-2x"></a>Webb-API 2.x
Lägg till en implementering av IExceptionLogger:

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

Lägg till det här tjänsterna i WebApiConfig:

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

[Exempel](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Alternativt kan du:

1. Ersätt endast ExceptionHandler med en anpassad implementering av IExceptionHandler. Detta är endast anropas när ramen är fortfarande kunna välja vilken svarsmeddelande att skicka (inte när anslutningen avbryts exempelvis)
2. Undantagsfilter (enligt beskrivningen i avsnittet Web API 1.x domänkontrollanter ovan) - inte anropas i samtliga fall.

## <a name="wcf"></a>WCF
Lägg till en klass som utökar Attribute och implementerar IErrorHandler och IServiceBehavior.

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

Lägg till attributet implementeringar tjänsten:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...

[Exempel](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Prestandaräknare för undantag
Om du har [installerat Application Insights Agent](app-insights-monitor-performance-live-website-now.md) på servern, du kan få ett diagram över undantag hastighet, mätt av .NET. Detta inkluderar både hanterade och ohanterade undantag för .NET.

Öppna ett mått Explorer-bladet, lägga till ett nytt diagram och välj **undantag hastighet**, listade under prestandaräknare.

.NET framework beräknar hastigheten genom att räkna antalet undantag i ett intervall och dividera med längden på intervallet.

Detta skiljer sig från antalet 'Undantag' beräknas av Application Insights-portalen inventering TrackException rapporter. Insamlingsintervallen är olika och SDK skickar inte TrackException rapporter för alla hanterade och ohanterade undantag.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="next-steps"></a>Nästa steg
* [Övervaka REST, SQL och andra anrop till beroenden](app-insights-asp-net-dependencies.md)
* [Övervaka sidinläsningstider, webbläsarundantag och AJAX-anrop](app-insights-javascript.md)
* [Övervaka prestandaräknare](app-insights-performance-counters.md)
