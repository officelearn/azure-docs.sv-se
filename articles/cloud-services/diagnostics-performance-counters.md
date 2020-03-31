---
title: Samla in prestandaräknare i Azure Cloud Services | Microsoft-dokument
description: Lär dig hur du upptäcker, använder och skapar prestandaräknare i Molntjänster med Azure Diagnostics och Application Insights.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 02/02/2018
ms.author: tagore
ms.openlocfilehash: 3b4028a09f69acd5d7a6579b4610785ed32e227d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469535"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Samla in prestandaräknare för din Azure Cloud Service

Prestandaräknare är ett sätt för dig att spåra hur bra ditt program och värden presterar. Windows Server innehåller många olika prestandaräknare som är relaterade till maskinvara, program, operativsystem med mera. Genom att samla in och skicka prestandaräknare till Azure kan du analysera den här informationen för att fatta bättre beslut. 

## <a name="discover-available-counters"></a>Upptäck tillgängliga räknare

En prestandaräknare består av två delar, ett antett namn (kallas även en kategori) och en eller flera räknare. Du kan använda PowerShell för att få en lista över tillgängliga prestandaräknare:

```powershell
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

Egenskapen `CounterSetName` representerar en uppsättning (eller kategori) och är en bra indikator på vad prestandaräknare är relaterade till. Egenskapen `Paths` representerar en samling räknare för en uppsättning. Du kan också `Description` få fastigheten för mer information om räknaren.

Om du vill hämta alla räknare `CounterSetName` för en `Paths` uppsättning använder du värdet och expanderar samlingen. Varje sökvägsobjekt är en räknare som du kan fråga efter. Om du till exempel vill hämta `Processor` tillgängliga räknare `Paths` som är relaterade till uppsättningen expanderar du samlingen:

```powershell
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

