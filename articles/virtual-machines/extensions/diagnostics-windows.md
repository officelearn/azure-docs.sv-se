---
title: Använda Azure PowerShell för att aktivera diagnostik på en virtuell Windows-dator | Microsoft Docs
services: virtual-machines-windows
documentationcenter: ''
description: Lär dig hur du använder PowerShell för att aktivera Azure Diagnostics i en virtuell dator som kör Windows
author: sbtron
manager: jeconnoc
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 520211f3499931281d3ac86a1da1144564a8bb48
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980762"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Använd PowerShell för att aktivera Azure Diagnostics i en virtuell dator som kör Windows

Azure-diagnostik är funktion inom Azure som aktiverar insamlingen av diagnostikdata på ett distribuerat program. Du kan använda diagnostics-tillägg för att samla in diagnostikdata som programloggar eller prestandaräknare från en Azure-dator (VM) som kör Windows. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Aktivera diagnostiktillägget om du använder Resource Manager-distributionsmodellen
Du kan aktivera diagnostiktillägget när du skapar en virtuell Windows-dator via Azure Resource Manager-distributionsmodellen genom att lägga till tilläggskonfigurationen Resource Manager-mallen. Se [skapa en virtuell Windows-dator med övervakning och diagnostik med hjälp av Azure Resource Manager-mallen](diagnostics-template.md).

Om du vill aktivera diagnostiktillägg i en befintlig virtuell dator som har skapats via Resource Manager-distributionsmodellen, kan du använda den [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) PowerShell-cmdlet som visas nedan.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* är sökvägen till den fil som innehåller diagnostikkonfigurationen i XML, enligt beskrivningen i den [exempel](#sample-diagnostics-configuration) nedan.  

Om konfigurationsfilen diagnostik anger en **StorageAccount** element med ett lagringskontonamn och sedan den *Set-AzVMDiagnosticsExtension* skript automatiskt in diagnostiken tillägg för att skicka diagnostikdata till det lagringskontot. Lagringskontot måste finnas i samma prenumeration som den virtuella datorn för detta ska fungera.

Om ingen **StorageAccount** har angetts i diagnostikkonfigurationen måste du skicka in den *StorageAccountName* parameter till cmdleten. Om den *StorageAccountName* parametern anges, kommer cmdleten använder alltid det lagringskonto som anges i parametern och inte det som har angetts i konfigurationsfilen för diagnostik.

Om lagringskonto för startdiagnostik finns i en annan prenumeration från den virtuella datorn, måste du explicit skicka i den *StorageAccountName* och *StorageAccountKey* parametrar för cmdleten. Den *StorageAccountKey* parametern behövs inte när diagnostiklagringskonto är i samma prenumeration, vilket cmdleten automatiskt kan fråga efter och ange nyckelvärdet när du aktiverar diagnostics-tillägg. Men om lagringskonto för startdiagnostik finns i en annan prenumeration och sedan cmdlet: en kanske inte kan hämta nyckel automatiskt och du måste uttryckligen ange nyckel via den *StorageAccountKey* parametern.  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

När diagnostiktillägget är aktiverat på en virtuell dator kan du kan hämta de aktuella inställningarna med hjälp av den [Get-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension) cmdlet.

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Cmdleten returnerar *PublicSettings*, som innehåller diagnostikkonfigurationen. Det finns två typer av konfiguration som stöds, WadCfg och xmlCfg. WadCfg är JSON-konfiguration och xmlCfg är XML-konfiguration i en Base64-kodat format. Om du vill läsa XML-filen, måste du avkoda den.

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

Den [Remove-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) cmdlet kan användas för att ta bort diagnostiktillägget från den virtuella datorn.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Aktivera diagnostiktillägget om du använder den klassiska distributionsmodellen
Du kan använda den [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) cmdlet för att aktivera en diagnostiktillägget på en virtuell dator som du skapar via den klassiska distributionsmodellen. I följande exempel visas hur du skapar en ny virtuell dator via den klassiska distributionsmodellen med diagnostiktillägget aktiverat.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Om du vill aktivera diagnostiktillägg i en befintlig virtuell dator som har skapats via den klassiska distributionsmodellen, använder du först den [Get-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) cmdlet för att hämta VM-konfigurationen. Uppdatera VM-konfigurationen för att inkludera diagnostics-tillägget med hjälp av den [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) cmdlet. Slutligen, tillämpa den uppdaterade konfigurationen till den virtuella datorn med hjälp av [Update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Exempelkonfiguration för diagnostik
Följande XML-filen kan användas för diagnostik offentliga konfigurationen med ovanstående skript. Den här exempelkonfigurationen överför olika prestandaräknare till diagnostiklagringskonto tillsammans med fel från program-, säkerhets- och systemkanaler i Windows-händelseloggar och eventuella fel från diagnostikinfrastrukturloggar.

Konfigurationen måste uppdateras för att inkludera följande:

* Den *resourceID* attributet för den **mått** elementet måste uppdateras med resurs-ID för den virtuella datorn.
  
  * Resurs-ID kan konstrueras med hjälp av följande mönster ”: / subscriptions / {*prenumerations-ID för prenumerationen med den virtuella datorn*} /resourceGroups/ {*resourcegroup-namnet för den virtuella datorn*} / providers/Microsoft.Compute/virtualMachines/ {*VM-namnet*} ”.
  * Om prenumerationen ID: T för den prenumeration där Virtuellt datorn körs till exempel är **11111111-1111-1111-1111-111111111111**, resursgruppens namn för resursgruppen är **MyResourceGroup**, och Namn på virtuell dator är **MyWindowsVM**, så är värdet för *resourceID* skulle bli:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Mer information om hur mått genereras baserat på prestandakonfiguration räknare och mått, se [Azure Diagnostics mått tabellen i storage](diagnostics-template.md#wadmetrics-tables-in-storage).
* Den **StorageAccount** elementet måste uppdateras med namnet på lagringskontot för gatewaydiagnostik.
  
    ```xml
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
* Mer information om hur du använder Azure Diagnostics-funktionen och andra tekniker för att felsöka problem finns i [hur du aktiverar diagnostik i Azure Cloud Services och virtuella datorer](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Schema för Diagnostics konfigurationer](https://msdn.microsoft.com/library/azure/mt634524.aspx) beskriver de olika alternativen för diagnostiktillägget gäller XML-konfigurationer.

