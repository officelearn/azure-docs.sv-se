---
title: 'Skicka användarkontext ID: N till att aktivera funktioner i Azure Application Insights | Microsoft Docs'
description: Spåra hur användarna flyttar din tjänst genom att tilldela dem en unik, beständig ID-sträng i Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: mbullwin;abgreg
ms.openlocfilehash: 196eeb7b5a817ff932f99c7db86ead5625b5f206
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Skicka användarkontext ID: N för att aktivera användning upplevelser i Azure Application Insights

## <a name="tracking-users"></a>Spåra användare

Application Insights kan du övervaka och spåra dina användare via en uppsättning av verktyg för användning av produkten: 
* [Användare, sessioner, händelser](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Trattar](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Kvarhållning](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Kohorter
* [Arbetsböcker](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

För att kunna spåra en användare gör över tid, måste Application Insights ett ID för varje användare eller en session. Inkludera följande-ID i varje vy händelse eller sidan.
- Användare, skorstenar, kvarhållning och kohort: inkludera användar-ID.
- Sessioner: Inkludera sessions-ID.

Om din app är integrerat med den [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), user ID spåras automatiskt.

## <a name="choosing-user-ids"></a>Om du väljer användar-ID

Användar-ID ska spara över användarsessioner att spåra funktionssättet för användare över tid. Det finns olika sätt att spara ID.
- En definition av en användare som du redan har i din tjänst.
- Om tjänsten har åtkomst till en webbläsare, kan skickas webbläsaren en cookie med ID i den. ID: T finns kvar för länge cookien i användarens webbläsare.
- Om det behövs kan du använda ett nytt ID varje session, men resultat om användarna kommer att vara begränsad. Till exempel inte du vill se hur en användarbeteende förändras över tid.

ID ska vara ett Guid eller en annan sträng som är tillräckligt komplext för att unikt identifiera varje användare. Exempelvis kan det vara ett långt slumptal.

Om ID: T innehåller personlig information om användaren, är inte ett lämpligt värde att skicka till Application Insights som ett användar-ID. Du kan skicka sådan ID som en [autentiserat användar-ID](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), men den uppfyller inte kravet på användar-ID för Användningsscenarier.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET-appar: inställningen användarkontexten i en ITelemetryInitializer

Skapa en telemetri initieraren som beskrivs i detalj [här](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer), och ange Context.User.Id och Context.Session.Id.

Det här exemplet anger användar-ID till en identifierare som upphör att gälla efter sessionen. Använd om möjligt ett användar-ID som kvarstår mellan sessioner.

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
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>Nästa steg
- Om du vill aktivera användning upplevelser börja skicka [anpassade händelser](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) eller [sidvisningar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Om du skickar redan anpassade händelser eller sidvisningar, utforska verktygen användning om du vill veta hur användarna använder din tjänst.
    * [Översikt över användning](app-insights-usage-overview.md)
    * [Användare, sessioner och händelser](app-insights-usage-segmentation.md)
    * [Trattar](usage-funnels.md)
    * [Kvarhållning](app-insights-usage-retention.md)
    * [Arbetsböcker](app-insights-usage-workbooks.md)
