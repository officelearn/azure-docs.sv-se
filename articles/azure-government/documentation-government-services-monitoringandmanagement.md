---
title: Azure Government Övervakning och hantering | Microsoft Docs
description: Detta ger en jämförelse av funktioner och vägledning om hur du utvecklar program för Azure Government.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: 38eb44e6355ddcb5ac78f5d8bb2008ea5a0f0cdf
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127943"
---
# <a name="azure-government-monitoring--management"></a>Azure Government Övervakning och hantering
Den här artikeln beskriver skillnaderna och överväganden för övervakning och hantering av tjänster i Azure Governments miljön.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Advisor
Advisor är allmänt tillgänglig i Azure Government.

Mer information finns i [offentliga rådgivare-dokumentation](../advisor/advisor-overview.md).

### <a name="variations"></a>Olika
Följande rekommendationer för rådgivare är för närvarande inte tillgängliga i Azure Government:

* Hög tillgänglighet
  * Konfigurera din VPN-gateway till aktiv-aktiv för anslutnings återhämtning
  * Skapa Azure Service Health aviseringar för att bli informerad när Azure-problem påverkar dig
  * Konfigurera Traffic Manager slut punkter för återhämtning
  * Använd mjuk borttagning för ditt Azure Storage-konto
* Prestanda
  * Förbättra App Service prestanda och tillförlitlighet
  * Minska DNS-tiden till att leva i Traffic Managers profilen för att växla över till felfria slut punkter snabbare
  * Förbättra SQL Data Warehouse prestanda
  * Använda Premium Storage
  * Migrera ditt lagrings konto till Azure Resource Manager
* Kostnad
  * Köp reserverade instanser av virtuella datorer för att spara pengar jämfört med betala per användning-kostnad
  * Eliminera ExpressRoute-kretsar som inte har etablerats
  * Ta bort eller konfigurera om inaktiva virtuella nätverksgatewayer

Den beräkning som används för att rekommendera att du ska ha rätt storlek eller stänga av underutnyttjade virtuella datorer är följande i Azure Government:

Advisor övervakar användningen av den virtuella datorn i 7 dagar och identifierar virtuella datorer med låg användning. Virtuella datorer betraktas som en låg belastning om processor användningen är 5% eller mindre och användningen av nätverket är mindre än 2% eller om den aktuella arbets belastningen kan hanteras av en mindre virtuell dator storlek. Om du vill vara mer aggressiv vid identifiering av underutnyttjade virtuella datorer kan du justera processor användnings regeln per prenumeration.

## <a name="automation"></a>Automation
Automation är allmänt tillgängligt i Azure Government.

Mer information finns i [offentlig dokumentation för Automation](../automation/automation-intro.md).

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate är allmänt tillgänglig i Azure Government.

Mer information finns i [Azure Migrate-dokumentationen](../migrate/migrate-overview.md).

### <a name="variations"></a>Olika
Följande Azure Migrate funktioner är för närvarande inte tillgängliga i Azure Government:

* Beroende visualiserings funktioner är inte tillgängliga i Azure Government eftersom Azure Migrate är beroende av Tjänstkarta för beroende visualisering som för närvarande inte är tillgänglig i Azure Government.
* Du kan bara skapa utvärderingar för Azure Government som mål regioner och använda Azure Government erbjudanden.

## <a name="backup"></a>Säkerhetskopiering
Backup är allmänt tillgängligt i Azure Government.

Mer information finns i [Azure Government säkerhets kopiering](documentation-government-services-backup.md).

## <a name="policy"></a>Princip
Principen är allmänt tillgänglig i Azure Government.

Mer information finns i [Azure Policy](../governance/policy/overview.md).

## <a name="site-recovery"></a>Webbplatsåterställning
Azure Site Recovery är allmänt tillgänglig i Azure Government.

Mer information finns i [Site Recovery kommersiell dokumentation](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Olika
Följande Site Recovery funktioner är för närvarande inte tillgängliga i Azure Government:
* E-postavisering

| Webbplatsåterställning | Klassisk | Resource Manager |
| --- | --- | --- |
| VMware/fysisk  | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) |
| Hyper-V | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) |
| Plats till plats | Allmän tillgänglighet (GA) | Allmän tillgänglighet (GA) |

Följande URL: er för Site Recovery skiljer sig i Azure Government:

| Azure Public | Azure Government | Anteckningar |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*. hypervrecoverymanager.windowsazure.us | Åtkomst till tjänsten Site Recovery |
| \*.backup.windowsazure.com  | \*. backup.windowsazure.us | Åtkomst till skydds tjänsten |
| \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | För lagring av ögonblicks bilder av virtuella datorer |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Hämta MySQL |

