---
title: "Skapa en virtuell dator och lagring för skalbara program i Azure | Microsoft Docs"
description: "Lär dig hur du distribuerar en virtuell dator som används för att köra ett skalbara program med Azure blob storage"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 011812f5e32537321301dad0c654bca341b3606d
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Skapa en virtuell dator och storage-konto för ett skalbart program

Den här kursen ingår i en serie. Den här kursen visar du distribuerar ett program som laddar upp och hämta stora mängder slumpmässiga data med Azure storage-konto. När du är klar har du ett konsolprogram som körs på en virtuell dator som du laddar upp och hämta stora mängder data till ett lagringskonto.

I delen en av serierna kan du lära dig hur du:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapa en virtuell dator
> * Konfigurera ett anpassat skript-tillägg

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
 
Exemplet Överför 50 stora filer till en blob-behållare i ett Azure Storage-konto. Ett lagringskonto ger ett unikt namnområde för att lagra och komma åt dina Azure storage-dataobjekt. Skapa ett lagringskonto i resursgruppen du skapade med hjälp av [New-AzureRmStorageAccount](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccount) kommando.

I följande kommando i stället använda egna globalt unikt namn för Blob storage-konto där du ser den `<blob_storage_account>` platshållare.

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

## <a name="deploy-configuration"></a>Distribuera konfiguration

För den här kursen finns förutsättningar som måste installeras på den virtuella datorn. Tillägget för anpassat skript används för att köra ett PowerShell.skript som utför följande aktiviteter:

> [!div class="checklist"]
> * Installera .NET core 2.0
> * Installera chocolatey
> * Installera GIT
> * Klona lagringsplatsen exempel
> * Återställa NuGet-paket
> * Skapar 50 1 GB-filer med slumpmässiga data

Kör följande cmdlet för att slutföra konfigurationen av den virtuella datorn. Det här steget tar 5 till 15 minuter att slutföra.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to instal .NET core, dependancies, and pre-create the files to upload.
Set-AzureRMVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Nästa steg

I delen en av serierna du lärt dig om att skapa ett lagringskonto, distribuerar en virtuell dator och konfigurera den virtuella datorn med nödvändiga förutsättningar, till exempel hur du:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapa en virtuell dator
> * Konfigurera ett anpassat skript-tillägg

Gå till två tillhör serien att överföra stora mängder data till ett lagringskonto med exponentiell försök och parallellitet.

> [!div class="nextstepaction"]
> [Överför stora mängder av stora filer parallellt till ett lagringskonto](storage-blob-scalable-app-upload-files.md)
