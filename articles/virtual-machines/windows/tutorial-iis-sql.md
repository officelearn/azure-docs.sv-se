---
title: Självstudie, skapa virtuella datorer som kör en SQL-, IIS-, .NET-stack i Azure| Microsoft Docs
description: I den här självstudien lär du dig hur du installerar Azure SQL, IIS, .NET-stacken på en virtuell Windows-dator i Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 4909edf6e434e626e89409f01ae0f5fbca5bf442
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515496"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Självstudie: Installera SQL-, IIS-, .NET-stacken på en virtuell Windows-dator med Azure PowerShell

I den här självstudien installerar vi en SQL-, IIS-, .NET-stack med Azure PowerShell. Stacken består av två virtuella datorer som kör Windows Server 2016, en med IIS och .NET och den andra med SQL Server.

> [!div class="checklist"]
> * Skapa en virtuell dator 
> * Installera IIS och .NET Core SDK på den virtuella datorn
> * Skapa en virtuell dator som kör SQL Server
> * Installera SQL Server-tillägget

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AzureRM.Compute-modulversion 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM.Compute` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="create-a-iis-vm"></a>Skapa en virtuell IIS-dator 

I det här exemplet använder vi cmdleten [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) i PowerShell Cloud Shell för att snabbt skapa en virtuell dator i Windows Server 2016 och sedan installera IIS och .NET Framework. De virtuella IIS- och SQL-datorerna delar en resursgrupp och ett virtuellt nätverk, så vi skapar variabler för de namnen.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Installera IIS och .NET Framework med det anpassade skripttillägget med cmdleten [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension).

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Skapa ett nytt undernät

Skapa ett till undernät för den virtuella SQL-datorn. Hämta vNet med [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Skapa en konfiguration för undernätet med [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Uppdatera vNet med den nya undernätsinformationen med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>Virtuell Azure SQL-dator

Använd en förkonfigurerad Azure Marketplace-avbildning av en SQL-server för att skapa den virtuella SQL-datorn. Först skapar vi den virtuella datorn och sedan installerar vi SQL Server-tillägget på den virtuella datorn. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Använd [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) för att lägga till [SQL Server-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) till den virtuella SQL-datorn.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du installerat en SQL&#92;IIS&#92;.NET-stack med Azure PowerShell. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator 
> * Installera IIS och .NET Core SDK på den virtuella datorn
> * Skapa en virtuell dator som kör SQL Server
> * Installera SQL Server-tillägget

Gå vidare till nästa självstudiekurs där du får lära dig att skydda IIS-webbservern med SSL-certifikat.

> [!div class="nextstepaction"]
> [Säker IIS-webbserver med SSL-certifikat](tutorial-secure-web-server.md)

