---
title: Skapa en virtuell dator och ett lagringskonto för ett skalbart program i Azure | Microsoft Docs
description: Lär dig hur du distribuerar en virtuell dator som ska användas för att köra ett skalbart program med Azure Blob Storage
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: fdc88f4ba893eed516aeca174e68fdda12a42a0f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Skapa en virtuell dator och ett lagringskonto för ett skalbart program

Den här självstudien ingår i en serie. Den här självstudien visar hur du distribuerar ett program som överför och laddar ner stora mängder slumpmässiga data med ett Azure-lagringskonto. När du är klar har du ett konsolprogram som körs på en virtuell dator där du laddar upp och laddar ner stora mängder data till ett lagringskonto.

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapa en virtuell dator
> * Konfigurera ett anpassat skripttillägg

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
 
I exemplet laddas 50 stora filer upp till en blobbehållare på ett Azure Storage-konto. Ett Azure-lagringskonto tillhandahåller en unik namnrymd där du kan lagra och få åtkomst till dina Azure-lagringdataobjekt. Skapa ett lagringskonto i resursgruppen som du skapade med kommandot [New-AzureRmStorageAccount](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccount).

I följande kommando infogar du ditt globalt unika lagringskontonamn på blobblagringskontot istället för platshållaren `<blob_storage_account>`.

```powershell-interactive
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell datorkonfiguration. Den här konfigurationen innehåller de inställningar som används vid distribution av den virtuella datorn, t.ex. den virtuella datorns avbildning, storlek och konfiguration för verifiering. När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

Skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Distribuera konfigurationen

I den här självstudien finns det förutsättningar för vad som måste installeras på den virtuella datorn. Det anpassade skripttillägget används för att köra ett PowerShell-skript som utför följande uppgifter:

> [!div class="checklist"]
> * Installera .NET Core 2.0
> * Installera Chocolatey
> * Installera GIT
> * Klona lagringsplatsexemplet
> * Återställ NuGet-paketen
> * Skapa 50 stycken 1 GB-filer med slumpmässiga data

Kör följande cmdlet för att slutföra konfigurationen av den virtuella datorn. Det här steget tar 5 till 15 minuter att slutföra.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzureRMVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Nästa steg

I del ett av serien lärde du dig att skapa ett lagringskonto, distribuera en virtuell dator och konfigurera den virtuella datorn med nödvändiga förutsättningar, till exempel:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapa en virtuell dator
> * Konfigurera ett anpassat skripttillägg

Gå till del två i serien för att ladda upp stora mängder data till ett lagringskonto med exponentiellt återförsök och parallellitet.

> [!div class="nextstepaction"]
> [Överföra stora mängder med stora filer parallellt till ett lagringskonto](storage-blob-scalable-app-upload-files.md)
