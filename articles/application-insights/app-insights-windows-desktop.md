---
title: "Övervaka användning och prestanda för Windows-appar"
description: "Analysera användning och prestanda för Windows-program med HockeyApp och Application Insights."
services: application-insights
documentationcenter: windows
author: CFreemanwa
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 35ca040ed123f6330f09f7fb1bc6be9ddaf61808
ms.contentlocale: sv-se
ms.lasthandoff: 04/07/2017


---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Övervaka användning och prestanda för Windows-appar


Med [Azure Application Insights](app-insights-overview.md) och [HockeyApp](https://hockeyapp.net) kan du övervaka dina distribuerade program för användning och prestanda.

> [!IMPORTANT]
> Vi rekommenderar [HockeyApp](https://hockeyapp.net) för att distribuera och övervaka appar för dator eller mobila enheter. Med HockeyApp kan du hantera distribution, livetestning och användarfeedback samt övervaka användning och krasher. Du kan också [exportera och fråga din telemetri med Analytics](app-insights-hockeyapp-bridge-app.md).
> 
> Även om telemetri kan skickas till Application Insights från ett program, är detta huvudsakligen användbatr för felsökning och utveckling.
> 
> 

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
```C#

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


