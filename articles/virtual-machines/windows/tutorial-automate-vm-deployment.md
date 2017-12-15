---
title: Anpassa en Windows-dator i Azure | Microsoft Docs
description: "Lär dig hur du använder tillägget för anpassat skript och Key Vault för att anpassa virtuella Windows-datorer i Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 17a6c243aaf73fcd88261870fbdd9e8c936471b8
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Hur du anpassar en Windows-dator i Azure
Om du vill konfigurera virtuella datorer (VM) på ett snabbt och konsekvent sätt önskade vanligtvis någon form av automatisering. Ett vanligt sätt att anpassa en virtuell Windows-dator är att använda [anpassade skript tillägget för Windows](extensions-customscript.md). I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Använda tillägget för anpassat skript för att installera IIS
> * Skapa en virtuell dator som använder tillägget för anpassat skript
> * Visa en IIS-webbplats som körs när tillägget används

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 


## <a name="custom-script-extension-overview"></a>Översikt över tillägget för anpassat skript
Tillägget för anpassat skript hämtar och kör skript på Azure Virtual Machines. Det här tillägget är användbart för post distributionskonfiguration, Programvaruinstallation eller någon annan konfiguration / hanteringsaktivitet. Skript kan hämtas från Azure storage eller GitHub eller tillhandahålls på Azure-portalen på tillägget körtiden.

Tillägget för anpassat skript kan integreras med Azure Resource Manager-mallar och kan också köras med hjälp av Azure CLI, PowerShell, Azure-portalen eller Azure Virtual Machine REST API.

Du kan använda tillägget för anpassat skript med Windows och Linux virtuella datorer.


## <a name="create-virtual-machine"></a>Skapa en virtuell dator
Innan du kan skapa en virtuell dator, skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroupAutomate* i den *EastUS* plats:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupAutomate -Location EastUS
```

Ange en administratör användarnamn och lösenord för de virtuella datorerna med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Nu kan du skapa den virtuella datorn med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapar krävs virtuella nätverkskomponenter, OS-konfigurationen och sedan skapar en virtuell dator med namnet *myVM*:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
New-AzureRmVM -ResourceGroupName myResourceGroupAutomate -Location EastUS -VM $vmConfig
```

Det tar några minuter för de resurser och den virtuella datorn skapas.


## <a name="automate-iis-install"></a>Automatisera installationen av IIS
Använd [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) att installera tillägget för anpassat skript. Tillägget körs `powershell Add-WindowsFeature Web-Server` att installera IIS-webbserver och uppdateringar av *Default.htm* sidan för att visa värdnamnet för den virtuella datorn:

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName myResourceGroupAutomate `
    -ExtensionName IIS `
    -VMName myVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
```


## <a name="test-web-site"></a>Test-webbplats
Hämta offentlig IP-adressen för din belastningsutjämnare med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtar IP-adressen för *myPublicIP* skapade tidigare:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Name myPublicIP | select IpAddress
```

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Webbplatsen visas, inklusive värdnamnet för den virtuella datorn som belastningsutjämnaren distribuerade trafik till som i följande exempel:

![Kör IIS-webbplats](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen automatiskt installera IIS på en virtuell dator. Du har lärt dig att:

> [!div class="checklist"]
> * Använda tillägget för anpassat skript för att installera IIS
> * Skapa en virtuell dator som använder tillägget för anpassat skript
> * Visa en IIS-webbplats som körs när tillägget används

Gå vidare till nästa kurs att lära dig hur du skapar anpassade VM-avbildningar.

> [!div class="nextstepaction"]
> [Skapa anpassade avbildningar av en virtuell dator](./tutorial-custom-images.md)
