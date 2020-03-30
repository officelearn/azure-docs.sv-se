---
title: Skicka klassiska Molntjänstmått till Azure Monitor-måttdatabas
description: Beskriver processen för att skicka prestandamått för gästoperativsystem för klassiska Azure Cloud Services till Azure Monitor-måttarkivet.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3b390ffa20cf3cf79b8fb6311ad05b2978bd5d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655812"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Skicka gästoperativsystem-mått till Azure Monitor-måttarkivet klassiska Molntjänster 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Med tillägget Azure Monitor [Diagnostics](diagnostics-extension-overview.md)kan du samla in mått och loggar från gästoperativsystemet (Guest OS) som körs som en del av ett virtuellt dator-, molntjänst- eller Service Fabric-kluster. Tillägget kan skicka telemetri till [många olika platser.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

I den här artikeln beskrivs processen för att skicka prestandamått för gästoperativsystem för Azure classic Cloud Services till Azure Monitor-måttarkivet. Från och med Diagnostik version 1.11 kan du skriva mått direkt till Azure Monitor-måttarkivet, där standardplattformsmått redan har samlats in. 

Genom att lagra dem på den här platsen kan du komma åt samma åtgärder som du kan för plattformsmått. Åtgärderna omfattar aviseringar i nära realtid, diagram, routning, åtkomst från ett REST API med mera.  Tidigare skrev diagnostiktillägget till Azure Storage, men inte till Azure Monitor-datalagringen.  

Processen som beskrivs i den här artikeln fungerar endast för prestandaräknare i Azure Cloud Services. Det fungerar inte för andra anpassade mått. 

## <a name="prerequisites"></a>Krav

- Du måste vara [tjänstadministratör eller medadministratör](../../cost-management-billing/manage/add-change-subscription-administrator.md) på din Azure-prenumeration. 

- Din prenumeration måste vara registrerad hos [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Du måste ha antingen [Azure PowerShell](/powershell/azure) eller [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) installerat.

- Din molntjänst måste finnas i en [region som stöder anpassade mått](metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Etablera ett molntjänst- och lagringskonto 

1. Skapa och distribuera en klassisk molntjänst. Ett exempel på klassiska Molntjänster-program och distribution finns på [Komma igång med Azure Cloud Services och ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Du kan använda ett befintligt lagringskonto eller distribuera ett nytt lagringskonto. Det är bäst om lagringskontot finns i samma region som den klassiska molntjänsten som du har skapat. Gå till resursbladet **lagringskonton** i Azure-portalen och välj sedan **Nycklar**. Ta del av lagringskontots namn och lagringskontonyckeln. Du behöver den här informationen i senare steg.

   ![Lagringskontonycklar](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 

Skapa en tjänstprincip i din Azure Active Directory-klient med hjälp av instruktionerna på [Använd portalen för att skapa ett Azure Active Directory-program och tjänsthuvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Observera följande när du går igenom den här processen: 

- Du kan lägga in valfri webbadress för inloggningsadressen.  
- Skapa ny klienthemlighet för den här appen.  
- Spara nyckeln och klient-ID:et för användning i senare steg.  

Ge appen som skapats i föregående steg *Övervakningsmått Publisher* behörigheter till den resurs som du vill avge mått mot. Om du planerar att använda appen för att generera anpassade mått mot många resurser kan du bevilja dessa behörigheter på resursgrupps- eller prenumerationsnivå.  

> [!NOTE]
> Tillägget Diagnostik använder tjänstens huvudnamn för att autentisera mot Azure Monitor och avge mått för din molntjänst.

## <a name="author-diagnostics-extension-configuration"></a>Konfiguration av tillägg för författarediagnostik 

Förbered konfigurationsfilen för diagnostiktillägget. Den här filen avgör vilka loggar och prestandaräknare diagnostiktillägget ska samla in för din molntjänst. Följande är ett exempel diagnostik konfigurationsfil:  

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

I avsnittet "SinksConfig" i diagnostikfilen definierar du en ny Azure Monitor-mottagare: 

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

Lägg till Azure Monitor Sink i avsnittet i konfigurationsfilen där du listar prestandaräknarena som ska samlas in. Den här posten säkerställer att alla prestandaräknare som du har angett dirigeras till Azure Monitor som mått. Du kan lägga till eller ta bort prestandaräknare efter dina behov. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Slutligen, i den privata konfigurationen, lägga till en *Azure Monitor-kontoavsnitt.* Ange tjänstens huvudklient-ID och hemlighet som du skapade tidigare. 

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

Spara den här diagnostikfilen lokalt.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Distribuera diagnostiktillägget till molntjänsten 

Starta PowerShell och logga in på Azure. 

```powershell
Login-AzAccount 
```

Använd följande kommandon för att lagra information om lagringskontot som du skapade tidigare. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

På samma sätt anger du sökvägen till diagnostikfilen till en variabel med hjälp av följande kommando:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Distribuera diagnostiktillägget till molntjänsten med diagnostikfilen med Azure Monitor-diskbänken konfigurerad med följande kommando:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Det är fortfarande obligatoriskt att tillhandahålla ett lagringskonto som en del av installationen av diagnostiktillägget. Alla loggar eller prestandaräknare som anges i diagnostikkonfigurationsfilen skrivs till det angivna lagringskontot.  

## <a name="plot-metrics-in-the-azure-portal"></a>Rita mått i Azure-portalen 

1. Gå till Azure Portal. 

   ![Azure-portalen för mått](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. Välj Övervaka på den vänstra **menyn.**

3. Välj fliken **Förhandsgranska mått på** **bladet Övervaka.**

4. Välj din klassiska molntjänst i den nedrullningsbara menyn Resurser.

5. Välj **azure.vm.windows.guest**på den nedrullningsbara menyn namnområden . 

6. Välj **Minne\Allokerade byte i Bruk**på den nedrullningsbara menyn Mått. 

Du använder funktioner för dimensionsfiltrering och delning för att visa det totala minne som används av en viss roll eller rollinstans. 

 ![Azure-portalen för mått](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [anpassade mått](metrics-custom-overview.md).

