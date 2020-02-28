---
title: Övervaka användning och prestanda för Windows-appar
description: Analysera användning och prestanda för Windows-program med Application Insights.
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 8234b9ba2c92fc64cfa8f598db99954e00caab45
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670839"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Övervaka användning och prestanda för klassiska Windows-appar

Program som finns lokalt, i Azure och i andra moln kan dra nytta av Application Insights. Den enda begränsningen är behovet av att [tillåta kommunikation](../../azure-monitor/app/ip-addresses.md) till Application Insights-tjänsten. För övervakning av UWP-program (Universell Windows-plattform) rekommenderar vi [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Så här skickar du telemetri till Application Insights från ett klassiskt Windows-program
1. [Skapa en Application Insights-resurs](https://portal.azure.com) på [Azure Portal](../../azure-monitor/app/create-new-resource.md ). Välj ASP.NET-app för programtyp.
2. Kopiera instrumenteringsnyckeln. Hitta nyckeln i Essentials-listrutan för den nya resursen som du nyss skapade. 
3. Redigera NuGet-paketet av för ett programprojekt och lägg till Microsoft.ApplicationInsights.WindowsServer i Visual Studio. (Eller välj Microsoft.ApplicationInsights om du bara vill ha API, utan standardmoduler för telemetrisk insamling)
4. Ange antingen instrumentationsnyckeln i koden:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *din nyckel* `";`
   
    eller i ApplicationInsights.config (om du har installerat ett standard-telemetripaket):
   
    `<InstrumentationKey>`*din nyckel*`</InstrumentationKey>` 
   
    Om du använder ApplicationInsights.config, kontrollera att egenskaperna i Solution Explorer är inställda på **Byggåtgärd = Innehåll, Kopiera till utdatakatalog = Kopiera**.
5. [Använda API](../../azure-monitor/app/api-custom-events-metrics.md) att skicka telemetri.
6. Kör din app och se Telemetrin i resursen som du skapade i Azure Portal.

## <a name="telemetry"></a>Exempelkod
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>Åsidosätt lagring av dator namn

Som standard samlar denna SDK upp och lagrar dator namnet för telemetri för system som du avger. Om du vill åsidosätta samlingen måste du använda en telemetri-initierare:

**Skriv anpassade TelemetryInitializer enligt nedan.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
Instansiera initieraren i metoden `Program.cs` `Main()` nedan och ange Instrumentation-nyckeln:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>Nästa steg
* [Skapa en instrumentpanel](../../azure-monitor/app/overview-dashboard.md)
* [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)
* [Utforska mått](../../azure-monitor/app/metrics-explorer.md)
* [Skriv analysfrågor](../../azure-monitor/app/analytics.md)

