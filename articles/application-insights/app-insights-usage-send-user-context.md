---
title: 'Skicka användarkontext ID: N för att aktivera användning upplevelser i Azure Application Insights | Microsoft Docs'
description: Spåra hur användarna flyttar via tjänsten genom att tilldela dem en unik, permanent ID-sträng i Application Insights.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/02/2017
ms.reviewer: abgreg;mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 5a224d7a78e35776b36b162228f6ba4c72677069
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636211"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Skicka användarkontext ID: N för att aktivera användning upplevelser i Azure Application Insights

## <a name="tracking-users"></a>Spåra användare

Application Insights kan du övervaka och spåra dina användare via en uppsättning verktyg för användning av produkten:

- [Användare, sessioner, händelser](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Trattar](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Kvarhållning](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) kohorter
- [Arbetsböcker](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

För att kunna spåra vad en användare gör med tiden, måste ett ID för varje användare eller en session i Application Insights. Inkludera följande-ID i varje anpassad händelse eller sidvisning.

- Användare, Trattar, kvarhållning och kohorter: inkludera användar-ID.
- : Kurserna sessions-ID.

Om din app är integrerad med den [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), user ID spåras automatiskt.

## <a name="choosing-user-ids"></a>Välja användar-ID

Användar-ID ska sparas i användarsessioner att spåra hur användarna ska fungera över tid. Det finns olika metoder för att spara ID.

- En definition av en användare som du redan har i din tjänst.
- Om tjänsten har åtkomst till en webbläsare, kan det skicka webbläsaren en cookie med ID i den. ID: T finns kvar för så länge cookien finns kvar i användarens webbläsare.
- Om det behövs kan du använda ett nytt ID varje session, men resultatet om användare begränsas. Exempelvis kan du inte se hur en användares beteende ändras över tid.

ID: T ska vara ett Guid eller en annan sträng som är tillräckligt komplext för att unikt identifiera varje användare. Exempelvis kan det vara ett långt slumptal.

Om det ID: T innehåller personlig information om användaren kan är det inte ett lämpligt värde att skicka till Application Insights som ett användar-ID. Du kan skicka sådana ett ID som en [autentiserad användar-ID](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), men den inte uppfyller kravet på användar-ID för Användningsscenarier.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET-appar: Ange användarkontexten i en ITelemetryInitializer

Skapa en telemetri-initierare som beskrivs i detalj [här](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer). Skicka sessions-ID via begärandetelemetri som och ange Context.User.Id och Context.Session.Id.

Det här exemplet anger användar-ID till en identifierare som upphör att gälla efter sessionen. Använd om möjligt ett användar-ID som bevaras mellan sessioner.

### <a name="telemetry-initializer"></a>Telemetri-initierare

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

- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du redan skicka anpassade händelser eller sidvisningar, utforska användningsverktygen om du vill veta hur användarna använder din tjänst.
    - [Användningsöversikten](app-insights-usage-overview.md)
    - [Användare, sessioner och händelser](app-insights-usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](app-insights-usage-retention.md)
    - [Arbetsböcker](app-insights-usage-workbooks.md)
