---
title: 'Skicka användar kontext-ID: n för att aktivera användnings upplevelser i Azure Application Insights | Microsoft Docs'
description: Spåra hur användare går igenom tjänsten genom att tilldela var och en av dem en unik, beständig ID-sträng i Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: cf639be5db90e3632b8931564ac397c42e1d8403
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899367"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Skicka användar kontext-ID: n för att aktivera användnings upplevelser i Azure Application Insights

## <a name="tracking-users"></a>Spåra användare

Med Application Insights kan du övervaka och spåra dina användare via en uppsättning produkt användnings verktyg:

- [Användare, sessioner, händelser](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Trattar](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Kvarhållning](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Kohorter
- [Arbetsböcker](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

För att kunna spåra vad en användare använder med tiden behöver Application Insights ett ID för varje användare eller session. Inkludera följande ID: n i varje anpassad händelse-eller sid visning.

- Användare, trattar, kvarhållning och kohorter: inkludera användar-ID.
- Sessioner: ta med sessions-ID.

> [!NOTE]
> Det här är en avancerad artikel som beskriver de manuella stegen för att spåra användar aktivitet med Application Insights. Med många webb program behöver **de här stegen inte utföras**eftersom standard-SDK: er för Server sidan tillsammans med [klient-och webb läsar sidans JavaScript SDK](../../azure-monitor/app/website-monitoring.md )är ofta tillräckliga för att automatiskt spåra användar aktivitet. Om du inte har konfigurerat [övervakning på klient sidan](../../azure-monitor/app/website-monitoring.md ) utöver SDK för Server sidan, gör du det först och testar för att se om verktygen för användar beteende analys fungerar som förväntat.

## <a name="choosing-user-ids"></a>Välja användar-ID

Användar-ID: n bör bevaras över användarsessioner som spårar hur användare beter sig över tid. Det finns olika metoder för att bevara ID: t.

- En definition av en användare som du redan har i din tjänst.
- Om tjänsten har åtkomst till en webbläsare kan den skicka en cookie med ett ID i webbläsaren. ID: t är kvar så länge cookien finns kvar i användarens webbläsare.
- Om det behövs kan du använda ett nytt ID varje session, men resultatet om användarna är begränsat. Du kan till exempel inte se hur en användares beteende förändras över tid.

ID: t ska vara ett GUID eller en annan sträng som är tillräckligt komplex för att identifiera varje användare unikt. Det kan till exempel vara ett långt slumptal.

Om ID: t innehåller personligt identifierad information om användaren, är det inte ett lämpligt värde att skicka till Application Insights som ett användar-ID. Du kan skicka ett sådant ID som ett [autentiserat användar-ID](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), men det uppfyller inte användar-ID-kravet för användnings scenarier.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET appar: Ange användar kontexten i en ITelemetryInitializer

Skapa en telemetri-initierare enligt beskrivningen i detalj [här](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer). Skicka sessions-ID: t via telemetri för begäran och ange Context.User.Id och Context.Session.Id.

I det här exemplet anges användar-ID: t till en identifierare som upphör att gälla efter sessionen. Använd om möjligt ett användar-ID som finns kvar mellan sessioner.

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

- Börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sid visningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)om du vill aktivera användnings upplevelser.
- Om du redan skickar anpassade händelser eller sid visningar, utforska användnings verktygen för att lära dig hur användarna använder tjänsten.
    - [Översikt över användning](usage-overview.md)
    - [Användare, sessioner och händelser](usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](usage-retention.md)
    - [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
