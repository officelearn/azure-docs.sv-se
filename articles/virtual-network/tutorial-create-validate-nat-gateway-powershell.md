---
title: 'Självstudiekurs: Skapa och testa en NAT-gateway - Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Den här självstudien visar hur du skapar en NAT-gateway med Azure PowerShell och testar NAT-tjänsten
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 61cda5e61d14c4eeaf2d88483603707598b1c911
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202249"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Självstudiekurs: Skapa en NAT-gateway med Azure PowerShell och testa NAT-tjänsten

I den här självstudien skapar du en NAT-gateway för att tillhandahålla utgående anslutning för virtuella datorer i Azure. Om du vill testa NAT-gatewayen distribuerar du en virtuell dator för källa och mål. Du ska testa NAT-gatewayen genom att skapa utgående anslutningar till en offentlig IP-adress. Dessa anslutningar kommer från källan till den virtuella måldatorn. Den här självstudien distribuerar källa och mål i två olika virtuella nätverk i samma resursgrupp för enkelhetens skull.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan slutföra den här självstudien med Azure Cloud Shell eller köra respektive kommandon lokalt.  Om du aldrig har använt Azure Cloud Shell bör du [logga in nu](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet **myResourceGroupNAT** på **platsen eastus2:**


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att komma åt Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPsource** i **myResourceGroupNAT**. Resultatet av det här kommandot lagras i en variabel med namnet **$publicIPsource** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

 Använd [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) för att skapa en offentlig IP-prefixresurs med namnet **myPublicIPprefixsource** i **myResourceGroupNAT**.  Resultatet av det här kommandot lagras i en variabel med namnet **$publicIPPrefixsource** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gatewayresurs

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gatewayresursen:
  - En offentlig IP-pool och offentligt IP-prefix som ska användas för utgående flöden som översätts av NAT-gatewayresursen.
  - Ändra tidsgränsen för inaktiv tid från standardvärdet 4 minuter till 10 minuter.

Skapa en global Azure [NAT-gateway med New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). Resultatet av det här kommandot kommer att skapa en gateway-resurs med namnet **myNATgateway** som använder den offentliga IP-adressen **myPublicIPsource** och det offentliga IP-prefixet **myPublicIPprefixsource**. Tidsgränsen för inaktiv tid är inställd på 10 minuter.  Resultatet av det här kommandot lagras i en variabel med namnet **$natGateway** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

Nu fungerar NAT-gatewayen och allt som saknas är att konfigurera vilka undernät i ett virtuellt nätverk som ska använda den.

## <a name="prepare-the-source-for-outbound-traffic"></a>Förbereda källan för utgående trafik

Vi guidar dig genom installation av en fullständig testmiljö. Du konfigurerar ett test med verktyg med öppen källkod för att verifiera NAT-gatewayen. Vi börjar med källan, som kommer att använda NAT gateway vi skapade tidigare.

### <a name="configure-virtual-network-for-source"></a>Konfigurera virtuellt nätverk för källa

Skapa det virtuella nätverket och associera undernätet till gatewayen.

Skapa ett virtuellt nätverk med namnet **myVnetsource** med ett undernät med namnet **mySubnetsource** med [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) i **myResourceGroupNAT** med [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Undernätet i det virtuella nätverket är **192.168.0.0/24**.  Resultatet av kommandona lagras i variabler med namnet **$subnetsource** och **$vnetsource** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

All utgående trafik till Internet-destinationer använder nu NAT-tjänsten.  Det är inte nödvändigt att konfigurera en UDR.

Innan vi kan testa NAT-gatewayen måste vi skapa en käll-VM.  Vi tilldelar en offentlig IP-adressresurs som en offentlig IP-ip på instansnivå för åtkomst till den här virtuella datorn utifrån. Den här adressen används endast för att komma åt den för testet.  Vi visar hur NAT-tjänsten har företräde framför andra utgående alternativ.

Du kan också skapa den här virtuella datorn utan en offentlig IP och skapa en annan virtuell dator som ska användas som en jumpbox utan en offentlig IP som en övning.

### <a name="create-public-ip-for-source-vm"></a>Skapa offentlig IP för käll-VM

Vi skapar en offentlig IP som ska användas för att komma åt den virtuella datorn.  Använd [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPVM** i **myResourceGroupNAT**.  Resultatet av det här kommandot lagras i en variabel med namnet **$publicIpsourceVM** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Skapa en NSG- och exponera SSH-slutpunkt för virtuell dator

Eftersom standard offentliga IP-adresser är "säkra som standard", skapar vi en NSG för att tillåta inkommande åtkomst för ssh. NAT-tjänsten är flödesriktningsmedveten. Den här NSG-funktionen används inte för utgående när NAT-gatewayen har konfigurerats i samma undernät. Använd [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) för att skapa en NSG-resurs med namnet **myNSGsource**. Använd [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) för att skapa en NSG-regel för SSH-åtkomst med namnet **ssh** i **myResourceGroupNAT**. Resultatet av det här kommandot lagras i variabeln **$nsgsource** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>Skapa nätverkskort för käll-VM

Skapa ett nätverksgränssnitt med [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) som heter **myNicsource**. Det här kommandot associerar den offentliga IP-adressen och nätverkssäkerhetsgruppen. Resultatet av det här kommandot lagras i en variabel med namnet **$nicsource** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Skapa en virtuell källa

#### <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du redan har ett SSH-nyckelpar kan du hoppa över det här steget.

Använd ssh-keygen för att skapa ett SSH-nyckelpar.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Mer detaljerad information om hur du skapar SSH-nyckelpar, inklusive användning av PuTTy, finns i [Använd SSH-nycklar med Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Om du skapar SSH-nyckelparet med Cloud Shell lagras nyckelparet i en behållaravbildning. Det här [lagringskontot skapas automatiskt](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Ta inte bort lagringskontot eller filresursen i det förrän du har hämtat nycklarna.

#### <a name="create-vm-configuration"></a>Skapa VM-konfiguration

Om du vill skapa en virtuell dator i PowerShell skapar du en konfiguration som har inställningar som avbildningen för alternativ för användning, storlek och autentisering. Konfigurationen används sedan till att skapa den virtuella datorn.

Definiera SSH-autentiseringsuppgifterna, informationen om operativsystemet och VM-storleken. I det här exemplet lagras SSH-nyckeln i ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Kombinera konfigurationsdefinitionerna för att skapa en virtuell dator med namnet **myVMsource** med [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) i **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Medan kommandot kommer tillbaka omedelbart, kan det ta några minuter för den virtuella datorn att få distribueras.

## <a name="prepare-destination-for-outbound-traffic"></a>Förbered destination för utgående trafik

Vi skapar nu en destination för den utgående trafik som översätts av NAT-tjänsten så att du kan testa den.

### <a name="configure-virtual-network-for-destination"></a>Konfigurera virtuellt nätverk för mål

Vi måste skapa ett virtuellt nätverk där målet virtuell maskin kommer att vara.  Dessa kommandon är samma steg som för källdatorn. Små ändringar har lagts till för att exponera målslutpunkten.

Skapa ett virtuellt nätverk med namnet **myVnetdestination** med ett undernät med namnet **mySubnetdestination** med [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) i **myResourceGroupNAT** med [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Undernätet i det virtuella nätverket är **192.168.0.0/24**.  Resultatet av kommandona lagras i variabler som heter **$subnetdestination** och **$vnetdestination** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Skapa offentlig IP för mål-VM

Vi skapar en offentlig IP som ska användas för att komma åt måldatorn.  Använd [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPdestinationVM** i **myResourceGroupNAT**.  Resultatet av det här kommandot lagras i en variabel med namnet **$publicIpdestinationVM** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Skapa en NSG- och exponera SSH- och HTTP-slutpunkt för virtuell dator

Standard offentliga IP-adresser är "säkra som standard", vi skapar en NSG för att tillåta inkommande åtkomst för ssh. Använd [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) för att skapa en NSG-resurs med namnet **myNSGdestination**. Använd [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) för att skapa en NSG-regel för SSH-åtkomst med namnet **ssh**.  Använd [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) för att skapa en NSG-regel för HTTP-åtkomst med namnet **http**. Båda reglerna skapas i **myResourceGroupNAT**. Resultatet av det här kommandot lagras i en variabel med namnet **$nsgdestination** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>Skapa nätverkskort för mål-VM

Skapa ett nätverksgränssnitt med [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) som heter **myNicdestination**. Det här kommandot associerar med den offentliga IP-adressen och nätverkssäkerhetsgruppen. Resultatet av det här kommandot lagras i en variabel med namnet **$nicdestination** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Skapa en virtuell måldator

#### <a name="create-vm-configuration"></a>Skapa VM-konfiguration

Om du vill skapa en virtuell dator i PowerShell skapar du en konfiguration som har inställningar som avbildningen för alternativ för användning, storlek och autentisering. Konfigurationen används sedan till att skapa den virtuella datorn.

Definiera SSH-autentiseringsuppgifterna, informationen om operativsystemet och VM-storleken. I det här exemplet lagras SSH-nyckeln i ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Kombinera konfigurationsdefinitionerna för att skapa en virtuell dator med namnet **myVMdestination** med [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) i **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Medan kommandot kommer tillbaka omedelbart, kan det ta några minuter för den virtuella datorn att få distribueras.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Förbereda en webbserver och testa nyttolasten på måldatorn

Först måste vi upptäcka IP-adressen för målet VM.  Om du vill hämta den offentliga IP-adressen för den virtuella datorn använder du [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra sedan in den i ett anteckningsblock så att du kan använda den i efterföljande steg. Ange att det här är den virtuella måldatorn.

### <a name="sign-in-to-destination-vm"></a>Logga in på mål-VM

SSH-autentiseringsuppgifterna ska lagras i ditt Cloud Shell från den föregående åtgärden.  Öppna ett [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn. 

```bash
ssh azureuser@<ip-address-destination>
```

Kopiera och klistra in följande kommandon när du har loggat in.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Dessa kommandon uppdaterar din virtuella dator, installerar nginx och skapar en 100-KBytes-fil. Den här filen hämtas från källdatorn med NAT-tjänsten.

Stäng SSH-sessionen med den virtuella måldatorn.

## <a name="prepare-test-on-source-vm"></a>Förbereda test på käll-VM

Först måste vi upptäcka IP-adressen för käll-VM.  Om du vill hämta den offentliga IP-adressen för den virtuella datorn använder du [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra sedan in den i ett anteckningsblock så att du kan använda den i efterföljande steg. Ange att det här är den virtuella datorn för källan.

### <a name="log-into-source-vm"></a>Logga in på käll-VM

Återigen lagras SSH-autentiseringsuppgifterna i Cloud Shell. Öppna en ny flik för [Azure Cloud Shell](https://shell.azure.com) i webbläsaren.  Använd IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn. 

```bash
ssh azureuser@<ip-address-source>
```

Kopiera och klistra in följande kommandon för att förbereda för testning av NAT-tjänsten.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Dessa kommandon kommer att uppdatera din virtuella dator, installera gå, installera [hey](https://github.com/rakyll/hey) från GitHub och uppdatera din skalmiljö.

Du är nu redo att testa NAT-tjänsten.

## <a name="validate-nat-service"></a>Validera NAT-tjänsten

När du är inloggad på källdatorn kan du använda **curl** and **hey** för att generera begäranden till mål-IP-adressen.

Använd curl för att hämta filen 100-KBytes.  Ersätt ** \<ip-adress-mål>** i exemplet nedan med den mål-IP-adress som du tidigare har kopierat.  Parametern **--output** anger att den hämtade filen kommer att ignoreras.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Du kan också generera en serie begäranden med **hey**. Återigen, ersätta ** \<ip-adress-mål>** med målet IP-adress som du tidigare har kopierat.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Det här kommandot genererar 100 begäranden, 10 samtidigt, med en timeout på 30 sekunder. TCP-anslutningen kommer inte att återanvändas.  Varje begäran hämtar 100 Kbyte.  I slutet av körningen, **hey** kommer att rapportera en del statistik om hur bra NAT-tjänsten gjorde.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [Ta bort AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) för att ta bort resursgruppen och alla resurser som finns i.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Nästa steg
I den här självstudien skapade du en NAT-gateway, skapade en käll- och mål-VM och testade sedan NAT-gatewayen.

Granska mått i Azure Monitor för att se nat-tjänsten fungera. Diagnostisera problem som resursutmattning av tillgängliga SNAT-portar.  Resursutmattning av SNAT-portar åtgärdas enkelt genom att lägga till ytterligare offentliga IP-adressresurser eller offentliga IP-prefixresurser eller båda.

- Lär dig mer om [NAT för virtuellt nätverk](./nat-overview.md)
- Lär dig mer om [NAT-gatewayresurs](./nat-gateway-resource.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure-portalen](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

