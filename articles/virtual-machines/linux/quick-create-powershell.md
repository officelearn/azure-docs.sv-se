---
title: Snabbstart – Skapa en virtuell Linux-dator med Azure PowerShell | Microsoft Docs
description: I den här snabbstarten lär du dig hur du använder Azure PowerShell för att skapa en virtuell Linux-dator
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e910ced35738fcba27a8a1d7f2f010b2cd42e55d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Snabbstart: Skapa en virtuell Linux-dator i Azure med PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure PowerShell-modulen för att distribuera en virtuell Linux-dator (VM) i Azure som kör Ubuntu. För att se hur den virtuella datorn fungerar i praktiken använder du sedan SSH för att ansluta till den virtuella datorn och installerar NGINX-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt krävs Azure PowerShell-modulen version 5.7.0 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

Slutligen måste du ha en offentlig SSH-nyckel med namnet *id_rsa.pub* i katalogen *.ssh* i din Windows-användarprofil. Mer detaljerad information om hur du skapar och använder SSH-nycklar finns i [Create SSH keys for Azure](ssh-from-windows.md) (Skapa SSH-nycklar för Azure).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Skapa virtuella nätverksresurser

Skapa ett virtuellt nätverk, undernät och offentlig IP-adress. Dessa resurser används för att tillhandahålla nätverksanslutning till den virtuella datorn och ansluta den till Internet:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myVNET" -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Skapa en Azure-nätverkssäkerhetsgrupp och trafikregel. Nätverkssäkerhetsgruppen skyddar den virtuella datorn med regler för inkommande och utgående trafik. I följande exempel skapas en regel för inkommande trafik för TCP-port 22 som tillåter SSH-anslutningar. För att tillåta inkommande webbtrafik skapas också en regel för inkommande trafik för TCP-port 80.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleSSH"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleWWW"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myNetworkSecurityGroup" -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Skapa ett virtuellt nätverkskort (NIC) med [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Det virtuella nätverkskortet ansluter den virtuella datorn till ett undernät, en nätverkssäkerhetsgrupp och en offentlig IP-adress.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name "myNic" -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Konfigurationen av en virtuell dator innehåller inställningarna som används när en virtuell dator distribueras, t.ex. alternativ för VM-avbildning, storlek och autentisering. Definiera SSH-autentiseringsuppgifterna, informationen om operativsystemet och VM-storleken på följande sätt:

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName "myVM" -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Kombinera de tidigare konfigurationsdefinitionerna för att skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

När distributionen har slutförts ansluter du till den virtuella datorn med SSH. För att sedan se hur den virtuella datorn fungerar i praktiken installerar du NGINX-webbservern.

Du hämtar den virtuella datorns offentliga IP-adress genom att köra cmdleten [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Använd en SSH-klient för att ansluta till den virtuella datorn. Du kan använda Azure Cloud Shell-gränssnittet från en webbläsare. Om du använder Windows kan du använda [Putty](ssh-from-windows.md) eller [Windows-undersystemet för Linux](/windows/wsl/install-win10). Ange den virtuella datorns offentliga IP-adress:

```bash
ssh azureuser@IpAddress
```

När du får en uppmaning är *azureuser* användarnamnet för inloggningen. Om du använder en lösenfras med dina SSH-nycklar måste du ange den när du uppmanas att göra det.

## <a name="install-web-server"></a>Installera webbservern

Om du vill se hur den virtuella datorn fungerar i praktiken installerar du NGINX-webbservern. Om du vill uppdatera paketkällorna och installera det senaste NGINX-paketet kör du följande kommandon från SSH-sessionen:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

När du är klar avslutar du SSH-sessionen med `exit`

## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

När NGINX har installerats och port 80 är öppen på den virtuella datorn från Internet använder du valfri webbläsare för att visa standardvälkomstsidan för NGINX. Använd den virtuella datorns offentliga IP-adress, som du hämtade i ett tidigare steg. Följande exempel visar NGINX-standardwebbplatsen:

![NGINX-standardwebbplats](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du cmdleten [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, skapade en nätverkssäkerhetsgrupp och en regel och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)