## <a name="monitor"></a>Övervaka
Azure Monitor är allmänt tillgänglig i Azure Government.

Mer information finns i [övervaka kommersiell dokumentation](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview).

### <a name="variations"></a>Olika
Följande avsnitt innehåller information om skillnader och lösningar för Azure Monitor i Azure Government:

#### <a name="action-groups"></a>Åtgärdsgrupper
Åtgärds grupper är allmänt tillgängliga i Azure Government utan skillnader från kommersiella Azure.   

#### <a name="activity-log-alerts"></a>Aktivitetsloggaviseringar
Aktivitets logg aviseringar är allmänt tillgängliga i Azure Government utan skillnader från kommersiella Azure.

#### <a name="alerts-experience"></a>Aviserings upplevelse
Det enhetliga gränssnittet för aviseringar är tillgängligt för mått-och logg aviseringar i Azure Government.

#### <a name="autoscale"></a>Automatisk skalning
Autoskalning är allmänt tillgängligt i Azure Government.

Om du använder PowerShell/ARM/REST-anrop för att ange inställningar anger du "location" för autoskalning till "USGov Virginia" eller "USGov Iowa". Den resurs som är riktad mot autoskalning kan finnas i vilken region som helst. Ett exempel på inställningen är nedan:

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Om du är intresse rad av att implementera autoskalning på dina resurser kan du ange inställningarna med hjälp av PowerShell/ARM/rest-anrop.

Mer information om hur du använder PowerShell finns i [offentlig dokumentation](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings).

#### <a name="metrics"></a>Mått
Mått är allmänt tillgängliga i Azure Government. Det finns dock stöd för flerdimensionella mått via REST API. Möjligheten att [Visa flerdimensionella mått](../azure-monitor/platform/metrics-charts.md) är i för hands version i Azure Government portalen.

#### <a name="metric-alerts"></a>Måttaviseringar
Den första generationen av mått varningar är allmänt tillgänglig i både Azure Government och kommersiella Azure. Den första generationen kallas *aviseringar (klassisk)* . Den andra generationen av mått varningar (även kallat [enhetlig aviserings upplevelse](../azure-monitor/platform/alerts-overview.md)) är nu också tillgänglig, men med en reducerad uppsättning resurs leverantörer [jämfört med det offentliga molnet](../azure-monitor/platform/alerts-metric-near-real-time.md). Fler kommer att läggas till med tiden. 

De resurser som för närvarande stöds i den andra generationens aviserings upplevelser är:
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft. DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft. EventGrid/Domains
- Microsoft. EventGrid/ämnen
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft. Insights/komponenter
- Microsoft. Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft. Network/natGateways
- Microsoft. Network/privateEndpoints
- Microsoft. Network/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft. OperationalInsights/arbets ytor
- Microsoft. PowerBIDedicated/kapacitet
- Microsoft.Relay/namespaces
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

> [!NOTE]
> Det finns **för närvarande inte stöd**för att skapa mått för flera resurs mått för Virtual Machines. Den här artikeln kommer att uppdateras så snart den här funktionen blir tillgänglig.

Du kan fortfarande använda [klassiska aviseringar](../azure-monitor/platform/alerts-classic.overview.md) för resurser som ännu inte är tillgängliga i den andra generationen aviseringar. 

När du använder PowerShell/ARM/rest-anrop för att skapa mått aviseringar måste du ange "plats" för mått aviseringen till "USGov Virginia" eller "USGov Iowa". Ett exempel på inställningen är nedan:

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Mer information om hur du använder PowerShell finns i [offentlig dokumentation](../azure-monitor/platform/powershell-quickstart-samples.md).


## <a name="application-insights"></a>Application Insights

> [!NOTE]
> Kod utan kod/tillägg baserad övervakning för Azure App Services **stöds inte för närvarande**. Så snart den här funktionen blir tillgänglig kommer den här artikeln att uppdateras.

