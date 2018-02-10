---
title: "Samla in på prestandaräknare i Azure-molntjänster | Microsoft Docs"
description: "Lär dig att upptäcka, använda och skapa prestandaräknare i Cloud Services med Azure Diagnostics och Application Insights."
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/18
ms.author: adegeo
ms.openlocfilehash: 3e0af48c172fa912f0ac9e05b7b761dd7eaad795
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Samla in prestandaräknare för Azure-molntjänst

Prestandaräknarna ger dig möjlighet att spåra hur väl dina program och värden utför. Windows Server innehåller många olika prestandaräknare som rör maskinvara, program, operativsystemet och mer. Du kan analysera informationen för att fatta bättre beslut genom att samla in och skicka prestandaräknare till Azure. 

## <a name="discover-available-counters"></a>Identifiera tillgängliga räknare

En prestandaräknare består av två delar, ett namn (även kallat en kategori) och en eller flera räknare. Du kan använda PowerShell för att hämta en lista över tillgängliga prestandaräknare:

```PowerShell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

Den `CounterSetName` egenskapen representerar en uppsättning (eller kategori) och är en bra indikator för räknarna som är relaterade till. Den `Paths` egenskap representerar en samling av räknare för en mängd. Du kan också få den `Description` -egenskapen för mer information om uppsättning räknare.

Om du vill hämta alla räknare för en uppsättning, använder den `CounterSetName` värde och expandera den `Paths` samling. Varje sökväg-objekt är en räknare som du kan fråga. Till exempel för att hämta tillgängliga räknare för den `Processor` ange, expandera den `Paths` samling:

```PowerShell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Dessa enskilda räknare sökvägar kan läggas till ramverk för Nätverksdiagnostik används för Molntjänsten. Mer information om hur en sökväg till prestandaräknaren är uppbyggd finns [att ange en räknarsökvägen](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Samla in en prestandaräknare

En prestandaräknare kan läggas till Molntjänsten för Azure-diagnostik eller Application Insights.

### <a name="application-insights"></a>Application Insights

Azure Application Insights för molntjänster kan du ange vilka prestandaräknare som du vill samla in. När du [lägga till Application Insights i ditt projekt](../application-insights/app-insights-cloudservices.md#sdk), en .config-fil med namnet **ApplicationInsights.config** har lagts till i Visual Studio-projekt. Den här konfigurationsfilen definierar vilken typ av information Application Insights samlar in och skickar till Azure.

Öppna den **ApplicationInsights.config** och leta reda på **ApplicationInsights** > **TelemetryModules** element. Varje `<Add>` underordnat element definierar en typ av telemetri för att samla in, tillsammans med dess konfiguration. Modultypen prestandaräknaren telemetri är `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Om det här elementet har redan definierats, Lägg inte till den en gång. Varje prestandaräknare för att samla in definieras under en nod med namnet `<Counters>`. Här är ett exempel som samlar in prestandaräknare för enhet:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Varje prestandaräknare representeras som en `<Add>` elementet under `<Counters>`. Den `PerformanceCounter` attribut definierar vilka prestandaräknare vill samla in. Den `ReportAs` attributet är det namn som ska visas i Azure-portalen för prestandaräknaren. Alla prestandaräknare som du samlar in placeras i en kategori med namnet **anpassade** i portalen. Till skillnad från Azure Diagnostics, kan du inte ange intervall dessa prestandaräknare som samlas in och skickas till Azure. Med Application Insights prestandaräknare som samlas in och skickas varje minut. 

Application Insights samlar automatiskt in följande prestandaräknare:

* \Process(??APP_WIN32_PROC??)\% Processortid
* \Memory\Tillgängliga byte
* \.NET CLR-undantag(??APP_CLR_PROC??)\# undantag som utlöses/sekund
* \Process(??APP_WIN32_PROC??)\Privata byte
* \Process(??APP_WIN32_PROC??)\Byte i I/O-data per sekund
* \Processor(_Total)\% processortid

Mer information finns i [prestandaräknare för System i Application Insights](../application-insights/app-insights-performance-counters.md) och [Programinsikter för Azure Cloud Services](../application-insights/app-insights-cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> När dessa data slås samman till lagringskontot på portalen har **inte** ett enhetligt sätt att diagrammets data. Vi rekommenderar starkt att du integrerar en annan Diagnostiktjänst som Application Insights i ditt program.

Azure-diagnostik-tillägget för molntjänster kan du ange vilka prestandaräknare som du vill samla in. Om du vill konfigurera Azure-diagnostik finns [Cloud Service övervakning-översikt](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

De prestandaräknare som du vill samla in definieras i den **diagnostics.wadcfgx** fil. Öppna filen (definieras per roll) i Visual Studio och Sök efter den **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **PerformanceCounters** element. Lägg till en ny **PerformanceCounterConfiguration** element som underordnad. Det här elementet har två attribut: `counterSpecifier` och `sampleRate`. Den `counterSpecifier` attribut definierar vilka systemprestanda räknaren ange (nedan i föregående avsnitt) att samla in. Den `sampleRate` värdet anger hur ofta värdet ska avsökas. Hela alla prestandaräknare som överförs till Azure enligt överordnat `PerformanceCounters` elementets `scheduledTransferPeriod` attributvärdet.

Mer information om den `PerformanceCounters` schemaelement, finns det [Azure Diagnostics schemat](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element).

Den period som definieras av den `sampleRate` attributet använder XML-varaktighet datatypen för att ange hur ofta prestandaräknaren ska avsökas. I exemplet nedan överföringshastighet anges till `PT3M`, vilket betyder att `[P]eriod[T]ime[3][M]inutes`: alla tre minuter.

Mer information om hur `sampleRate` och `scheduledTransferPeriod` är definierat, finns det **varaktighet datatyp** i avsnittet den [W3 XML-typerna Date och Time datum](https://www.w3schools.com/XML/schema_dtypes_date.asp) kursen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Skapa en ny perf-räknare

En ny prestandaräknare kan skapas och används av din kod. Koden som skapar en ny prestandaräknare måste köra upphöjd, annars misslyckas. Molntjänsten `OnStart` startkoden kan skapa prestandaräknaren, för att kräva att du kör rollen i en upphöjd kontext. Eller skapa en startåtgärd som kör upphöjd och skapar prestandaräknaren. Mer information om uppgifter för start finns [hur du konfigurerar och kör Start-aktiviteter för en molntjänst](cloud-services-startup-tasks.md).

Om du vill konfigurera din roll att köra utökade lägger du till en `<Runtime>` elementet så att den [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) fil.

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Du kan skapa och registrera en ny prestandaräknare med ett fåtal rader med kod. Använd den `System.Diagnostics.PerformanceCounterCategory.Create` metodöverlagringen som skapar både kategorin och räknaren. Följande kod kontrollerar först om den finns, och om de saknas, skapar både kategorin och räknaren.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the cateogry and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

När du vill använda räknaren anropa den `Increment` eller `IncrementBy` metod.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Nu när ditt program använder din anpassade räknare, måste du konfigurera Azure Diagnostics och Application Insights för att spåra räknaren.


### <a name="application-insights"></a>Application Insights

Som tidigare nämnts kan prestandaräknarna för Application Insights definieras i den **ApplicationInsights.config** fil. Öppna **ApplicationInsights.config** och Sök efter den **ApplicationInsights** > **TelemetryModules** > **Lägg till**  >  **Räknare** element. Skapa en `<Add>` underordnat element och ange den `PerformanceCounter` attributet kategori och namnet för prestandaräknare som du skapade i din kod. Ange den `ReportAs` attribut till ett eget namn som du vill se i portalen.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure Diagnostics

Som tidigare beskrivits prestandaräknare som du vill samla in definieras i den **diagnostics.wadcfgx** fil. Öppna filen (definieras per roll) i Visual Studio och Sök efter den **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg**  >  **DiagnosticMonitorConfiguration** > **PerformanceCounters** element. Lägg till en ny **PerformanceCounterConfiguration** element som underordnad. Ange den `counterSpecifier` attributet kategori och namnet för prestandaräknare som du skapade i din kod. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Mer information

- [Application Insights för Azure-molntjänster](../application-insights/app-insights-cloudservices.md#performance-counters)
- [Prestandaräknare för system i Application Insights](../application-insights/app-insights-performance-counters.md)
- [Ange en sökväg för räknaren](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure Diagnostics Schema - prestandaräknare](../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md#performancecounters-element)