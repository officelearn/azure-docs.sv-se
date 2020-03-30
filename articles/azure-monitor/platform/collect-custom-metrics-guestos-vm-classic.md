---
title: Skicka klassiska Windows VM-mått till Azure Monitor-måttdatabasen
description: Skicka gästoperativsystem till Azure Monitor-datalagret för en virtuell Windows-dator (klassisk)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 65bb1a3915ece384974da12b4e7a1ad0c1e08133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655828"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Skicka gästoperativsystem-mått till Azure Monitor-måttdatabasen för en virtuell Windows-dator (klassisk)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor [Diagnostics-tillägget](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (kallas "WAD" eller "Diagnostik") kan du samla in mått och loggar från gästoperativsystemet (Guest OS) som körs som en del av en virtuell dator, molntjänst eller Service Fabric-kluster. Tillägget kan skicka telemetri till [många olika platser.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

I den här artikeln beskrivs processen för att skicka prestandamått för gästoperativsystem för en virtuell Windows-dator (klassisk) till Azure Monitor-måttdatabasen. Från och med Diagnostik version 1.11 kan du skriva mått direkt till Azure Monitor-måttarkivet, där standardplattformsmått redan har samlats in. 

Genom att lagra dem på den här platsen kan du komma åt samma åtgärder som du gör för plattformsmått. Åtgärderna omfattar aviseringar i nära realtid, diagram, routning, åtkomst från ett REST API med mera. Tidigare skrev diagnostiktillägget till Azure Storage, men inte till Azure Monitor-datalagringen. 

Processen som beskrivs i den här artikeln fungerar bara på klassiska virtuella datorer som kör Operativsystemet Windows.

## <a name="prerequisites"></a>Krav

- Du måste vara [tjänstadministratör eller medadministratör](../../cost-management-billing/manage/add-change-subscription-administrator.md) på din Azure-prenumeration. 

- Din prenumeration måste vara registrerad hos [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Du måste ha antingen [Azure PowerShell](/powershell/azure) eller [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) installerat.

- Vm-resursen måste finnas i en [region som stöder anpassade mått](metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Skapa ett klassiskt virtuellt dator- och lagringskonto

1. Skapa en klassisk virtuell dator med hjälp av Azure-portalen.
   ![Skapa klassisk virtuell dator](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. När du skapar den här virtuella datorn väljer du alternativet för att skapa ett nytt klassiskt lagringskonto. Vi använder det här lagringskontot i senare steg.

1. Gå till resursbladet **lagringskonton** i Azure-portalen. Välj **Nycklar**och anteckna nyckeln för lagringskontots namn och lagringskonto. Du behöver den här informationen i senare steg.
   ![Åtkomstnycklar för lagring](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Skapa en tjänstprincip i din Azure Active Directory-klient med hjälp av instruktionerna på [Skapa ett tjänsthuvudnamn](../../active-directory/develop/howto-create-service-principal-portal.md). Observera följande när du går igenom den här processen: 
- Skapa ny klienthemlighet för den här appen.
- Spara nyckeln och klient-ID:et för användning i senare steg.

Ge den här appen "Övervakningsmått Utgivare" behörigheter till den resurs som du vill avge mått mot. Du kan använda en resursgrupp eller en hel prenumeration.  

> [!NOTE]
> Tillägget Diagnostik använder tjänstens huvudnamn för att autentisera mot Azure Monitor och avge mått för din klassiska virtuella dator.

## <a name="author-diagnostics-extension-configuration"></a>Konfiguration av tillägg för författarediagnostik

1. Förbered konfigurationsfilen för diagnostiktillägget. Den här filen avgör vilka loggar och prestandaräknare diagnostiktillägget ska samla in för din klassiska virtuella dator. Här är ett exempel:

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
1. I avsnittet "SinksConfig" i diagnostikfilen definierar du en ny Azure Monitor-diskho enligt följande:

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

1. I avsnittet i konfigurationsfilen där listan över prestandaräknare som ska samlas in visas dirigerar du prestandaräknarna till Azure Monitor-diskbänken "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Definiera Azure Monitor-kontot i den privata konfigurationen. Lägg sedan till tjänstens huvudinformation som ska användas för att generera mått.

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Distribuera diagnostiktillägget till molntjänsten

1. Starta PowerShell och logga in.

    ```powershell
    Login-AzAccount
    ```

1. Börja med att ange kontext för den klassiska virtuella datorn.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Ange kontexten för det klassiska lagringskontot som skapades med den virtuella datorn.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Ange sökvägen diagnostik till en variabel med hjälp av följande kommando:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Förbered uppdateringen för din klassiska virtuella dator med diagnostikfilen som har Azure Monitor-diskbänken konfigurerad.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Distribuera uppdateringen till den virtuella datorn genom att köra följande kommando:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Det är fortfarande obligatoriskt att tillhandahålla ett lagringskonto som en del av installationen av diagnostiktillägget. Alla loggar eller prestandaräknare som anges i konfigurationsfilen diagnostik kommer att skrivas till det angivna lagringskontot.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Rita måtten i Azure-portalen

1.  Gå till Azure Portal. 

1.  Välj Övervaka på den vänstra **menyn.**

1.  Välj **Mätvärden**på **bladet Monitor** .

    ![Navigera i mått](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. Välj den klassiska virtuella datorn på den nedrullningsbara menyn Resurser.

1. Välj **azure.vm.windows.guest**på den nedrullningsbara menyn namnområden .

1. Välj **Minne\Allokerade byte i Bruk**på den nedrullningsbara menyn Mått.
   ![Ritdiagrammått](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).