I det här avsnittet beskrivs den kompletterande konfiguration som krävs för att använda Application Insights i Azure Government. Läs mer om hur du Azure Monitor och Application Insights utcheckning av den [fullständiga dokumentationen](https://docs.microsoft.com/azure/azure-monitor/overview).

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Aktivera Application Insights för ASP.NET & ASP.NET Core med Visual Studio

För närvarande för Azure Government-kunder är det enda sättet att aktivera Application Insights via traditionell **Lägg till program insikter** -knappen i Visual Studio som kräver en liten manuell lösning. Kunder som har det associerade problemet kan se fel meddelanden som _"det finns ingen Azure-prenumeration kopplad till det här kontot_ _" eller "den valda prenumerationen stöder inte Application Insights_ trots att `microsoft.insights` Resource provider har statusen registrerad för prenumerationen. För att undvika det här problemet måste du utföra följande steg:

1. Växla Visual Studio för att [rikta in Azure Government molnet](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs).

2. Skapa (eller om redan befintlig uppsättning) användar miljö variabeln för AzureGraphApiVersion enligt följande: (om du vill skapa en användar miljö variabel går du till **kontroll panelen** > **system** > **avancerade Systeminställningar** > **avancerade** > **miljövariabler**.)

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. Gör lämpliga ändringar i Application Insights SDK-slutpunkten för antingen [ASP.net](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) eller [ASP.net Core](#aspnet-core) beroende på din projekt typ.

### <a name="snapshot-debugger"></a>Felsökning av ögonblicksbild

Snapshot Debugger är nu tillgängligt för Azure Government kunder. Om du vill använda Snapshot Debugger det enda ytterligare kravet är att du använder [Snapshot Collector version 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) eller senare. Följ bara standard Snapshot Debugger- [dokumentationen](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger).

### <a name="sdk-endpoint-modifications"></a>Ändringar av SDK-slutpunkt

För att kunna skicka data från Application Insights till Azure Government region måste du ändra standard slut punkts adresserna som används av Application Insights SDK: er. Varje SDK kräver något annorlunda ändringar.

### <a name="net-with-applicationinsightsconfig"></a>.NET med applicationinsights. config

> [!NOTE]
> Filen applicationinsights. config skrivs automatiskt över när en SDK-uppgradering utförs. När du har genomfört en SDK-uppgradering måste du ange regionens angivna slut punkts värden igen.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

Ändra filen appSettings. json i projektet enligt följande för att justera huvud slut punkten:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

Värdena för Live mått och profil frågans slut punkt kan bara anges via kod. Om du vill åsidosätta standardvärdena för alla slut punkts värden via kod gör du följande ändringar i `ConfigureServices`-metoden i `Startup.cs`-filen:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Azure Functions

Installera följande paket i ditt funktions projekt:

- Microsoft. ApplicationInsights-version 2.10.0
- Microsoft. ApplicationInsights. PerfCounterCollector-version 2.10.0
- Microsoft. ApplicationInsights. Windows Server. TelemetryChannel version 2.10.0

Lägg även till (eller ändra) Start koden för ditt funktions program:

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

Ändra applicationinsights. XML-filen om du vill ändra standard slut punkts adressen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Ändra `application.properties`-filen och Lägg till:

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

Slut punkterna kan också konfigureras via miljövariabler:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>Brand Väggs undantag

Tjänsten Azure Application Insights använder ett antal IP-adresser. Du kan behöva känna till de här adresserna om appen som du övervakar ligger bakom en brand vägg.

> [!NOTE]
> Även om dessa adresser är statiska, är det möjligt att vi behöver ändra dem från tid till tid. All Application Insights trafik representerar utgående trafik med undantag för tillgänglighets övervakning och webhookar som kräver inkommande brand Väggs regler.

### <a name="outgoing-ports"></a>Utgående portar
Du måste öppna vissa utgående portar i serverns brand vägg för att tillåta Application Insights SDK och/eller Statusövervakare att skicka data till portalen:

| Syfte | URL | IP-adress | Portar |
| --- | --- | --- | --- |
| Telemetri | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar
Azure Monitor loggar är allmänt tillgängliga i Azure Government.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>Olika

* Lösningar som är tillgängliga i Azure Government är:
  * [Övervakare av nätverksprestanda (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) – NPM är en molnbaserad nätverks övervaknings lösning för offentliga miljöer och hybrid moln miljöer. Organisationer använder NPM för att övervaka nätverks tillgänglighet i lokala miljöer och moln miljöer.  Slut punkts övervakning – en under funktion i NPM övervakar nätverks anslutningen till program.

Följande Azure Monitor loggar funktioner och lösningar är för närvarande inte tillgängliga i Azure Government.

* Lösningar som är i förhands granskning i Microsoft Azure, inklusive:
  * Tjänstkarta
  * Windows 10 Upgrade Analytics-lösning
  * Application Insights lösning
  * Azure Network Security Group Analytics-lösning
  * Azure Automation Analytics-lösning
  * Key Vault-analys lösning
* Lösningar och funktioner som kräver uppdateringar av lokal program vara, inklusive:
  * Surface Hub lösning
* Funktioner som är i för hands version i Global Azure, inklusive:
  * Exportera data till Power BI
* Azure-mått och Azure-diagnostik

URL: erna för Azure Monitor loggar skiljer sig i Azure Government:

| Azure Public | Azure Government | Anteckningar |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Log Analytics arbets ytor Portal |
| *workspaceId*. ods.OpInsights.Azure.com |*workspaceId*. ods.OpInsights.Azure.us |[API för datainsamling](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*. ods.opinsights.azure.us |Agent kommunikation – [Konfigurera brand Väggs inställningar](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*. oms.opinsights.azure.us |Agent kommunikation – [Konfigurera brand Väggs inställningar](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*. blob.core.usgovcloudapi.net |Agent kommunikation – [Konfigurera brand Väggs inställningar](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Avancerad analys Portal – [Konfigurera brand Väggs inställningar](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |Avancerad analys Portal – [Konfigurera brand Väggs inställningar](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |Avancerad analys Portal – [Konfigurera brand Väggs inställningar](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*. azure-automation.us |Azure Automation- [Konfigurera brand Väggs inställningar](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| Ej tillämpligt | *. usgovtrafficmanager.net | Azure Traffic Manager – [Konfigurera brand Väggs inställningar](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

Följande Azure Monitor loggar fungerar annorlunda i Azure Government:

* Om du vill ansluta din System Center Operations Manager hanterings grupp till Azure Monitor loggar måste du hämta och importera uppdaterade hanterings paket.
  + System Center Operations Manager 2016
    1. Installera Samlad [uppdatering 2 för System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Importera hanterings paketen som ingår i Samlad uppdatering 2 till Operations Manager. Information om hur du importerar ett hanterings paket från en disk finns i [så här importerar du ett Operations Manager hanterings paket](https://technet.microsoft.com/library/hh212691.aspx).
    3. Om du vill ansluta Operations Manager till Azure Monitor loggar följer du stegen i [ansluta Operations Manager till Azure Monitor loggar](../azure-monitor/platform/om-agents.md).
  + System Center Operations Manager 2012 R2 UR3 (eller senare)/Operations Manager 2012 SP1 UR7 (eller senare)
    1. Hämta och spara de [uppdaterade hanterings paketen](https://go.microsoft.com/fwlink/?LinkId=828749).
    2. Zippa upp filen som du laddade ned.
    3. Importera hanterings paketen till Operations Manager. Information om hur du importerar ett hanterings paket från en disk finns i [så här importerar du ett Operations Manager hanterings paket](https://technet.microsoft.com/library/hh212691.aspx).
    4. Om du vill ansluta Operations Manager till Azure Monitor loggar följer du stegen i [ansluta Operations Manager till Azure Monitor loggar](../azure-monitor/platform/om-agents.md).

* Mer information om hur du använder dator grupper från Configuration Manager finns i [ansluta Configuration Manager till Azure Monitor](../azure-monitor/platform/collect-sccm.md).

### <a name="frequently-asked-questions"></a>Vanliga frågor och svar
* Kan jag migrera data från Azure Monitor loggar i Microsoft Azure till Azure Government?
  * Nej. Det går inte att flytta data eller din arbets yta från Microsoft Azure till Azure Government.
* Kan jag växla mellan Microsoft Azure och Azure Government arbets ytor från Operations Management Suite-portalen?
  * Nej. Portalerna för Microsoft Azure och Azure Government är separata och delar inte ut information.

Mer information finns i [Azure Monitor loggar offentlig dokumentation](../log-analytics/log-analytics-overview.md).

## <a name="scheduler"></a>Scheduler
Information om den här tjänsten och hur du använder den finns i [Azure Scheduler-dokumentation](../scheduler/index.md).

## <a name="azure-portal"></a>Azure Portal
Du kan komma åt Azure Government Portal [här](https://portal.azure.us).

## <a name="azure-resource-manager"></a>Azure Resource Manager
Information om den här tjänsten och hur du använder den finns i [Azure Resource Manager-dokumentationen](../azure-resource-manager/management/overview.md).

## <a name="next-steps"></a>Nästa steg
* Prenumerera på [Azure Government blogg](https://blogs.msdn.microsoft.com/azuregov/)
* Få hjälp med Stack Overflow med hjälp av [Azure-gov](https://stackoverflow.com/questions/tagged/azure-gov)
* Ge feedback eller begär nya funktioner via [forumen Azure Government feedback](https://feedback.azure.com/forums/558487-azure-government)
