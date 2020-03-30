---
title: Använda Azure PowerShell för att aktivera diagnostik på en Virtuell Windows-dator
services: virtual-machines-windows
documentationcenter: ''
description: Lär dig hur du använder PowerShell för att aktivera Azure Diagnostics på en virtuell dator som kör Windows
author: sbtron
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 55afeb52323ead7db8be7e8fd1dabc880328e888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921546"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Använda PowerShell för att aktivera Azure Diagnostics på en virtuell dator som kör Windows

Azure Diagnostics är den funktion i Azure som möjliggör insamling av diagnostikdata på ett distribuerat program. Du kan använda diagnostiktillägget för att samla in diagnostikdata som programloggar eller prestandaräknare från en virtuell Azure-dator (VM) som kör Windows. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Aktivera diagnostiktillägget om du använder resurshanterarens distributionsmodell
Du kan aktivera diagnostiktillägget medan du skapar en Windows VM via distributionsmodellen för Azure Resource Manager genom att lägga till tilläggskonfigurationen i Resource Manager-mallen. Se [Skapa en virtuell Windows-dator med övervakning och diagnostik med hjälp av Azure Resource Manager-mallen](diagnostics-template.md).

Om du vill aktivera diagnostiktillägget på en befintlig virtuell dator som skapades via resurshanterarens distributionsmodell kan du använda [PowerShell-cmdleten Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) som visas nedan.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* är sökvägen till filen som innehåller diagnostikkonfigurationen i XML, enligt beskrivningen i [exemplet](#sample-diagnostics-configuration) nedan.  

Om konfigurationsfilen för diagnostik anger ett **StorageAccount-element** med ett lagringskontonamn, ställer skriptet *Set-AzVMDiagnosticsExtension* automatiskt in diagnostiktillägget för att skicka diagnostikdata till det lagringskontot. För att detta ska fungera måste lagringskontot finnas i samma prenumeration som den virtuella datorn.

Om inget **StorageAccount** angavs i diagnostikkonfigurationen måste du skicka in parametern *StorageAccountName* till cmdleten. Om parametern *StorageAccountName* har angetts använder cmdlet alltid det lagringskonto som anges i parametern och inte det som anges i konfigurationsfilen för diagnostik.

Om diagnostiklagringskontot finns i en annan prenumeration än den virtuella datorn måste du uttryckligen skicka in parametrarna *StorageAccountName* och *StorageAccountKey* till cmdlet. *Parametern StorageAccountKey* behövs inte när diagnostiklagringskontot finns i samma prenumeration, eftersom cmdleten automatiskt kan fråga och ange nyckelvärdet när diagnostiktillägget aktiveras. Men om diagnostiklagringskontot finns i en annan prenumeration kanske cmdleten inte kan hämta nyckeln automatiskt och du måste uttryckligen ange nyckeln via parametern *StorageAccountKey.*  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

När diagnostiktillägget är aktiverat på en virtuell dator kan du hämta de aktuella inställningarna med hjälp av [cmdleten Get-AzVmDiagnosticsExtension.](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension)

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

Cmdlet *returnerar PublicSettings*, som innehåller diagnostikkonfigurationen. Det finns två typer av konfiguration som stöds, WadCfg och xmlCfg. WadCfg är JSON-konfiguration och xmlCfg är XML-konfiguration i base64-kodat format. Om du vill läsa XML måste du avkoda den.

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

[Cmdleten Remove-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) kan användas för att ta bort diagnostiktillägget från den virtuella datorn.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Aktivera diagnostiktillägget om du använder den klassiska distributionsmodellen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Du kan använda [cmdleten Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) för att aktivera ett diagnostiktillägg på en virtuell dator som du skapar via den klassiska distributionsmodellen. Följande exempel visar hur du skapar en ny virtuell dator via den klassiska distributionsmodellen med diagnostiktillägget aktiverat.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM

Om du vill aktivera diagnostiktillägget på en befintlig virtuell dator som skapades via den klassiska distributionsmodellen använder du först [Cmdleten Get-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) för att hämta vm-konfigurationen. Uppdatera sedan vm-konfigurationen så att den innehåller diagnostiktillägget med hjälp av [cmdleten Set-AzureVMDiagnosticsExtension.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) Slutligen, tillämpa den uppdaterade konfigurationen på den virtuella datorn med hjälp av [Update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Exempel på diagnostikkonfiguration
Följande XML kan användas för den offentliga diagnostikkonfigurationen med ovanstående skript. Den här exempelkonfigurationen överför olika prestandaräknare till diagnostiklagringskontot, tillsammans med fel från programmet, säkerheten och systemkanalerna i Windows-händelseloggarna och eventuella fel från diagnostikinfrastrukturloggarna.

Konfigurationen måste uppdateras så att den innehåller följande:

* *Resurs-ID-attributet* för **metrics-elementet** måste uppdateras med resurs-ID för den virtuella datorn.
  
  * Resurs-ID:t kan konstrueras med hjälp av följande mönster: "/subscriptions/{*prenumerations-ID för prenumerationen med VM*}/resourceGroups/{*Resursgruppsnamnet för VM*}/providers/Microsoft.Compute/virtualMachines/{*VM-namnet*}".
  * Om till exempel prenumerations-ID för prenumerationen där den virtuella datorn körs är **11111111-1111-1111-1111-111111111111111111,** är resursgruppnamnet för resursgruppen **MyResourceGroup**och VM-namnet är **MyWindowsVM**, då skulle värdet för *resourceID* vara:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Mer information om hur mått genereras baserat på konfigurationen för prestandaräknare och mått finns [i azure diagnostics-måtttabellen i lagring](diagnostics-template.md#wadmetrics-tables-in-storage).
* **StorageAccount-elementet** måste uppdateras med namnet på diagnostiklagringskontot.
  
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
* Mer information om hur du använder Azure Diagnostics-funktionen och andra tekniker för att felsöka problem finns [i Aktivera diagnostik i Azure Cloud Services och Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Diagnostikkonfigurationsschemat](https://msdn.microsoft.com/library/azure/mt634524.aspx) förklarar de olika XML-konfigurationsalternativen för diagnostiktillägget.

