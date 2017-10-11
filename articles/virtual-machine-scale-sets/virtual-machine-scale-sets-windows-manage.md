---
title: "Hantera virtuella datorer i en Skaluppsättning för virtuell dator | Microsoft Docs"
description: Hantera virtuella datorer i en virtuell dator-skala med Azure PowerShell.
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.openlocfilehash: d09a020b903e5f43afe03b86c675bcc1eb536cbc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Hantera virtuella datorer i en skaluppsättning för virtuell dator
Använd uppgifterna i den här artikeln för att hantera virtuella datorer i din skaluppsättning för virtuell dator.

De flesta av de uppgifter som rör hantering av en virtuell dator i en skaluppsättning kräver att du vet instans-ID för den dator som du vill hantera. Du kan använda [resursutforskaren Azure](https://resources.azure.com) att hitta instans-ID för en virtuell dator i en skaluppsättning. Du kan också använda Resursläsaren för att kontrollera statusen för de aktiviteter som du är klar.

Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

## <a name="display-information-about-a-scale-set"></a>Visa information om en skaluppsättning
Du kan hämta allmän information om en skalningsuppsättning som också kallas instansvyn. Eller så kan du få mer specifik information, till exempel information om resurser i skaluppsättning.

Ersätt angiven värdena med namnet eller resursgrupp och skala ange och sedan köra kommandot:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Den returnerar ungefär så här:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

Ersätt angiven värdena med namnet på din resursuppsättningen för gruppen och skala. Ersätt  *#*  med den virtuella dator som du vill ha information om och sedan köra den instans-ID:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Den returnerar något som liknar det här exemplet:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## <a name="start-a-virtual-machine-in-a-scale-set"></a>Starta en virtuell dator i en skaluppsättning
Ersätt angiven värdena med namnet på din resursuppsättningen för gruppen och skala. Ersätt  *#*  med identifieraren för den virtuella datorn som du vill starta och köra den:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

I Resursläsaren, kan vi se att status för instansen är **kör**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Du kan starta alla virtuella datorer i skaluppsättningen genom att inte använda - InstanceId-parameter.

## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Stoppa en virtuell dator i en skaluppsättning
Ersätt angiven värdena med namnet på din resursuppsättningen för gruppen och skala. Ersätt  *#*  med identifieraren för den virtuella datorn som du vill stoppa och sedan köra den:

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

I Resursläsaren, kan vi se att status för instansen är **frigjorts**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

Använd parametern - StayProvisioned om du vill stoppa en virtuell dator och inte frigör den. Du kan stoppa alla virtuella datorer i uppsättningen med hjälp av parametern - InstanceId inte.

## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Starta om en virtuell dator i en skaluppsättning
Ersätt värdet inom citattecken med namnet på resursgruppen och skaluppsättning. Ersätt  *#*  med identifieraren för den virtuella datorn som du vill starta om och kör det:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Du kan starta om alla virtuella datorer i uppsättningen genom att inte använda - InstanceId-parameter.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Ta bort en virtuell dator från en skaluppsättning
Ersätt värdet inom citattecken med namnet på resursgruppen och skaluppsättning. Ersätt  *#*  med identifieraren för den virtuella datorn som du vill ta bort och sedan köra den:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Du kan ta bort virtuella datorns skaluppsättning samtidigt genom att inte använda - InstanceId-parameter.

## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skaluppsättning
Du kan lägga till eller ta bort virtuella datorer genom att ändra uppsättningen kapacitet. Hämta skaluppsättning som du vill ändra, ange kapaciteten som du vill att den ska vara och uppdatera skaluppsättningen med den nya kapaciteten. I de här kommandona ersätter du noterade värden med namnet på resursgruppen och dess skala.

    $vmss = Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
    $vmss.sku.capacity = 5
    Update-AzureRmVmss -ResourceGroupName "resource group name" -Name "scale set name" -VirtualMachineScaleSet $vmss 

Om du tar bort virtuella datorer från skaluppsättning tas de virtuella datorerna med de högsta ID bort först.

