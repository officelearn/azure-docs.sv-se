---
title: 'Snabbstart: Skapa en NAT-gateway - Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Den här snabbstarten visar hur du skapar en NAT-gateway med Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 1d5f8d6e0b2499bbecd32e7cb3fda2cd2cad4d19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202254"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>Snabbstart: Skapa en NAT-gateway med Azure PowerShell

Den här snabbstarten visar hur du använder Azure Virtual Network NAT-tjänst. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan slutföra den här självstudien med Azure Cloud Shell eller köra kommandona lokalt.  Om du inte har använt Azure Cloud Shell [loggar du in nu](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet **myResourceGroupNAT** på **platsen eastus2:**

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

Offentliga IP-alternativ för NAT-gateway är:

* **Offentliga IP-adresser**
* **Offentliga IP-prefix**

Båda kan användas med NAT gateway.

Vi lägger till en offentlig IP-adress och ett offentligt IP-prefix i det här scenariot för att demonstrera.

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att komma åt Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) för att skapa en offentlig IP-adressresurs med namnet **myPublicIP** i **myResourceGroupNAT**. Resultatet av det här kommandot lagras i en variabel **$publicIP** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

Använd [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) för att skapa en offentlig IP-prefixresurs med namnet **myPublicIPprefix** i **myResourceGroupNAT**.  Resultatet av det här kommandot lagras i en variabel med namnet **$publicIPPrefix** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gatewayresurs

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gatewayresursen:
  - En offentlig IP-pool och offentligt IP-prefix som ska användas för utgående flöden som översätts av NAT-gatewayresursen.
  - Ändra tidsgränsen för inaktiv tid från standardvärdet 4 minuter till 10 minuter.

Skapa en global Azure [NAT-gateway med New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Resultatet av det här kommandot kommer att skapa en gateway-resurs med namnet **myNATgateway** som använder den offentliga IP-adressen **myPublicIP** och det offentliga IP-prefixet **myPublicIPprefix**. Tidsgränsen för inaktiv tid är inställd på 10 minuter.  Resultatet av det här kommandot lagras i en variabel med namnet **$natGateway** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

Nu fungerar NAT-gatewayen och allt som saknas är att konfigurera vilka undernät i ett virtuellt nätverk som ska använda den.

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Skapa det virtuella nätverket och associera undernätet till gatewayen.

Skapa ett virtuellt nätverk med namnet **myVnet** med ett undernät med namnet **mySubnet** med [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) i **myResourceGroup** med [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Undernätet i det virtuella nätverket är **192.168.0.0/24**.  Resultatet av kommandona lagras i variabler som heter **$subnet** och **$vnet** för senare användning.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

All utgående trafik till Internet-destinationer använder nu NAT-tjänsten.  Det är inte nödvändigt att konfigurera en UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Skapa en virtuell dator för att använda NAT-tjänsten

Vi ska nu skapa en virtuell dator för att använda NAT-tjänsten.  Den här virtuella datorn har en offentlig IP att använda som en offentlig IP på instansnivå så att du kan komma åt den virtuella datorn.  NAT-tjänsten är flödesriktningsmedveten och ersätter standardmålet för Internet i undernätet. Den virtuella datorns offentliga IP-adress används inte för utgående anslutningar.

### <a name="create-public-ip-for-source-vm"></a>Skapa offentlig IP för käll-VM

Vi skapar en offentlig IP som ska användas för att komma åt den virtuella datorn.  Använd [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPVM** i **myResourceGroupNAT**.  Resultatet av det här kommandot lagras i en variabel med namnet **$publicIpVM** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Skapa en NSG- och exponera SSH-slutpunkt för virtuell dator

Standard offentliga IP-adresser är "säkra som standard", måste vi skapa en NSG för att tillåta inkommande åtkomst för ssh. Använd [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) för att skapa en NSG-resurs med namnet **myNSG**. Använd [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) för att skapa en NSG-regel för SSH-åtkomst med namnet **ssh** i **myResourceGroupNAT**.  Resultatet av det här kommandot lagras i en variabel med namnet **$nsg** för senare användning.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Skapa nätverkskort för virtuell dator

Skapa ett nätverksgränssnitt med [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) som heter **myNic**. Det här kommandot associerar den offentliga IP-adressen och nätverkssäkerhetsgruppen. Resultatet av det här kommandot lagras i en variabel med namnet **$nic** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Skapa en virtuell dator

#### <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du redan har ett SSH-nyckelpar kan du hoppa över det här steget.

Använd ssh-keygen för att skapa ett SSH-nyckelpar.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Mer detaljerad information om hur du skapar SSH-nyckelpar, inklusive användning av PuTTy, finns i [Använd SSH-nycklar med Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Om du skapar SSH-nyckelparet med Cloud Shell lagras nyckelparet i en behållaravbildning. Det här [lagringskontot skapas automatiskt](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Ta inte bort lagringskontot eller filresursen i det förrän du har hämtat nycklarna.

#### <a name="create-vm-configuration"></a>Skapa VM-konfiguration

Om du vill skapa en virtuell dator i PowerShell skapar du en konfiguration som har inställningar för avbildningen att använda, storlek och autentiseringsalternativ. Konfigurationen används för att skapa den virtuella datorn.

Definiera SSH-autentiseringsuppgifterna, informationen om operativsystemet och VM-storleken. I det här exemplet lagras SSH-nyckeln i ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Kombinera konfigurationsdefinitionerna för att skapa en virtuell dator med namnet **myVM** med [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) i **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Vänta tills den virtuella datorn förbereder sig för att distribuera och fortsätt sedan med resten av stegen.

## <a name="discover-the-ip-address-of-the-vm"></a>Upptäck DEN virtuella datorns IP-adress

Först måste vi upptäcka IP-adressen för den virtuella datorn som du har skapat. Om du vill hämta den offentliga IP-adressen för den virtuella datorn använder du [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra sedan in den i ett anteckningsblock så att du kan använda den för att komma åt den virtuella datorn.

### <a name="sign-in-to-vm"></a>Logga in på virtuell dator

SSH-autentiseringsuppgifterna ska lagras i ditt Cloud Shell från den föregående åtgärden.  Öppna ett [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn.

```bash
ssh azureuser@<ip-address-destination>
```

Du är nu redo att använda NAT-tjänsten.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [Ta bort AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) för att ta bort resursgruppen och alla resurser som finns i.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en NAT-gateway och en virtuell dator för att använda den. 

Granska mått i Azure Monitor för att se nat-tjänsten fungera. Diagnostisera problem som resursutmattning av tillgängliga SNAT-portar.  Resursutmattning av SNAT-portar åtgärdas genom att lägga till ytterligare offentliga IP-adressresurser eller offentliga IP-prefixresurser eller båda.


- Lär dig mer om [AZURE Virtual Network NAT](./nat-overview.md)
- Lär dig mer om [NAT-gatewayresurs](./nat-gateway-resource.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure-portalen](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]


