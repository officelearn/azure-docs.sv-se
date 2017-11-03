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
ms.openlocfilehash: 6f7ef46d9c40138c211427845423783fefde5dc3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Installera en SQL &#92; IIS &#92;. NET stack i Azure

Den här självstudiekursen installera en SQL &#92; IIS &#92;. NET stacken med hjälp av Azure PowerShell. Den här stack består av två virtuella datorer som kör Windows Server 2016 med IIS och .NET och andra med SQL Server.

> [!div class="checklist"]
> * Skapa en virtuell dator med hjälp av ny AzVM
> * Installera IIS och .NET Core SDK på den virtuella datorn
> * Skapa en virtuell dator som kör SQL Server
> * Installera SQL Server-tillägg



## <a name="create-a-iis-vm"></a>Skapa en virtuell IIS-dator 

I det här exemplet använder vi den [ny AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) cmdlet i PowerShell-gränssnittet för moln att snabbt skapa en virtuell Windows Server 2016-dator och installera IIS och .NET Framework. IIS och SQL virtuella datorer dela en resursgrupp och virtuella nätverk, så vi skapa variabler för dessa namn.

Klicka på den **prova** knappen längst upp till höger i kodblocket ska starta molnet Shell i det här fönstret. Du blir ombedd att ange autentiseringsuppgifter för den virtuella datorn cmd i Kommandotolken.

```azurepowershell-interactive
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm -Name myIISVM -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

Installera IIS och .NET framework med tillägget för anpassat skript.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName myIISVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>Azure SQL-VM

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

