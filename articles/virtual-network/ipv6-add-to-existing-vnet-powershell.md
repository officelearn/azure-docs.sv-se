---
title: Uppgradera ett IPv4-program till IPv6 i Azure Virtual Network - PowerShell
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar IPv6-adresser till ett befintligt program i azure virtuellt nätverk med Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: c733538a4e730a95008a8ec1e4d50c20d6ce24ec
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420768"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Uppgradera ett IPv4-program till IPv6 i virtuellt Azure-nätverk – PowerShell

Den här artikeln visar hur du lägger till IPv6-anslutning till ett befintligt IPv4-program i ett virtuellt Azure-nätverk med en standardbelastningsutjämnare och offentlig IP. Uppgraderingen på plats omfattar:
- IPv6-adressutrymme för det virtuella nätverket och undernätet
- en standardbelastningsutjämnare med både IPv4- och IPV6-frontendkonfigurationer
- Virtuella datorer med nätverkskort som har både en IPv4 + IPv6-konfiguration
- IPv6 Public IP så att belastningsutjämnaren har Internet-vända IPv6-anslutning



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell-modul version 6.9.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har distribuerat en standardbelastningsutjämning enligt beskrivningen i [Snabbstart: Skapa en standardbelastningsutjämnare - Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Hämta resursgruppen

Innan du kan skapa det virtuella nätverket med dubbla staplar måste du hämta resursgruppen med [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Skapa en IPv6 IP-adresser

Skapa en offentlig IPv6-adress med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för standardbelastningsbalanseraren. I följande exempel skapas en offentlig IP-adress iPv6 med namnet *PublicIP_v6* i resursgruppen *myResourceGroupSLB:*

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Konfigurera frontend för belastningsutjämnare

Hämta den befintliga belastningsutjämnarkonfigurationen och lägg sedan till den nya IP-adressen för IPv6 med [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) enligt följande:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Konfigurera serverdpool för belastningsutjämnare

Skapa serverdelspoolen på den lokala kopian av belastningsutjämnarkonfigurationen och uppdatera den löpbelastningsutjämnaren med den nya serverdelspoolkonfigurationen enligt följande:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Konfigurera regler för lastbalanserare
Hämta den befintliga konfigurationen av frontend-frontend- och serverdelspoolen och lägg sedan till nya belastningsutjämningsregler med [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Lägga till IPv6-adressintervall

Lägg till IPv6-adressintervall i det virtuella nätverket och undernätet som är värd för de virtuella datorerna enligt följande:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>Lägga till IPv6-konfiguration i NÄTVERKSKORTET

Konfigurera alla nätverkskort för virtuella datorer med en IPv6-adress med [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) enligt följande:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa virtuellt IPv6-nätverk med dubbla stackar i Azure-portalen
Du kan visa det virtuella nätverket IPv6 med dubbla stackar i Azure-portalen på följande sätt:
1. I portalens sökfält anger du *myVnet*.
2. När **myVnet** visas i sökresultaten markerar du det. Detta startar **översiktssidan för** det virtuella nätverket med dubbla stacker som heter *myVNet*. Det virtuella nätverket med dubbla staplar visar de tre nätverkskort med både IPv4- och IPv6-konfigurationer i det dubbla stackundernätet med namnet *mySubnet*.

  ![IPv6 virtuellt nätverk med dubbla staplar i Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du uppdaterat en befintlig standardbelastningsutjämning med en IP-konfiguration för IPv4-klientdel till en konfiguration med dubbla stackar (IPv4 och IPv6). Du har också lagt till IPv6-konfigurationer i nätverkskorten för de virtuella datorerna i serverdapoolen och i det virtuella nätverket som är värd för dem. Mer information om IPv6-stöd i virtuella Azure-nätverk finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)
