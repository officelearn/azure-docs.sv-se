---
title: Överför en generaliserad virtuell hårddisk till Azure PowerShell-skriptexemplet| Microsoft Docs
description: PowerShell-exempelskript ska överföra en generaliserad virtuell Hårddisk till Azure och skapa en ny virtuell dator med hjälp av resource manager-distributionsmodellen och hanterade diskar.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 95826cb3928f9a3220505040ef3162fe5016d08b
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447624"
---
# <a name="sample-script-to-upload-a-vhd-to-azure-and-create-a-new-vm"></a>Exempelskript för att överföra en virtuell hårddisk till Azure och skapa en ny virtuell dator

Det här skriptet tar en lokal VHD-fil från en generaliserad virtuell dator och överför den till Azure, skapar en hanterad diskavbildning och använder den för att skapa en ny virtuell dator.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Exempelskript

```powershell
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'EastUS'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$containername = 'mycontainer'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vmName = 'myVM'
$imageName = 'myImage'
$vhdName = 'myUploadedVhd.vhd'
$diskSizeGB = '128'
$subnetName = 'mySubnet'
$vnetName = 'myVnet'
$ipName = 'myPip'
$nicName = 'myNic'
$nsgName = 'myNsg'
$ruleName = 'myRdpRule'
$computerName = 'myComputerName'
$vmSize = 'Standard_DS1_v2'

# Get the username and password to be used for the administrators account on the VM. 
# This is used when connecting to the VM using RDP.

$cred = Get-Credential

# Upload the VHD
New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
    -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ('https://' + $storageaccount + '.blob.core.windows.net/' + $containername + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath

# Note: Uploading the VHD may take awhile!

# Create a managed image from the uploaded VHD 
$imageConfig = New-AzImageConfig -Location $location
$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized `
    -BlobUri $urlOfUploadedImageVhd
$image = New-AzImage -ImageName $imageName -ResourceGroupName $resourceGroup -Image $imageConfig
 
# Create the networking resources
$singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
$pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $resourceGroup -Location $location `
    -AllocationMethod Dynamic
$rdpRule = New-AzNetworkSecurityRuleConfig -Name $ruleName -Description 'Allow RDP' -Access Allow `
    -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
$nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $resourceGroup -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
$vnet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName

# Start building the VM configuration
$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize

# Set the VM image as source image for the new VM
$vm = Set-AzVMSourceImage -VM $vm -Id $image.Id

# Finish the VM configuration and add the NIC.
$vm = Set-AzVMOSDisk -VM $vm  -DiskSizeInGB $diskSizeGB -CreateOption FromImage -Caching ReadWrite
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

# Create the VM
New-AzVM -VM $vm -ResourceGroupName $resourceGroup -Location $location

# Verify that the VM was created
$vmList = Get-AzVM -ResourceGroupName $resourceGroup
$vmList.Name


```


<!-- 
[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")] -->

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando                                                                                                             | Anteckningar                                                                                                                                                                                |
|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)                           | Skapar en resursgrupp där alla resurser lagras.                                                                                                                          |
| [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)                         | Skapar ett lagringskonto.                                                                                                                                                           |
| [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd)                                               | Överför en virtuell hårddisk från en lokal virtuell dator till en blob i ett molnlagringskonto i Azure.                                                                       |
| [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig)                               | Skapar ett konfigurerbart avbildningsobjekt.                                                                                                                                                 |
| [Set-AzImageOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azimageosdisk)                               | Anger operativsystemets diskegenskaper i ett avbildningsobjekt.                                                                                                                        |
| [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage)                                           | Skapar en ny avbildning.                                                                                                                                                                 |
| [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk.                                                                                |
| [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)                         | Skapar ett virtuellt nätverk.                                                                                                                                                           |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress)                       | Skapar en offentlig IP-adress.                                                                                                                                                         |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface)                     | Skapar ett nätverksgränssnitt.                                                                                                                                                         |
| [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig)   | Skapar en regelkonfiguration för nätverkssäkerhetsgrupper. Konfigurationen används för att skapa en NSG-regel när NSG:n skapas.                                                       |
| [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup)             | Skapar en nätverkssäkerhetsgrupp.                                                                                                                                                    |
| [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)                         | Hämtar ett virtuellt nätverk i en resursgrupp.                                                                                                                                          |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig)                                     | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage)                           | Anger en avbildning för en virtuell dator.                                                                                                                                            |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk)                                     | Anger operativsystemets diskegenskaper på en virtuell dator.                                                                                                                      |
| [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem)                   | Anger operativsystemets diskegenskaper på en virtuell dator.                                                                                                                      |
| [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)                 | Lägger till ett nätverksgränssnitt i en virtuell dator.                                                                                                                                       |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)                                                 | Skapa en virtuell dator.                                                                                                                                                            |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup)                     | Tar bort en resursgrupp och alla resurser som ingår i gruppen.                                                                                                                         |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
