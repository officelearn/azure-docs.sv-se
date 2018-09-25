---
title: Skicka OS gästmått av Azure Monitor-måtten lagra klassisk molntjänst
description: Skicka OS gästmått av Azure Monitor-måtten lagra klassisk molntjänst
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986922"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Skicka OS gästmått av Azure Monitor-måtten lagra klassisk molntjänst

Azure Monitor [Windows Azure-diagnostiktillägget](azure-diagnostics.md) (WAD) kan du samla in mått och loggar från gästoperativsystemet (guestOS) som körs som en del av en virtuell dator, molntjänst eller Service Fabric-kluster.  Tillägget kan skicka telemetri till många olika platser som anges i tidigare länkade artikeln.  

Den här artikeln beskriver processen för att skicka gäst-OS prestandamått för Azure klassiska molntjänster till arkivet som Azure Monitor-mått. Från och med WAD version 1.11, kan du skriva mått direkt till Azure Monitor metrics store där standardplattform mått är redan har samlats in. Lagra dem i den här platsen kan du komma åt samma åtgärder som är tillgängliga för plattformen mått.  Åtgärder omfattar nästan i realtid avisering, diagram, routning, komma åt från REST API och mycket mer.  Tidigare skrev WAD tillägget till Azure Storage, men inte Azure Monitor-datalagret.  

Den process som beskrivs i den här artikeln fungerar bara för prestandaräknare för Azure Cloud Services. Det fungerar inte för andra anpassade mått. 
   

## <a name="pre-requisites"></a>Förutsättningar

- Du måste vara en [tjänstadministratör eller delad administratör](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) på din Azure-prenumeration 

- Prenumerationen måste vara registrerad med [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Du måste ha antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) installerad, eller så kan du använda [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="provision-cloud-service-and-storage-account"></a>Etablera molntjänst och Storage-konto 

1. Skapa och distribuera en klassisk molntjänst (en exempelprogrammet klassiskt moln-tjänsten och distribution finns [här](../cloud-services/cloud-services-dotnet-get-started.md). 

2. Du kan använda ett befintligt lagringskonto eller distribuera ett nytt lagringskonto. Det är bäst om lagringskontot är i samma region som den klassiska molntjänst som du nyss skapade. Gå till bladet Lagringskonto resurs i Azure-portalen och välj den **nycklar**. Anteckna namn och storage-konto lagringskontonyckeln, du behöver dessa i senare steg.

   ![Lagringskontonycklar](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 

Skapa tjänstens huvudnamn i Azure Active Directory-klienten med hjälp av instruktionerna på... / azure/azure-resource-manager/resource-group-create-service-principal-portal. Observera följande när du går igenom den här processen: 
  - Du kan placera i valfri URL för inloggnings URL.  
  - Skapa nya klienthemligheten för den här appen  
  - Spara den och klient-id för användning i senare steg.  

Ge appen skapades i föregående steg *övervakning mått Publisher* behörigheter till resursen som du vill generera måtten mot. Om du planerar att använda appen för att skapa anpassade mått mot många resurser, kan du ge behörigheterna resource group eller på prenumerationsnivån.  

> [!NOTE]
> Diagnostiktillägget använder tjänstens huvudnamn för att autentisera mot Azure Monitor och skapa mått för din molntjänst 

## <a name="author-diagnostics-extension-configuration"></a>Redigera konfigurationen för Diagnostiktillägg 

Förbered konfigurationsfilen WAD diagnostics-tillägg. Den här filen avgör vilka loggar och prestandaräknare diagnostiktillägget bör samla in för din molntjänst. Nedan visas ett exempel på diagnostik konfigurationsfil.  

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

I avsnittet i konfigurationsfilen där listan över prestanda prestandaräknare samlas in, lägger du till Azure-Övervakare för mottagare. Den här posten säkerställer att alla prestandaräknare som angetts dirigeras till Azure Monitor som mått. Passa på att lägga till/ta bort prestandaräknare enligt dina behov. 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
Slutligen i konfigurationen för privata lägger du till en *Azure-Övervakningskontot* avsnittet. Ange tjänstens huvudnamn klient-ID och hemlighet som har skapats i tidigare steg. 

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

Starta PowerShell och logga in på Azure 

```PowerShell
Login-AzureRmAccount 
```

Store information om Storage-konto skapas i ett tidigare steg i variabler med hjälp av följande kommandon. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
På samma sätt, ange sökvägen till diagnostik till en variabel med i kommandot nedan. 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Distribuera diagnostics-tillägg till din molntjänst med diagnostik-fil med Azure Monitor-mellanlagringsplatsen som konfigurerats med hjälp av kommandot nedan 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> Det är fortfarande obligatoriskt att ange ett Lagringskonto som en del av installationen av diagnostics-tillägg. Loggar och/eller prestandaräknare som angetts i konfigurationsfilen diagnostik skrivs till det angivna lagringskontot.  

## <a name="plot-metrics-in-the-azure-portal"></a>Rita mått i Azure portal 

Gå till Azure-portalen 

 ![Mått Azure-portalen](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. I den vänstra menyn, klickar du på Övervakaren 

1. På Monitor-bladet klickar du på fliken Förhandsgranska i mått 

1. I resource listrutan väljer du din klassiska molntjänst 

1. Välj i listrutan namnområden **azure.vm.windows.guest** 

1. I mått som nedrullningsbar listruta, Välj *minne\dedikerade byte som används* 

Du kan välja att visa den totala mängden minne som används av en viss roll och varje rollinstans med hjälp av den dimension som filtrering och dela upp funktionerna. 

 ![Mått Azure-portalen](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).