Dessa enskilda räknarvägar kan läggas till i diagnostikramverket som molntjänsten använder. Mer information om hur en prestandaräknaresökväg skapas finns i [Ange en räknarebana](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Samla in en prestandaräknare

En prestandaräknare kan läggas till i din molntjänst för antingen Azure Diagnostics eller Application Insights.

### <a name="application-insights"></a>Application Insights

Med Azure Application Insights för Molntjänster kan du ange vilka prestandaräknare du vill samla in. När du [har lagt till Application Insights i projektet](../azure-monitor/app/cloudservices.md#sdk)läggs en config-fil med namnet **ApplicationInsights.config** till i Visual Studio-projektet. Den här konfigurationsfilen definierar vilken typ av information Application Insights samlar in och skickar till Azure.

Öppna filen **ApplicationInsights.config** och leta reda på elementet **ApplicationInsights** > **TelemetryModules.** Varje `<Add>` underordnadt element definierar en typ av telemetri att samla in, tillsammans med dess konfiguration. Modulen för telemetrimodul `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`för prestandaräknare är . Om det här elementet redan har definierats ska du inte lägga till det en andra gång. Varje prestandaräknare som ska samlas in `<Counters>`definieras under en nod med namnet . Här är ett exempel som samlar in enhetsprestandaräknare:

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

Varje prestandaräknare representeras `<Add>` som `<Counters>`ett element under . Attributet `PerformanceCounter` definierar vilken prestandaräknare som ska samlas in. Attributet `ReportAs` är titeln som ska visas i Azure-portalen för prestandaräknaren. Alla prestandaräknare som du samlar in läggs i en kategori med namnet **Anpassad** i portalen. Till skillnad från Azure Diagnostics kan du inte ange intervallet dessa prestandaräknare samlas in och skickas till Azure. Med Application Insights samlas prestandaräknare in och skickas varje minut. 

Application Insights samlar automatiskt in följande prestandaräknare:

* \Process(?? APP_WIN32_PROC??) \% Processortid
* \Memory\Tillgängliga byte
* \.NET CLR-undantag(??APP_CLR_PROC??)\# undantag som utlöses/sekund
* \Process(??APP_WIN32_PROC??)\Privata byte
* \Process(??APP_WIN32_PROC??)\Byte i I/O-data per sekund
* \Processor(_Total)\% processortid

Mer information finns [i Systemprestandaräknare i Application Insights](../azure-monitor/app/performance-counters.md) och [Application Insights för Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

> [!IMPORTANT]
> Alla dessa data sammanställs i lagringskontot, men portalen tillhandahåller **inte** ett eget sätt att kartlägga data. Vi rekommenderar starkt att du integrerar en annan diagnostiktjänst, till exempel Application Insights, i ditt program.

Azure Diagnostics-tillägget för Molntjänster kan du ange vilka prestandaräknare du vill samla in. Information om hur du konfigurerar Azure Diagnostics finns i [Översikt över molntjänstövervakning](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

De prestandaräknare som du vill samla in definieras i filen **diagnostics.wadcfgx.** Öppna den här filen (den definieras per roll) i Visual Studio och hitta elementet **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Lägg till ett nytt **PerformanceCounterConfiguration-element** som underordnad. Det här elementet `counterSpecifier` har `sampleRate`två attribut: och . Attributet `counterSpecifier` definierar vilken systemprestandaräknareuppsättning (som beskrivs i föregående avsnitt) som ska samlas in. Värdet `sampleRate` anger hur ofta värdet är avsökning. Som helhet överförs alla prestandaräknare till Azure enligt `PerformanceCounters` det `scheduledTransferPeriod` överordnade elementets attributvärde.

Mer information om `PerformanceCounters` schemaelementet finns i [Azure Diagnostics Schema](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element).

Den period som `sampleRate` definieras av attributet använder datatypen XML-varaktighet för att ange hur ofta prestandaräknaren avsöks. I exemplet nedan är hastigheten `PT3M`inställd på `[P]eriod[T]ime[3][M]inutes`, vilket innebär : var tredje minut.

Mer information om `sampleRate` hur `scheduledTransferPeriod` och definieras finns i avsnittet **Varaktighetsdatatyp** i självstudiekursen [W3 XML-datum och tidsdatum.](https://www.w3schools.com/XML/schema_dtypes_date.asp)

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

En ny prestandaräknare kan skapas och användas av koden. Koden som skapar en ny prestandaräknare måste köras upphöjd, annars misslyckas den. Startkoden `OnStart` för molntjänsten kan skapa prestandaräknaren, vilket kräver att du kör rollen i en förhöjd kontext. Du kan också skapa en startuppgift som körs upphöjd och skapar prestandaräknaren. Mer information om startuppgifter finns i [Konfigurera och köra startuppgifter för en molntjänst](cloud-services-startup-tasks.md).

Om du vill konfigurera rollen så `<Runtime>` att den körs upphöjd lägger du till ett element i [CSDEF-filen.](cloud-services-model-and-package.md#servicedefinitioncsdef)

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

Du kan skapa och registrera en ny prestandaräknare med några rader kod. Använd `System.Diagnostics.PerformanceCounterCategory.Create` metodöverbelastningen som skapar både kategorin och räknaren. Följande kod kontrollerar först om kategorin finns, och om den saknas, skapas både kategorin och räknaren.

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

            // Define the category and counter names.
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

Anropa räknaren när `Increment` `IncrementBy` du vill använda räknaren.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Nu när ditt program använder din anpassade räknare måste du konfigurera Azure Diagnostics eller Application Insights för att spåra räknaren.


### <a name="application-insights"></a>Application Insights

Som tidigare nämnts definieras prestandaräknarna för Application Insights i filen **ApplicationInsights.config.** Öppna **ApplicationInsights.config** och hitta **applicationinsights** > **telemetrimoduler** > lägga**till** > **räknare** elementet. Skapa `<Add>` ett underordnat `PerformanceCounter` element och ange attributet till kategorin och namnet på prestandaräknaren som du skapade i koden. Ange `ReportAs` attributet till ett eget namn som du vill se i portalen.

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

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

Som tidigare nämnts definieras prestandaräknarna som du vill samla in i filen **diagnostics.wadcfgx.** Öppna den här filen (den definieras per roll) i Visual Studio och hitta elementet **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters.** Lägg till ett nytt **PerformanceCounterConfiguration-element** som underordnad. Ange `counterSpecifier` attributet till kategorin och namnet på prestandaräknaren som du skapade i koden. 

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

- [Application Insights för Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)
- [Systemprestandaräknare i Application Insights](../azure-monitor/app/performance-counters.md)
- [Ange en motbana](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure Diagnostics-schema – prestandaräknare](../azure-monitor/platform/diagnostics-extension-schema-windows.md#performancecounters-element)



