---
title: 'Skicka användarkontext ID: N för att aktivera användning upplevelser i Azure Application Insights | Microsoft Docs'
description: Spåra hur användarna flyttar via tjänsten genom att tilldela dem en unik, permanent ID-sträng i Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/02/2017
ms.reviewer: abgreg
ms.author: mbullwin
ms.openlocfilehash: 14322162d3f78f0cb90ecaf077d1d85f7cbba581
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649644"
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Skicka användarkontext ID: N för att aktivera användning upplevelser i Azure Application Insights

## <a name="tracking-users"></a>Spåra användare

Application Insights kan du övervaka och spåra dina användare via en uppsättning verktyg för användning av produkten: 
* [Användare, sessioner, händelser](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Trattar](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Kvarhållning](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Kohorter
* [Arbetsböcker](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

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

Skapa en telemetri-initierare som beskrivs i detalj [här](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer), och ange Context.User.Id och Context.Session.Id.

Det här exemplet anger användar-ID till en identifierare som upphör att gälla efter sessionen. Använd om möjligt ett användar-ID som bevaras mellan sessioner.

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
- Om du redan skicka anpassade händelser eller sidvisningar, utforska användningsverktygen om du vill veta hur användarna använder din tjänst.
    * [Användningsöversikten](app-insights-usage-overview.md)
    * [Användare, sessioner och händelser](app-insights-usage-segmentation.md)
    * [Trattar](usage-funnels.md)
    * [Kvarhållning](app-insights-usage-retention.md)
    * [Arbetsböcker](app-insights-usage-workbooks.md)
