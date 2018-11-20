---
title: Skicka gäst-OS mått till Azure Monitor-datalager för en Windows-dator (klassisk)
description: Skicka gäst-OS mått till Azure Monitor-datalager för en Windows-dator (klassisk)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: db0de3ec354fd599d2c10b26eea2991469410c12
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52163375"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Skicka gäst-OS mått till Azure Monitor-datalager för en Windows-dator (klassisk)

Azure Monitor [diagnostiktillägget](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (kallas ”WAD” eller ”diagnostik”) kan du samla in mått och loggar från gästoperativsystemet (gäst-OS) som körs som en del av en virtuell dator, en molntjänst eller ett Service Fabric kluster. Tillägget kan skicka telemetri till [många olika platser.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Den här artikeln beskriver processen för att skicka gäst-OS prestandamått för en Windows-dator (klassisk) till arkivet som Azure Monitor-mått. Från och med diagnostik version 1.11, kan du skriva mått direkt till i Azure Monitor mått store, där standardplattform mått är redan har samlats in. 

Lagra dem i den här platsen kan du komma åt samma åtgärder som du gör plattform mått. Åtgärder omfattar nästan i realtid avisering, diagram, routning, åtkomst från ett REST-API och mycket mer. Tidigare skrev Diagnostics-tillägg till Azure Storage, men inte till Azure Monitor-datalager. 

Processen som beskrivs i den här artikeln fungerar bara på klassiska virtuella datorer som kör Windows-operativsystem.

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara en [tjänstadministratör eller delad administratör](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) på din Azure-prenumeration. 

- Prenumerationen måste vara registrerad med [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal). 

- Du måste ha antingen [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) eller [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) installerad.

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Skapa en klassisk virtuell dator och storage-konto

1. Skapa en klassisk virtuell dator med hjälp av Azure portal.
   ![Skapa en klassisk virtuell dator](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png)

1. När du skapar den här virtuella datorn, väljer du alternativet för att skapa ett klassiskt lagringskonto. Vi använder det här lagringskontot i senare steg.

1. I Azure-portalen går du till den **lagringskonton** resursbladet. Välj **nycklar**, och anteckna lagringskontonamn och lagringskontonyckel. Du behöver den här informationen i senare steg.
   ![Åtkomstnycklar för lagring](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Skapa tjänstens huvudnamn i Azure Active Directory-klienten med hjälp av anvisningarna i [skapa ett huvudnamn för tjänsten](../active-directory/develop/howto-create-service-principal-portal.md). Observera följande när du går igenom den här processen: 
- Skapa nya klienthemligheten för den här appen.
- Spara den och klient-ID för användning i senare steg.

Ge den här appen ”övervakning mått Publisher” behörigheter till den resurs som du vill generera måtten mot. Du kan använda en resursgrupp eller en hela prenumerationen.  

> [!NOTE]
> Diagnostiktillägget använder tjänstens huvudnamn för att autentisera mot Azure Monitor och skapa mått för din klassiska virtuella dator.

## <a name="author-diagnostics-extension-configuration"></a>Redigera konfigurationen för Diagnostiktillägg

1. Förbered din konfigurationsfil för diagnostik-tillägget. Den här filen avgör vilka loggar och prestandaräknare diagnostiktillägget bör samla in för din klassiska virtuella dator. Följande är ett exempel:

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
1. I avsnittet ”SinksConfig” i filen diagnostik att definiera en ny Azure Monitor-kanalmottagare enligt följande:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Dirigera prestandaräknarna till Azure Monitor-mellanlagringsplatsen ”AzMonSink” i avsnittet i konfigurationsfilen där listan över prestandaräknare som samlas in visas.

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. I den privata konfigurationen definierar du Azure Monitor-konto. Lägg sedan till information om tjänstens huvudnamn du använder för att skapa mått.

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Distribuera Diagnostics-tillägg till din molntjänst

1. Starta PowerShell och logga in.

    ```powershell
    Login-AzureRmAccount
    ```

1. Börja med att kontexten för den klassiska virtuella datorn.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Ange sedan kontexten för det klassiska lagringskontot som har skapats med den virtuella datorn.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Ange sökvägen till diagnostik-filen till en variabel med hjälp av följande kommando:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Förbered uppdateringen för din klassisk virtuell dator med diagnostik-fil som har konfigurerats Azure Monitor-mellanlagringsplatsen.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Distribuera uppdateringen på den virtuella datorn genom att köra följande kommando:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Det är fortfarande obligatoriskt att ange ett lagringskonto som en del av installationen av Diagnostics-tillägg. Alla loggar eller prestandaräknare som anges i konfigurationsfilen diagnostik skrivs till det angivna lagringskontot.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Rita mått i Azure portal

1.  Gå till Azure Portal. 

1.  På menyn till vänster väljer **övervakaren.**

1.  På den **övervakaren** bladet väljer **mått**.

    ![Navigera mått](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png)

1. Välj din klassisk virtuell dator i listrutan resurser.

1. Välj i listrutan namnområden **azure.vm.windows.guest**.

1. Välj i listrutan mått **minne\dedikerade byte som används**.
   ![Rita mått](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png)


## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).
