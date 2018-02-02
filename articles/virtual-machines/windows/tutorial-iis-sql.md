---
title: "Skapa virtuella datorer som kör en SQL- &#92; IIS &#92;. NET stack i Azure | Microsoft Docs"
description: "Självstudiekurs – installera en Azure SQL, IIS, .NET stacken på en Windows-datorer."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6533ab205e07243e2f757ea0a66028e1d140c52b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Installera en SQL &#92; IIS &#92;. NET stack i Azure

Den här självstudiekursen installera en SQL &#92; IIS &#92;. NET stacken med hjälp av Azure PowerShell. Den här stack består av två virtuella datorer som kör Windows Server 2016 med IIS och .NET och andra med SQL Server.

> [!div class="checklist"]
> * Skapa en virtuell dator med hjälp av ny AzVM
> * Installera IIS och .NET Core SDK på den virtuella datorn
> * Skapa en virtuell dator som kör SQL Server
> * Installera SQL Server-tillägg

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 5.1.1 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-iis-vm"></a>Skapa en virtuell IIS-dator 

I det här exemplet använder vi den [ny AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) cmdlet i PowerShell-gränssnittet för moln att snabbt skapa en virtuell Windows Server 2016-dator och installera IIS och .NET Framework. IIS och SQL virtuella datorer dela en resursgrupp och virtuella nätverk, så vi skapa variabler för dessa namn.

Klicka på den **prova** knappen längst upp till höger i kodblocket ska starta molnet Shell i det här fönstret. Du blir ombedd att ange autentiseringsuppgifter för den virtuella datorn cmd i Kommandotolken.

```azurepowershell-interactive
$vmName = "IISVM$(Get-Random)"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRMVm -Name $vmName -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

Installera IIS och .NET framework med tillägget för anpassat skript.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>Azure SQL VM

Vi använder en förkonfigurerad Azure marketplace-avbildning av en SQLServer för att skapa SQL-VM. Vi först skapa den virtuella datorn och sedan vi installerar SQL Server-tillägget på den virtuella datorn. 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

Använd [Set AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) att lägga till den [SQL Server-tillägget](/sql/virtual-machines-windows-sql-server-agent-extension.md) för SQL-VM.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>Nästa steg

I kursen får installerat du en SQL &#92; IIS &#92;. NET stacken med hjälp av Azure PowerShell. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator med hjälp av ny AzVM
> * Installera IIS och .NET Core SDK på den virtuella datorn
> * Skapa en virtuell dator som kör SQL Server
> * Installera SQL Server-tillägg

Gå vidare till nästa kurs att lära dig hur du skyddar IIS-webbserver med SSL-certifikat.

> [!div class="nextstepaction"]
> [Säker IIS-webbserver med SSL-certifikat](tutorial-secure-web-server.md)

