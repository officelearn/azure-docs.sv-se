---
title: Övervaka användning och prestanda för Windows-appar
description: Analysera användning och prestanda för Windows-program med Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/16/2018
ms.author: mbullwin
ms.openlocfilehash: 5b325fd4326f2594a7386c65dea17a3da19abde8
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Övervaka användning och prestanda för Windows-appar

Även om telemetri kan skickas till Application Insights från ett program, är detta huvudsakligen användbatr för felsökning och utveckling.

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Skicka telemetri till Application Insights från ett Windows-program
1. [Skapa en Application Insights-resurs](app-insights-create-new-resource.md) på [Azure Portal](https://portal.azure.com). Välj ASP.NET-app för programtyp.
2. Kopiera instrumenteringsnyckeln. Hitta nyckeln i Essentials-listrutan för den nya resursen som du nyss skapade. 
3. Redigera NuGet-paketet av för ett programprojekt och lägg till Microsoft.ApplicationInsights.WindowsServer i Visual Studio. (Eller välj Microsoft.ApplicationInsights om du bara vill ha API, utan standardmoduler för telemetrisk insamling)
4. Ange antingen instrumentationsnyckeln i koden:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *din nyckel* `";` 
   
    eller i ApplicationInsights.config (om du har installerat ett standard-telemetripaket):
   
    `<InstrumentationKey>`*din nyckel*`</InstrumentationKey>` 
   
    Om du använder ApplicationInsights.config, kontrollera att egenskaperna i Solution Explorer är inställda på **Byggåtgärd = Innehåll, Kopiera till utdatakatalog = Kopiera**.
5. [Använda API](app-insights-api-custom-events-metrics.md) att skicka telemetri.
6. Kör din app och se telemetri i den resurs du skapat i Azure Portal.

## <a name="telemetry"></a>Exempelkod
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Nästa steg
* [Skapa en instrumentpanel](app-insights-dashboards.md)
* [Diagnostiksökning](app-insights-diagnostic-search.md)
* [Utforska mått](app-insights-metrics-explorer.md)
* [Skriv analysfrågor](app-insights-analytics.md)

