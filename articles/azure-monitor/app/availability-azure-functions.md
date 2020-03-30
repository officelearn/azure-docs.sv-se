---
title: Skapa och köra anpassade tillgänglighetstester med Azure Functions
description: Det här dokumentet täcker hur du skapar en Azure-funktion med TrackAvailability() som körs regelbundet enligt konfigurationen som anges i TimerTrigger-funktionen. Resultatet av det här testet skickas till din Application Insights-resurs, där du kan fråga efter och avisera tillgänglighetsresultatdata. Anpassade tester gör att du kan skriva mer komplexa tillgänglighetstester än vad som är möjligt med hjälp av portalgränssnittet, övervaka en app inuti ditt Azure VNET, ändra slutpunktsadressen eller skapa ett tillgänglighetstest om det inte är tillgängligt i din region.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665807"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Skapa och köra anpassade tillgänglighetstester med Azure Functions

Den här artikeln beskriver hur du skapar en Azure-funktion med TrackAvailability() som körs regelbundet enligt konfigurationen som anges i TimerTrigger-funktionen med din egen affärslogik. Resultatet av det här testet skickas till din Application Insights-resurs, där du kan fråga efter och avisera tillgänglighetsresultatdata. På så sätt kan du skapa anpassade tester som liknar vad du kan göra via [tillgänglighetsövervakning](../../azure-monitor/app/monitor-web-app-availability.md) i portalen. Anpassade tester gör att du kan skriva mer komplexa tillgänglighetstester än vad som är möjligt med hjälp av portalgränssnittet, övervaka en app inuti ditt Azure VNET, ändra slutpunktsadressen eller skapa ett tillgänglighetstest även om den här funktionen inte är tillgänglig i din region.

> [!NOTE]
> Det här exemplet är utformat endast för att visa dig mekaniken i hur API-anropet TrackAvailability() fungerar inom en Azure-funktion. Inte hur man skriver den underliggande HTTP-testkod / affärslogik som skulle krävas för att förvandla detta till en fullt fungerande tillgänglighet test. Som standard om du går igenom det här exemplet kommer du att skapa ett tillgänglighetstest som alltid genererar ett fel.

## <a name="create-timer-triggered-function"></a>Skapa timerutlösad funktion

- Om du har en application insights-resurs:
    - Som standard Azure Functions skapar en Application Insights resurs men om du vill använda en av dina redan skapade resurser måste du ange det när du skapar.
    - Följ instruktionerna om hur du [skapar en Azure Functions-resurs och timerutlöst funktion](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppa innan rensning) med följande alternativ.
        -  Välj fliken **Övervakning** högst upp.

            ![ Skapa en Azure Functions-app med din egen App Insights-resurs](media/availability-azure-functions/create-function-app.png)

        - Välj listrutan Programstatistik och skriv eller välj namnet på din resurs.

            ![Välja befintlig application insights-resurs](media/availability-azure-functions/app-insights-resource.png)

        - Välj **Granska + skapa**
- Om du inte har skapat en application insights-resurs ännu för den utlösta timern-funktionen:
    - Som standard när du skapar ditt Azure Functions-program skapar den en Application Insights-resurs åt dig.
    - Följ instruktionerna om hur du [skapar en Azure Functions-resurs och timerutlöst funktion](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppa före rensning).

## <a name="sample-code"></a>Exempelkod

Kopiera koden nedan till filen run.csx (detta ersätter den befintliga koden). Det gör du genom att gå in i ditt Azure Functions-program och välja din timerutlösarfunktion till vänster.

>[!div class="mx-imgBorder"]
>![Azure-funktionens run.csx i Azure-portalen](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> För slutpunktsadressen som `EndpointAddress= https://dc.services.visualstudio.com/v2/track`du vill använda: . Såvida inte din resurs finns i en region som Azure Government eller Azure China, i vilket fall konsultera den här artikeln om [att åsidosätta standardslutpunkterna](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) och välja lämplig slutpunkt för Telemetrikanal för din region.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
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
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

Till höger under Visa filer väljer du **Lägg till**. Anropa den nya filen **function.proj** med följande konfiguration.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Lägg till till till höger. Namnge filen function.proj](media/availability-azure-functions/addfile.png)

Till höger under Visa filer väljer du **Lägg till**. Anropa den nya filen **runAvailabilityTest.csx** med följande konfiguration.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Kontrollera tillgänglighet

Om du vill vara säker på att allt fungerar kan du titta på diagrammet på fliken Tillgänglighet för din Application Insights-resurs.

> [!NOTE]
> Om du implementerade din egen affärslogik i runAvailabilityTest.csx så kommer du att se framgångsrika resultat som i skärmdumpar nedan, om du inte gjorde det kommer du att se misslyckade resultat.

>[!div class="mx-imgBorder"]
>![Fliken Tillgänglighet med lyckade resultat](media/availability-azure-functions/availtab.png)

När du konfigurerar testet med Azure Functions kommer du att märka att namnet på testet inte visas till skillnad från att använda Lägg till **test** på fliken Tillgänglighet och att du inte kommer att kunna interagera med det. Resultaten visualiseras men du får en sammanfattningsvy i stället för samma detaljerade vy som du får när du skapar ett tillgänglighetstest via portalen.

Om du vill visa transaktionsinformation från slutna till slutna data väljer du **Lyckad** eller **Misslyckad** under detaljgranskning och väljer sedan ett exempel. Du kan också komma till transaktionsinformationen från slutpunkt till slutpunkt genom att välja en datapunkt i diagrammet.

>[!div class="mx-imgBorder"]
>![Välj ett exempeltillgänglighetstest](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Transaktionsinformation från slutna till sluten tid](media/availability-azure-functions/end-to-end.png)

Om du körde allt som det är (utan att lägga till affärslogik) ser du att testet misslyckades.

## <a name="query-in-logs-analytics"></a>Fråga i loggar (Analytics)

Du kan använda Logs(analytics) för att visa tillgänglighetsresultat, beroenden med mera. Mer information om loggar finns i [Översikt över loggfrågor](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Tillgänglighetsresultat](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Beroenden](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Nästa steg

- [Programkarta](../../azure-monitor/app/app-map.md)
- [Transaktionsdiagnostik](../../azure-monitor/app/transaction-diagnostics.md)
