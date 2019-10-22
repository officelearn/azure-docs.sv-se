---
title: Skapa och kör anpassade tillgänglighets test med Azure Functions
description: Det här dokumentet beskriver hur du skapar en Azure-funktion med TrackAvailability () som körs regelbundet enligt konfigurationen i funktionen TimerTrigger. Resultatet av det här testet skickas till din Application Insights-resurs, där du kan fråga efter och Avisera om tillgänglighets resultat data. Med anpassade tester kan du skriva mer komplexa tillgänglighets test än vad som är möjligt med hjälp av Portal gränssnittet, övervaka en app inuti ditt Azure VNET, ändra slut punkts adressen eller skapa ett tillgänglighets test om det inte är tillgängligt i din region.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 10/11/2019
ms.openlocfilehash: a2b29f2c24a3244cdc800d882e7c2ed8f943eabe
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677589"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Skapa och kör anpassade tillgänglighets test med Azure Functions

Den här artikeln beskriver hur du skapar en Azure-funktion med TrackAvailability () som ska köras regelbundet enligt konfigurationen i funktionen TimerTrigger. Resultatet av det här testet skickas till din Application Insights-resurs, där du kan fråga efter och Avisera om tillgänglighets resultat data. På så sätt kan du skapa anpassade tester som liknar vad du kan göra via [tillgänglighets övervakning](../../azure-monitor/app/monitor-web-app-availability.md) i portalen. Med anpassade tester kan du skriva mer komplexa tillgänglighets test än vad som är möjligt med hjälp av Portal gränssnittet, övervaka en app inuti ditt Azure VNET, ändra slut punkts adressen eller skapa ett tillgänglighets test även om den här funktionen inte är tillgänglig i din region.


## <a name="create-timer-triggered-function"></a>Skapa timer-utlöst funktion

- Om du har en Application Insights-resurs:
    - Som standard skapar Azure Functions en Application Insights resurs, men om du vill använda en av dina redan skapade resurser måste du ange att du under genereringen.
    - Följ anvisningarna för hur du [skapar en funktion som utlöses av en Azure Functions resurs och timer](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppa före rensning) med följande val.
        -  Klicka på avsnittet Application Insights innan du väljer **skapa**.

            ![ Skapa en Azure Functions-app med din egen App Insights-resurs](media/availability-azure-functions/create-function-app.png)

        - Klicka på **Välj befintlig resurs** och skriv namnet på din resurs. Välj **Använd**

            ![Välja befintlig Application Insights resurs](media/availability-azure-functions/app-insights-resource.png)

        - Välj **Skapa**
- Om du inte har en Application Insights-resurs som har skapats ännu för din timer-utlöst funktion:
    - Som standard när du skapar ditt Azure Functions program skapas en Application Insights resurs åt dig.
    - Följ anvisningarna för hur du [skapar en Azure Functions resurs och timer-utlöst funktion](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppa före rensning).

## <a name="sample-code"></a>Exempelkod

Kopiera koden nedan till filen Run. CSX (det ersätter den befintliga koden). Det gör du genom att gå till Azure Functions programmet och välja funktionen timer-utlösare till vänster.

![Azure Functions Run. CSX i Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> För den slut punkts adress som du skulle använda: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Om inte din resurs finns i en region som Azure Government eller Azure Kina, i vilket fall läser du den här artikeln om hur [du åsidosätter standard slut punkterna](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) och väljer lämplig telemetri-kanal slut punkt för din region.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");

    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }

    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";

    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

Till höger under Visa filer väljer du **Lägg till**. Anropa den nya fil **funktionen. proj** med följande konfiguration.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![Till höger väljer du Lägg till. Namnge fil funktionen. proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Kontrol lera tillgänglighet

För att se till att allt fungerar kan du titta på diagrammet på fliken tillgänglighet i din Application Insights-resurs.

![Fliken tillgänglighet med lyckade resultat](media/availability-azure-functions/availtab.png)

När du ställer in testet med Azure Functions kan du se att namnet på testet inte visas och att du inte kan interagera med det om du vill använda **Lägg till test** på fliken tillgänglighet. Resultaten visualiseras men du får en sammanfattningsvy i stället för samma detaljerade vy som du får när du skapar ett tillgänglighets test via portalen.

Om du vill se en transaktions information från slut punkt till slut punkt väljer du **lyckades** eller **misslyckades** under detalj granskning till och väljer sedan ett exempel. Du kan också komma till transaktions informationen från slut punkt till slut punkt genom att välja en data punkt i diagrammet.

![Välj ett exempel på tillgänglighets test](media/availability-azure-functions/sample.png)

![Transaktions information från slut punkt till slut punkt](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Fråga i loggar (analys)

Du kan använda loggar (analys) om du vill visa tillgänglighets resultat, beroenden och mer. Mer information om loggar finns i [Översikt över logg frågor](../../azure-monitor/log-query/log-query-overview.md).

![Tillgänglighets resultat](media/availability-azure-functions/availabilityresults.png)

![Beroenden](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Nästa steg

- [Programkarta](../../azure-monitor/app/app-map.md)
- [Transaktions diagnostik](../../azure-monitor/app/transaction-diagnostics.md)
