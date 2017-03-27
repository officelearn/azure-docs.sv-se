---
title: "Azure snabbstart – skapa virtuell dator med PowerShell | Microsoft Docs"
description: "Lär dig att snabbt skapa en virtuell Linux-dator med PowerShell"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: b2526e639c131627838e6ea0254601e80633f7f3
ms.lasthandoff: 03/21/2017


---

# <a name="create-a-linux-virtual-machine-with-powershell"></a>Skapa en virtuell Linux-dator med PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här guiden beskriver hur man använder PowerShell för att skapa en virtuell Azure-dator som kör Ubuntu 14.04 LTS.

Innan du börjar måste en offentlig SSH-nyckel med namnet `id_rsa.pub` lagras i katalogen `.ssh` i din Windows-användarprofil. Mer detaljerad information om hur du skapar SSH-nycklar för Azure finns i [Create SSH keys for Azure](./virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Skapa SSH-nycklar för Azure).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp. En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-networking-resources"></a>Skapa nätverksresurser

Skapa ett virtuellt nätverk, undernät och offentlig IP-adress. Dessa resurser används för att skapa nätverksanslutning till den virtuella datorn och ansluta den till internet.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location westeurope `
-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location westeurope `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Skapa en nätverkssäkerhetsgrupp och en regel för nätverkssäkerhetsgrupp. Nätverkssäkerhetsgruppen skyddar den virtuella datorn med hjälp av regler för inkommande och utgående trafik. I detta fall skapas en regel för inkommande trafik för port 22, som tillåter inkommande SSH-anslutningar.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location westeurope `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH
```

Skapa ett nätverkskort för den virtuella datorn. Nätverkskortet ansluter den virtuella datorn till ett undernät, en nätverkssäkerhetsgrupp och en offentlig IP-adress.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location westeurope `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell datorkonfiguration. Den här konfigurationen innehåller de inställningar som används vid distribution av den virtuella datorn, t.ex. den virtuella datorns avbildning, storlek och konfiguration för verifiering.

```powershell
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName myVM -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName Canonical -Offer UbuntuServer -Skus 14.04.2-LTS -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Skapa den virtuella datorn.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

När distributionen har slutförts kan du skapa en SSH-anslutning med den virtuella datorn.

Kör följande kommandon för att returnera den offentliga IP-adressen för den virtuella datorn.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Från en dator med SSH installerat använder du följande kommando för att ansluta till den virtuella datorn. Om du arbetar med Windows kan du skapa anslutningen med hjälp av [Putty](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-ssh-from-windows?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-private-key-for-putty). 

```bash 
ssh <Public IP Address>
```

När du får en uppmaning är `azureuser` användarnamnet för inloggningen. Om du angav en lösenfras när du skapade SSH-nycklar måste du även ange den.

## <a name="delete-virtual-machine"></a>Ta bort en virtuell dator

När den inte längre behövs kan följande kommando användas för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudie: Skapa virtuella datorer med hög tillgänglighet](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Utforska exempel på distribution av virtuella datorer med PowerShell](./virtual-machines-linux-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

