---
title: Övervaka användning och prestanda för Windows-appar
description: Analysera användning och prestanda för Windows-program med Application Insights.
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: eb9e0fc480098478a3a68265ac85e0d5450e27fe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537397"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Övervaka användning och prestanda för klassiska Windows-appar

Program som finns lokalt, i Azure och i andra moln kan dra nytta av Application Insights. Den enda begränsningen är behovet av att [tillåta kommunikation](../../azure-monitor/app/ip-addresses.md) till Application Insights-tjänsten. För övervakning av UWP-program (Universell Windows-plattform) rekommenderar vi [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Så här skickar du telemetri till Application Insights från ett klassiskt Windows-program
1. [Skapa en Application Insights-resurs](../../azure-monitor/app/create-new-resource.md ) på [Azure Portal](https://portal.azure.com). Välj ASP.NET-app för programtyp.
2. Kopiera instrumenteringsnyckeln. Hitta nyckeln i Essentials-listrutan för den nya resursen som du nyss skapade. 
3. Redigera NuGet-paketet av för ett programprojekt och lägg till Microsoft.ApplicationInsights.WindowsServer i Visual Studio. (Eller välj Microsoft.ApplicationInsights om du bara vill ha API, utan standardmoduler för telemetrisk insamling)
4. Ange antingen instrumentationsnyckeln i koden:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *din nyckel* `";`
   
    eller i ApplicationInsights.config (om du har installerat ett standard-telemetripaket):
   
    `<InstrumentationKey>`*din nyckel*`</InstrumentationKey>` 
   
    Om du använder ApplicationInsights.config, kontrollera att egenskaperna i Solution Explorer är inställda på **Byggåtgärd = Innehåll, Kopiera till utdatakatalog = Kopiera**.
5. [Använda API](../../azure-monitor/app/api-custom-events-metrics.md) att skicka telemetri.
6. Kör din app och se telemetrin i resursen som du skapade i Azure-portalen.

## <a name="example-code"></a><a name="telemetry"></a>Exempelkod
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

## <a name="override-storage-of-computer-name"></a>Åsidosätt lagring av datornamn

Som standard samlar denna SDK in och lagrar datornamnet på det system som avger telemetri. För att åsidosätta samlingen måste du använda en telemetriinitierare:

**Skriv anpassad TelemetryInitializer enligt nedan.**

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
Instansiera initializern `Program.cs` `Main()` i metoden nedan och ange instrumenteringsnyckeln:

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
* [Utforska mått](../../azure-monitor/platform/metrics-charts.md)
* [Skriv analysfrågor](../../azure-monitor/app/analytics.md)

