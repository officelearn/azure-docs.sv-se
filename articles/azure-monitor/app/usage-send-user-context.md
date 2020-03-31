---
title: Användarkontext-ID:er för att spåra aktivitet - Azure Application Insights
description: Spåra hur användare flyttar genom din tjänst genom att tilldela var och en av dem en unik, beständig ID-sträng i Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: beb5a0f7ad3733aaf12b0880af4fba23a705a7e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670941"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Skicka användarkontext-ID:er för att aktivera användningsupplevelser i Azure Application Insights

## <a name="tracking-users"></a>Spåra användare

Med Application Insights kan du övervaka och spåra användarna genom en uppsättning verktyg för produktanvändning:

- [Användare, sessioner, händelser](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Trattar](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Lagring](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Kohorter
- [Arbetsböcker](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

För att spåra vad en användare gör över tid behöver Application Insights ett ID för varje användare eller session. Inkludera följande ID:er i varje anpassad händelse eller sidvy.

- Användare, trattar, kvarhållning och kohorter: Inkludera användar-ID.
- Sessioner: Inkludera sessions-ID.

> [!NOTE]
> Detta är en avancerad artikel som beskriver de manuella stegen för att spåra användaraktivitet med Application Insights. Med många webbprogram **kan dessa steg inte krävas**, eftersom standardserversidan SDK:er tillsammans med [JavaScript SDK på klienten/webbläsaren](../../azure-monitor/app/website-monitoring.md )är ofta tillräckliga för att automatiskt spåra användaraktivitet. Om du inte har konfigurerat [övervakning på klientsidan](../../azure-monitor/app/website-monitoring.md ) utöver SDK på serversidan gör du det först och testar för att se om analysverktygen för användarbeteende fungerar som förväntat.

## <a name="choosing-user-ids"></a>Välja användar-ID:er

Användar-ID:er bör finnas kvar över användarsessioner för att spåra hur användare beter sig över tid. Det finns olika metoder för att bevara ID.

- En definition av en användare som du redan har i din tjänst.
- Om tjänsten har tillgång till en webbläsare kan den skicka webbläsaren en cookie med ett ID i den. ID:t kvarstår så länge cookien finns kvar i användarens webbläsare.
- Om det behövs kan du använda ett nytt ID varje session, men resultaten om användarna kommer att begränsas. Du kan till exempel inte se hur en användares beteende förändras med tiden.

ID:t ska vara ett Guid eller en annan strängkomplex som är tillräckligt för att identifiera varje användare unikt. Det kan till exempel vara ett långt slumptal.

Om ID:et innehåller personligt identifierande information om användaren är det inte ett lämpligt värde att skicka till Application Insights som användar-ID. Du kan skicka ett sådant ID som ett [autentiserat användar-ID,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users)men det uppfyller inte användar-ID-kravet för användningsscenarier.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET appar: Ställa in användarkontexten i en ITelemetryInitializer

Skapa en telemetriinitierare enligt beskrivningen i detalj [här](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer). Skicka sessions-ID:t via e-metrin och ange Context.User.Id och Context.Session.Id.

I det här exemplet anges användar-ID:t till en identifierare som upphör att gälla efter sessionen. Använd om möjligt ett användar-ID som finns kvar mellan sessioner.

### <a name="telemetry-initializer"></a>Telemetriinitierare

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera användningsupplevelser börjar du skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan har skickat anpassade händelser eller sidvisningar kan du utforska användningsverktygen för att ta reda på hur användarna använder tjänsten.
    - [Översikt över användning](usage-overview.md)
    - [Användare, sessioner och händelser](usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](usage-retention.md)
    - [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
