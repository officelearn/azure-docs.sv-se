---
title: Skicka gäst-OS mått av Azure Monitor-måtten lagra klassiska molntjänster
description: Skicka gäst-OS mått av Azure Monitor-måtten lagra Cloud Services
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 30b08062aa360c4a43dc1bfe9f574447b58521f5
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095219"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Skicka gäst-OS mått av Azure Monitor-måtten lagra klassiska molntjänster 
Med Azure Monitor [diagnostiktillägget](azure-diagnostics.md), du kan samla in mått och loggar från gästoperativsystemet (gäst-OS) som körs som en del av en virtuell dator, en molntjänst eller ett Service Fabric-kluster. Tillägget kan skicka telemetri till [många olika platser.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Den här artikeln beskriver processen för att skicka gäst-OS-prestandamått för Azure klassiska molntjänster till arkivet som Azure Monitor-mått. Från och med diagnostik version 1.11, kan du skriva mått direkt till i Azure Monitor mått store, där standardplattform mått är redan har samlats in. 

Lagra dem i den här platsen kan du komma åt samma åtgärder som du kan för plattformen mått. Åtgärder omfattar nästan i realtid avisering, diagram, routning, åtkomst från ett REST-API och mycket mer.  Tidigare skrev Diagnostics-tillägg till Azure Storage, men inte till Azure Monitor-datalager.  

Processen som beskrivs i den här artikeln fungerar endast för prestandaräknare i Azure Cloud Services. Det fungerar inte för andra anpassade mått. 
   

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara en [tjänstadministratör eller delad administratör](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) på din Azure-prenumeration. 

- Prenumerationen måste vara registrerad med [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal). 

- Du måste ha antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) eller [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) installerad.


## <a name="provision-a-cloud-service-and-storage-account"></a>Etablera en cloud service och storage-konto 

1. Skapa och distribuera en klassisk molntjänst. Ett exempel klassiska Cloud Services-program och distribution finns på [Kom igång med Azure Cloud Services och ASP.NET](../cloud-services/cloud-services-dotnet-get-started.md). 

2. Du kan använda ett befintligt lagringskonto eller distribuera ett nytt lagringskonto. Det är bäst om lagringskontot tillhör samma region som den klassiska molntjänst som du skapade. I Azure-portalen går du till den **lagringskonton** resursbladet och välj sedan **nycklar**. Anteckna namnet på lagringskontot och lagringskontonyckeln. Du behöver den här informationen i senare steg.

   ![Lagringskontonycklar](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 

Skapa tjänstens huvudnamn i Azure Active Directory-klienten med hjälp av anvisningarna i [Använd portalen för att skapa en Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Observera följande när du ska via den här processen: 

  - Du kan placera i valfri URL för URL-inloggningen.  
  - Skapa nya klienthemligheten för den här appen.  
  - Spara den och klient-ID för användning i senare steg.  

Ge appen skapades i föregående steg *övervakning mått Publisher* behörigheter till resursen som du vill generera måtten mot. Om du planerar att använda appen för att skapa anpassade mått mot många resurser, kan du ge behörigheterna resource group eller på prenumerationsnivån.  

> [!NOTE]
> Diagnostiktillägget använder tjänstens huvudnamn för att autentisera mot Azure Monitor och skapa mått för din molntjänst.

## <a name="author-diagnostics-extension-configuration"></a>Redigera konfigurationen för Diagnostiktillägg 

Förbered din konfigurationsfil för diagnostik-tillägget. Den här filen avgör vilka loggar och prestandaräknare diagnostiktillägget bör samla in för din molntjänst. Nedan följer ett exempel på diagnostik konfigurationsfil:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

Definiera en ny Azure Monitor-kanalmottagare i avsnittet ”SinksConfig” i filen diagnostik: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

I avsnittet i konfigurationsfilen där du lista prestandaräknarna som samlar in, lägger du till Azure Monitor-mellanlagringsplatsen. Den här posten säkerställer att alla prestandaräknare som du har angett dirigeras till Azure Monitor som mått. Du kan lägga till eller ta bort prestandaräknare efter dina behov. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Slutligen i konfigurationen för privata lägger du till en *Azure-Övervakningskontot* avsnittet. Ange tjänstens huvudnamn klient-ID och hemlighet som du skapade tidigare. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```
 
Spara diagnostik filen lokalt.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Distribuera Diagnostics-tillägg till din molntjänst 

Starta PowerShell och logga in på Azure. 

```PowerShell
Login-AzureRmAccount 
```

Använd följande kommandon för att lagra information om storage-kontot som du skapade tidigare. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
På samma sätt, ange sökvägen till diagnostik till en variabel med hjälp av följande kommando:

```PowerShell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Distribuera Diagnostics-tillägg till din molntjänst med diagnostik-fil med Azure Monitor-mellanlagringsplatsen som konfigurerats med följande kommando:  

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> Det är fortfarande obligatoriskt att ange ett lagringskonto som en del av installationen av Diagnostics-tillägg. Alla loggar eller prestandaräknare som anges i konfigurationsfilen diagnostik skrivs till det angivna lagringskontot.  

## <a name="plot-metrics-in-the-azure-portal"></a>Rita mått i Azure portal 

1. Gå till Azure Portal. 

   ![Mått Azure-portalen](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

2. På menyn till vänster väljer **övervakaren.**

3. På den **övervakaren** bladet och välja den **mått förhandsversion** fliken.

4. Välj din klassiska molntjänst i listrutan resurser.

5. Välj i listrutan namnområden **azure.vm.windows.guest**. 

6. Välj i listrutan mått **minne\dedikerade byte som används**. 

Du kan använda dimensionen filtrering och dela funktioner för att visa den totala mängden minne som används av en viss roll eller rollinstans. 

 ![Mått Azure-portalen](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).



