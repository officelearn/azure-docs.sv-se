---
title: 'Snabb start: skapa en intern belastningsutjämnare – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Den här snabb starten visar hur du skapar en intern belastningsutjämnare med hjälp av Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: e1fa97fb8a3dcae3a78ba0bc85cf59db2c167dea
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696803"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Snabb start: skapa en intern belastningsutjämnare för att belastningsutjämna virtuella datorer med Azure PowerShell

Kom igång med Azure Load Balancer genom att använda Azure PowerShell för att skapa en intern belastningsutjämnare och två virtuella datorer.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installerat lokalt eller Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* Med namnet **CreateIntLBQS-RG**.
* På den **östra** platsen.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar. Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du distribuerar virtuella datorer och testar belastningsutjämnaren, skapar du de stödda virtuella nätverks resurserna.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Skapa ett virtuellt nätverk och en Azure skydds-värd

Skapa ett virtuellt nätverk med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Med namnet **myVNet**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Undernät med namnet **myBackendSubnet**.
* Virtuellt nätverk **10.0.0.0/16**.
* Undernät **10.0.0.0/24**.
* Undernät med namnet **AzureBastionSubnet**.
* Undernät **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Skapa en offentlig IP-adress för Azure skydds-värden

Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en offentlig IP-adress för skydds-värden:

* Med namnet **myPublicIPBastion**
* I resurs gruppen **CreateIntLBQS-RG**.
* På den **östra** platsen.
* **Statisk** Metod för allokering.
* **Standard** -SKU.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Skapa en Azure skydds-värd

Använd [New-AzBastion](/powershell/module/az.network/new-azbastion) för att skapa en skydds-värd:

* Med namnet **myBastion**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I virtuellt nätverk **myVNet**.
* Associerat med offentliga IP- **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Det kan ta några minuter innan Azure skydds-värden kan distribueras.

### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp
Skapa en nätverkssäkerhetsgrupp så att du kan definiera inkommande anslutningar till det virtuella nätverket.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Skapa en regel för nätverkssäkerhetsgruppen för port 80
Skapa en regel för nätverks säkerhets grupp med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Med namnet **myNSGRuleHTTP**.
* Beskrivning av **Tillåt http**.
* Åtkomst till **Tillåt**.
* Protokoll **(*)**.
* Riktningen **inkommande**.
* Prioritet **2000**.
* **Internet** källa.
* Käll port intervall för **(*)**.
* Mål adressens prefix för **(*)**.
* Mål **Port 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverks säkerhets grupp med [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Med namnet **myNSG**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I **östasiatiska** platser.
* Med säkerhets regler som skapats i föregående steg som lagras i en variabel.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Skapa standard Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-pool för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  * En server dels-IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.
  * En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  * En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-frontend-ip"></a>Skapa klientdels-IP

Skapa en frontend-IP med [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Med namnet **frontend**.
* Privat IP-adress för **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Konfigurera en serverdelsadresspool

Skapa en backend-adresspool med [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Med namnet **myBackEndPool**.
* De virtuella datorerna ansluter till den här serverdelspoolen i de återstående stegen.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. 

En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Övervakar hälso tillståndet för de virtuella datorerna.
* Med namnet **myHealthProbe**.
* Protokoll- **TCP**.
* Övervaknings **Port 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i frontend **-poolen för klient delen.**
* Skickar belastningsutjämnad nätverks trafik till Server dels adresspoolen **myBackEndPool** med **port 80**. 
* Använda **myHealthProbe** för hälso avsökning.
* Protokoll- **TCP**.
* Tids gräns för inaktivitet på **15 minuter**.
* Aktivera TCP-återställning.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```
>[!NOTE]
>De virtuella datorerna i backend-poolen har ingen utgående Internet anslutning med den här konfigurationen. </br> Mer information om hur du tillhandahåller utgående anslutningar finns i: </br> **[Utgående anslutningar i Azure](load-balancer-outbound-connections.md)**</br> Alternativ för att tillhandahålla anslutning: </br> **[Konfiguration för lastbalanserare med ”endast utgående”](egress-only.md)** </br> **[Vad är Virtual Network NAT?](../virtual-network/nat-overview.md)**


### <a name="create-load-balancer-resource"></a>Skapa belastnings Utjämnings resurs

Skapa en intern belastningsutjämnare med [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Med namnet **myLoadBalancer**
* I **öster**.
* I resurs gruppen **CreateIntLBQS-RG**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Skapa nätverksgränssnitt

Skapa tre nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Med namnet **myNicVM1**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I **östasiatiska** platser.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Med namnet **myNicVM2**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I **östasiatiska** platser.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Ange ett administratörsanvändarnamn och lösenord för de virtuella datorerna med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Skapa de virtuella datorerna med:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Med namnet **myVM1**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM1**.
* Ansluten till belastningsutjämnarens **myLoadBalancer**.
* I **zon 1**.
* På den **östra** platsen.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* Med namnet **myVM2**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM2**.
* Ansluten till belastningsutjämnarens **myLoadBalancer**.
* I **zon 2**.
* På den **östra** platsen.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar. Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du distribuerar virtuella datorer och testar belastningsutjämnaren, skapar du de stödda virtuella nätverks resurserna.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Skapa ett virtuellt nätverk och en Azure skydds-värd

Skapa ett virtuellt nätverk med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Med namnet **myVNet**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Undernät med namnet **myBackendSubnet**.
* Virtuellt nätverk **10.0.0.0/16**.
* Undernät **10.0.0.0/24**.
* Undernät med namnet **AzureBastionSubnet**.
* Undernät **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Skapa en offentlig IP-adress för Azure skydds-värden

Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en offentlig IP-adress för skydds-värden:

* Med namnet **myPublicIPBastion**
* I resurs gruppen **CreateIntLBQS-RG**.
* På den **östra** platsen.
* **Statisk** Metod för allokering.
* **Standard** -SKU.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Skapa en Azure skydds-värd

Använd [New-AzBastion](/powershell/module/az.network/new-azbastion) för att skapa en skydds-värd:

* Med namnet **myBastion**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I virtuellt nätverk **myVNet**.
* Associerat med offentliga IP- **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Det kan ta några minuter innan Azure skydds-värden kan distribueras.


### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp
Skapa en nätverkssäkerhetsgrupp så att du kan definiera inkommande anslutningar till det virtuella nätverket.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Skapa en regel för nätverkssäkerhetsgruppen för port 80
Skapa en regel för nätverks säkerhets grupp med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Med namnet **myNSGRuleHTTP**.
* Beskrivning av **Tillåt http**.
* Åtkomst till **Tillåt**.
* Protokoll **(*)**.
* Riktningen **inkommande**.
* Prioritet **2000**.
* **Internet** källa.
* Käll port intervall för **(*)**.
* Mål adressens prefix för **(*)**.
* Mål **Port 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverks säkerhets grupp med [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Med namnet **myNSG**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I **östasiatiska** platser.
* Med säkerhets regler som skapats i föregående steg som lagras i en variabel.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Skapa Basic Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-pool för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  * En server dels-IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.
  * En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  * En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-frontend-ip"></a>Skapa klientdels-IP

Skapa en frontend-IP med [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Med namnet **frontend**.
* Privat IP-adress för **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Konfigurera en serverdelsadresspool

Skapa en backend-adresspool med [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Med namnet **myBackEndPool**.
* De virtuella datorerna ansluter till den här serverdelspoolen i de återstående stegen.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. 

En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Övervakar hälso tillståndet för de virtuella datorerna.
* Med namnet **myHealthProbe**.
* Protokoll- **TCP**.
* Övervaknings **Port 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i frontend **-poolen för klient delen.**
* Skickar belastningsutjämnad nätverks trafik till Server dels adresspoolen **myBackEndPool** med **port 80**. 
* Använda **myHealthProbe** för hälso avsökning.
* Protokoll- **TCP**.
* Tids gräns för inaktivitet på **15 minuter**.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>Skapa belastnings Utjämnings resurs

Skapa en offentlig belastningsutjämnare med [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Med namnet **myLoadBalancer**
* I **öster**.
* I resurs gruppen **CreateIntLBQS-RG**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Skapa nätverksgränssnitt

Skapa tre nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Med namnet **myNicVM1**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I **östasiatiska** platser.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Med namnet **myNicVM2**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I **östasiatiska** platser.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Skapa tillgänglighets uppsättning för virtuella datorer

Använd [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) för att skapa en tillgänglighets uppsättning för de virtuella datorerna:

* Med namnet **myAvSet**.
* I resurs gruppen **CreateIntLBQS-RG**.
* På den **östra** platsen.

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreateIntLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Ange ett administratörsanvändarnamn och lösenord för de virtuella datorerna med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Skapa de virtuella datorerna med:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Med namnet **myVM1**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM1**.
* Ansluten till belastningsutjämnarens **myLoadBalancer**.
* På den **östra** platsen.
* I **myAvSet** tillgänglighets uppsättning.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* Med namnet **myVM2**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM2**.
* Ansluten till belastningsutjämnarens **myLoadBalancer**.
* På den **östra** platsen.
* I **myAvSet** tillgänglighets uppsättning.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

Det tar några minuter att skapa och konfigurera de tre virtuella datorerna.

---

## <a name="install-iis"></a>Installera IIS

Använd [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension?view=latest) för att installera Anpassat skripttillägg. 

Tillägget kör PowerShell Add-WindowsFeature Web-Server för att installera IIS-webbservern och uppdaterar sedan Default.htm sidan för att Visa värd namnet för den virtuella datorn:

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

### <a name="create-network-interface"></a>Skapa nätverks gränssnitt

Skapa ett nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="mytestvm"></a>myTestVM

* Med namnet **myNicTestVM**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I **östasiatiska** platser.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

Ange ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Skapa den virtuella datorn med:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* Med namnet **myTestVM**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicTestVM**.
* På den **östra** platsen.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>Testa

1. [Logga in](https://portal.azure.com) i Azure-portalen.

1. Hitta den privata IP-adressen för belastningsutjämnaren på **översikts** skärmen. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer**.

2. Anteckna eller kopiera adressen bredvid **privat IP-adress** i **översikten** över **myLoadBalancer**.

3. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj **myTestVM** i resurs gruppen **CreateIntLBQS-RG** i resurs listan.

4. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

6. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

7. Öppna **Internet Explorer** på **myTestVM**.

8. Ange IP-adressen från föregående steg i adress fältet i webbläsaren. IIS-webbserverns standardsida visas i webbläsaren.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Skapa en intern standard belastnings utjämning" border="true":::
   
Om du vill se belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer kan du anpassa standard sidan för varje virtuell dators IIS-webbserver och sedan framtvinga en uppdatering av webbläsaren från klient datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen, belastningsutjämnaren och återstående resurser.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreateIntLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten

* Du har skapat en intern belastningsutjämnare för standard eller Basic
* Anslutna virtuella datorer. 
* Konfigurerat trafik regel för belastnings utjämning och hälso avsökning.
* Belastnings utjämning har testats.

Om du vill veta mer om Azure Load Balancer fortsätter du till...
> [!div class="nextstepaction"]
> [Vad är Azure Load Balancer?](load-balancer-overview.md)