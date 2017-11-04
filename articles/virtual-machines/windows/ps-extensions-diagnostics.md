---
title: "Aktivera diagnostik på en virtuell Windows-dator med hjälp av Azure PowerShell | Microsoft Docs"
services: virtual-machines-windows
documentationcenter: 
description: "Lär dig hur du kan aktivera Azure-diagnostik i en virtuell dator som kör Windows PowerShell"
author: sbtron
manager: timlt
editor: 
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: d0be4a712657edfc516c5f32e66519f5d9486728
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Använd PowerShell för att aktivera Azure Diagnostics i en virtuell dator som kör Windows
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure-diagnostik är funktion i Azure som möjliggör insamling av diagnostikdata på ett distribuerat program. Du kan använda tillägget diagnostik för att samla in diagnostikdata som programloggarna eller prestandaräknare från en Azure-dator (VM) som kör Windows. Den här artikeln beskriver hur du använder Windows PowerShell för att aktivera tillägget diagnostik för en virtuell dator. Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för kraven för den här artikeln.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Aktivera diagnostik tillägget om du använder Resource Manager-distributionsmodellen
När du skapar en virtuell Windows-dator via Azure Resource Manager-distributionsmodellen genom att lägga till tillägget konfigurationen Resource Manager-mall kan du aktivera tillägget diagnostik. Se [skapa en virtuell Windows-dator med övervakning och diagnostik med hjälp av Azure Resource Manager-mallen](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Om du vill aktivera tillägget diagnostik på en befintlig virtuell dator som har skapats via Resource Manager-distributionsmodellen, kan du använda den [Set AzureRMVMDiagnosticsExtension](/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension) PowerShell-cmdlet enligt nedan.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* är sökvägen till filen som innehåller konfiguration för diagnostik i XML, som beskrivs i den [exempel](#sample-diagnostics-configuration) nedan.  

Om konfigurationsfilen diagnostik anger en **StorageAccount** element med namnet på ett lagringskonto, sedan *Set AzureRMVMDiagnosticsExtension* skript automatiskt in diagnostiken tillägget till skicka diagnostikdata till detta lagringskonto. Lagringskontot måste vara i samma prenumeration som den virtuella datorn för det här ska fungera.

Om inget **StorageAccount** har angetts i diagnostik-konfigurationen måste du skicka in den *StorageAccountName* parameter för cmdlet. Om den *StorageAccountName* parametern anges, kommer cmdleten använder alltid storage-konto som har angetts i parametern och inte det som har angetts i konfigurationsfilen diagnostik.

Om diagnostik storage-konto finns i en annan prenumeration från den virtuella datorn, måste du uttryckligen skicka in den *StorageAccountName* och *StorageAccountKey* parametrar för cmdleten. Den *StorageAccountKey* parametern behövs inte när diagnostiklagringskonto är i samma prenumeration som cmdleten automatiskt kan fråga och ange värdet för nyckeln när du aktiverar tillägget diagnostik. Om lagringskontot för gatewaydiagnostik är i en annan prenumeration sedan cmdleten kanske inte går att hämta nyckeln automatiskt och måste du uttryckligen ange nyckel via den *StorageAccountKey* parameter.  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

När tillägget diagnostik är aktiverat på en virtuell dator, du kan hämta de aktuella inställningarna med hjälp av den [Get-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/get-azurermvmdiagnosticsextension) cmdlet.

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Returnerar cmdleten *PublicSettings*, som innehåller diagnostik-konfigurationen. Det finns två typer av konfiguration som stöds, WadCfg och xmlCfg. WadCfg är JSON-konfiguration och xmlCfg är XML-konfiguration i en Base64-kodat format. Om du vill läsa XML-filen måste du avkoda den.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

Den [ta bort AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/remove-azurermvmdiagnosticsextension) cmdlet kan användas för diagnostik-tillägget tas bort från den virtuella datorn.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Aktivera diagnostik tillägget om du använder den klassiska distributionsmodellen
Du kan använda den [Set AzureVMDiagnosticsExtension](/powershell/module/azure/set-azurevmdiagnosticsextension) cmdlet för att aktivera tillägget diagnostik på en virtuell dator som du skapar med den klassiska distributionsmodellen. I följande exempel visas hur du skapar en ny virtuell dator via den klassiska distributionsmodellen med filnamnstillägget diagnostik aktiverad.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Aktivera diagnostik tillägg på en befintlig virtuell dator som har skapats via den klassiska distributionsmodellen först med den [Get-AzureVM](/powershell/module/azure/get-azurevm) för att hämta VM-konfiguration. Uppdatera VM-konfiguration med diagnostik-tillägget med hjälp av [Set AzureVMDiagnosticsExtension](/powershell/module/azure/set-azurevmdiagnosticsextension) cmdlet. Slutligen använder den uppdaterade konfigurationen på den virtuella datorn med [uppdatering AzureVM](/powershell/module/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Diagnostik exempelkonfiguration
Följande XML-filen kan användas för diagnostik offentliga konfiguration med skript som ovan. Den här exempelkonfiguration överför olika prestandaräknare till diagnostiklagringskonto tillsammans med fel från program-, säkerhets- och systemkanaler i händelseloggarna i Windows och eventuella fel från diagnostik infrastruktur loggar.

Konfigurationen måste uppdateras för att inkludera följande:

* Den *resourceID* attribut för den **mått** element måste uppdateras med resurs-ID för den virtuella datorn.
  
  * Resurs-ID kan konstrueras med hjälp av följande mönster ”: /subscriptions/ {*prenumerations-ID för prenumerationen med den virtuella datorn*} /resourceGroups/ {*resourcegroup namnet för den virtuella datorn*} / providers/Microsoft.Compute/virtualMachines/ {*på det Virtuella datornamnet*} ”.
  * Till exempel om prenumerationen ID för prenumerationen där den virtuella datorn körs är **11111111-1111-1111-1111-111111111111**, resursgruppens namn för resursgruppen är **MyResourceGroup**, och Namn på virtuell dator är **MyWindowsVM**, så är värdet för *resourceID* skulle vara:
    
      ```
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Mer information om hur mått genereras baserat på prestanda räknare och mått konfiguration, se [Azure Diagnostics mått tabellen i lagring](extensions-diagnostics-template.md#wadmetrics-tables-in-storage).
* Den **StorageAccount** element måste uppdateras med namnet på lagringskontot för gatewaydiagnostik.
  
    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du använder Azure Diagnostics-funktionen och andra tekniker för att felsöka problem finns [aktiverar diagnostik i Azure-molntjänster och virtuella datorer](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Diagnostik konfigurationer schemat](https://msdn.microsoft.com/library/azure/mt634524.aspx) förklarar alternativen olika XML-konfigurationer för diagnostik-tillägg.

