---
title: Skicka guest OS mått till Azure Monitor-datalager för en Windows-datorn (klassisk)
description: Skicka guest OS mått till Azure Monitor-datalager för en Windows-datorn (klassisk)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: cb803450f7765ae62292ff3afb7f32209b437f78
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978938"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Skicka guest OS mått till Azure Monitor-datalager för en Windows-datorn (klassisk)

Azure Monitor [Windows Azure-diagnostiktillägget](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (WAD) kan du samla in mått och loggar från gästoperativsystemet (gästoperativsystem) körs som en del av en virtuell dator, molntjänst eller Service Fabric-kluster. Tillägget kan skicka telemetri till många olika platser som anges i tidigare länkade artikeln.

Den här artikeln beskriver processen för att skicka gäst-OS prestandamått för en Windows-datorn (klassisk) till arkivet som Azure Monitor-mått. Från och med WAD version 1.11, kan du skriva mått direkt till Azure Monitor metrics store där standardplattform mått är redan har samlats in. Lagra dem i den här platsen kan du komma åt samma åtgärder som är tillgängliga för plattformen mått.  Åtgärder omfattar nästan i realtid avisering, diagram, routning, komma åt från REST API och mycket mer.  Tidigare skrev WAD tillägget till Azure Storage, men inte Azure Monitor-datalagret. 

Den process som beskrivs i den här artikeln fungerar bara klassiska virtuella datorer som kör Windows-operativsystemet.

## <a name="pre-requisites"></a>Förutsättningar

- Du måste vara en [tjänstadministratör eller delad administratör](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) på din Azure-prenumeration 

- Prenumerationen måste vara registrerad med [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Du måste ha antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) installerad, eller så kan du använda [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Skapa en klassisk virtuell dator och Storage-konto

1. Skapa en klassisk virtuell dator med Azure portal ![skapa klassiska virtuella datorer](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png)

1. När du skapar den här virtuella datorn kan du välja att skapa ett klassiskt lagringskonto. Vi använder det här lagringskontot i senare steg.

1. I Azure-portalen går du till resursbladet Storage-konto och välj den **nycklar** och noterar lagringskontonamn och lagringskontonyckel. Du behöver dessa nycklar i senare steg ![åtkomstnycklar för lagring](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Skapa tjänstens huvudnamn i Azure Active Directory-klienten med hjälp av instruktionerna i [skapa ett huvudnamn för tjänsten](../azure-resource-manager/resource-group-create-service-principal-portal.md). Observera följande när du går igenom den här processen: 
- Skapa nya klienthemligheten för den här appen  
- Spara den och klient-id för användning i senare steg.

Ge den här appen ”övervakning mått Publisher” behörigheter till resursen som du vill generera måtten mot. Du kan använda en resursgrupp eller en hela prenumerationen.  

> [!NOTE]
> Diagnostiktillägget använder tjänstens huvudnamn för att autentisera mot Azure Monitor och skapa mått för din klassiska virtuella dator.

## <a name="author-diagnostics-extension-configuration"></a>Redigera konfigurationen för Diagnostiktillägg

1. Förbered konfigurationsfilen WAD diagnostics-tillägg. Den här filen avgör vilka loggar och prestandaräknare diagnostiktillägget bör samla in för din klassiska virtuella dator. Nedan visas ett exempel.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
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
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
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
1. Definiera en ny Azure Monitor-kanalmottagare i avsnittet ”SinksConfig” i filen diagnostik:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide your Classic VM’s Resource ID </ResourceId>
                <Region>Region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Vidarebefordra prestandaräknarna för de Azure Monitor mottagare ”AzMonSink” i avsnittet i konfigurationsfilen där listan med prestandaräknare som samlas in finns.

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Definiera Azure Monitor-konto i privata konfigurationen och lägga till information om tjänstens huvudnamn du använder för att skapa mått.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Spara filen lokalt.

## <a name="deploy-diagnostics-extension-to-your-cloud-service"></a>Distribuera Diagnostics-tillägg till din molntjänst

1. Starta PowerShell och logga in

    ```powershell
    Login-AzureRmAccount
    ```

1. Starta genom att ange kontexten till din klassisk virtuell dator

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Ange sedan kontexten för det klassiska lagringskontot som har skapats med den virtuella datorn.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Ange sökvägen till diagnostik-filen till en variabel med i kommandot nedan.

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Förbereda uppdateringen till den klassiska virtuella datorn med filen diagnostik med Azure Monitor-mellanlagringsplatsen konfigurerad

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Distribuera uppdateringen på den virtuella datorn genom att köra kommandot nedan

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Det är fortfarande obligatoriskt att ange ett Lagringskonto som en del av installationen av diagnostics-tillägg. Loggar och/eller prestandaräknare som angetts i konfigurationsfilen diagnostik skrivs till det angivna lagringskontot.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Rita mått i Azure portal

1.  Gå till Azure-portalen

1.  I den vänstra menyn klickar du på Övervakaren

1.  På Monitor-bladet klickar du på den **mått**
   ![navigera mått](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png)

1. I resursen listrutan väljer du din klassisk virtuell dator

1. Välj i listrutan för namnområden **azure.vm.windows.guest**

1. I mått som nedrullningsbar listruta, Välj **minne\dedikerade byte som används**
   ![Rita mått](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png)


## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).
