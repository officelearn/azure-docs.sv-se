---
title: Skapa en privat Azure-länktjänst med Azure PowerShell| Microsoft-dokument
description: Lär dig hur du skapar en privat Azure-länktjänst med Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932088"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Skapa en privat länktjänst med Azure PowerShell
Den här artikeln visar hur du skapar en privat länktjänst i Azure med Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln den senaste Azure PowerShell-modulversionen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa en privat länk måste du skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *WestCentralUS-platsen:*

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk för din privata länk med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myvnet* med undernät för*frontendSubnet*), serverdel *(backendSubnet*), privat länk *(otherSubnet):*

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>Skapa intern belastningsutjämnare
Skapa en intern standardbelastningsutdelning med [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). I följande exempel skapas en intern standardbelastningsutjämning med hjälp av frontend IP-konfiguration, avsökning, regel och serverdelspool som du skapade i föregående steg:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Skapa en privat länktjänst
Skapa en privat länktjänst med [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  I det här exemplet skapas en privat länktjänst med namnet *myPLS* med standardbelastningsutjämning i resursgruppen *myResourceGroup*. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Hämta privat länktjänst
Få information om din privata länktjänst med [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) enligt följande:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

I det här skedet har din private link-tjänst skapats och är redo att ta emot trafiken. Observera att ovanstående exempel är bara att demonstrera skapa Private Link Service med PowerShell.  Vi har inte konfigurerat serverdpoolerna för belastningsutjämnarhanteraren eller något program på serverdapoolerna för att lyssna på trafiken. Om du vill se trafikflöden från till rekommenderar du starkt att konfigurera programmet bakom standardbelastningsutjämnaren. 

Därefter kommer vi att visa hur man kartlägger den här tjänsten till en privat slutpunkt i olika virtuella nätverk med PowerShell. Återigen är exemplet begränsat till att skapa den privata slutpunkten och ansluta till Private Link Service som skapats ovan. Du kan skapa virtuella datorer i det virtuella nätverket för att skicka/ta emot trafik till den privata slutpunkten för att skapa ditt scenario. 

## <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt
### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk för din privata slutpunkt med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I det här exemplet skapas ett virtuellt nätverk med namnet *vnetPE* i resursgruppen *myResourceGroup:*
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt
Skapa en privat slutpunkt för att använda privat länktjänst som skapats ovan i det virtuella nätverket:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Hämta privat slutpunkt
Hämta IP-adressen för den `Get-AzPrivateEndpoint` privata slutpunkten med följande:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Godkänna den privata slutpunktsanslutningen
Godkänn den privata slutpunktsanslutningen till den privata länktjänsten med "Godkänn-AzPrivateEndpointConnection".

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [privat Azure-länk](private-link-overview.md)
 
