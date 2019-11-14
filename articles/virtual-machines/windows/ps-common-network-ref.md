---
title: Vanliga PowerShell-kommandon för virtuella Azure-nätverk
description: Vanliga PowerShell-kommandon för att komma igång med att skapa ett virtuellt nätverk och dess associerade resurser för virtuella datorer.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 3abde706ddff297094c7fbb1579b534894b349d2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032920"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Vanliga PowerShell-kommandon för virtuella Azure-nätverk

Om du vill skapa en virtuell dator måste du skapa ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) eller veta om ett befintligt virtuellt nätverk där den virtuella datorn kan läggas till. När du skapar en virtuell dator måste du vanligt vis överväga att skapa de resurser som beskrivs i den här artikeln.

Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

Vissa variabler kan vara användbara för dig om du kör fler än ett av kommandona i den här artikeln:

- $location-platsen för nätverks resurserna. Du kan använda [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) för att hitta en [geografisk region](https://azure.microsoft.com/regions/) som passar dig.
- $myResourceGroup-namnet på resurs gruppen där nätverks resurserna finns.

## <a name="create-network-resources"></a>Skapa nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa undernätskonfigurationer |$subnet 1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1"-AddressPrefix xx. X. X. X/XX<BR>$subnet 2 = New-AzVirtualNetworkSubnetConfig-Name "mySubnet2"-AddressPrefix XX. X. X. X/XX<BR><BR>Ett vanligt nätverk kan ha ett undernät för en [belastningsutjämnare mot Internet](../../load-balancer/load-balancer-internet-overview.md) och ett separat undernät för en [intern belastningsutjämnare](../../load-balancer/load-balancer-internal-overview.md). |
| Skapa ett virtuellt nätverk |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet"-ResourceGroupName $MyResourceGroup-location $location-AddressPrefix xx. X. X. X/XX-undernät $subnet 1, $subnet 2 |
| Testa ett unikt domän namn |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" – plats $location<BR><BR>Du kan ange ett DNS-domännamn för en [offentlig IP-resurs](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), vilket skapar en mappning för DomainName.location.cloudapp.Azure.com till den offentliga IP-adressen i de Azure-HANTERAde DNS-servrarna. Namnet får endast innehålla bokstäver, siffror och bindestreck. Det första och sista tecknet måste vara en bokstav eller en siffra och domän namnet måste vara unikt inom dess Azure-plats. Om **Sant** returneras är det föreslagna namnet globalt unikt. |
| Skapa en offentlig IP-adress |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>Den offentliga IP-adressen använder det domän namn som du tidigare har testat och används av belastningsutjämnarens konfiguration av klient delen. |
| Skapa en IP-konfiguration för klient delen |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP"-PublicIpAddress $pip<BR><BR>Klient dels konfigurationen omfattar den offentliga IP-adress som du skapade tidigare för inkommande nätverks trafik. |
| Skapa en serverdelsadresspool |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Tillhandahåller interna adresser för den belastningsutjämnare som nås via ett nätverks gränssnitt. |
| Skapa en avsökning |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "unprobe"-RequestPath ' healthProbe. aspx '-Protocol http-port 80-IntervalInSeconds 15-ProbeCount 2<BR><BR>Innehåller hälso avsökningar som används för att kontrol lera tillgängligheten för instanser av virtuella datorer i backend-adresspoolen. |
| Skapa en belastnings Utjämnings regel |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name http-FrontendIpConfiguration $FrontendIP-BackendAddressPool $BeAddressPool-PROBE $HealthProbe-Protocol TCP-FrontendPort 80-BackendPort 80<BR><BR>Innehåller regler som tilldelar en offentlig port på belastningsutjämnaren till en port i backend-adresspoolen. |
| Skapa en inkommande NAT-regel |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1"-FrontendIpConfiguration $FrontendIP-Protocol TCP-FrontendPort 3441-BackendPort 3389<BR><BR>Innehåller regler som mappar en offentlig port på belastningsutjämnaren till en port för en speciell virtuell dator i backend-adresspoolen. |
| Skapa en lastbalanserare |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-Name "myLoadBalancer"-location $location-FrontendIpConfiguration $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $BeAddressPool-PROBE $healthProbe |
| Skapa ett nätverks gränssnitt |$nic 1 = [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-Name "myNIC" – location $location-PrivateIpAddress xx. X. X. X-undernät $subnet 2-LoadBalancerBackendAddressPool $loadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $loadBalancer. InboundNatRules [0]<BR><BR>Skapa ett nätverks gränssnitt med den offentliga IP-adressen och det virtuella nätverks under nätet som du skapade tidigare. |

## <a name="get-information-about-network-resources"></a>Hämta information om nätverks resurser

| Aktivitet | Kommando |
| ---- | ------- |
| Lista virtuella nätverk |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över alla virtuella nätverk i resurs gruppen. |
| Hämta information om ett virtuellt nätverk |Get-AzVirtualNetwork-Name "myVNet"-ResourceGroupName $myResourceGroup |
| Lista undernät i ett virtuellt nätverk |Get-AzVirtualNetwork-Name "myVNet"-ResourceGroupName $myResourceGroup &#124; Välj undernät |
| Hämta information om ett undernät |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1"-VirtualNetwork $VNet<BR><BR>Hämtar information om under nätet i det angivna virtuella nätverket. $Vnet-värdet representerar det objekt som returneras av Get-AzVirtualNetwork. |
| Lista IP-adresser |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över offentliga IP-adresser i resurs gruppen. |
| Visa lista över belastningsutjämnare |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över alla belastnings utjämningar i resurs gruppen. |
| Visa lista över nätverks gränssnitt |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över alla nätverks gränssnitt i resurs gruppen. |
| Hämta information om ett nätverks gränssnitt |Get-AzNetworkInterface-Name "myNIC"-ResourceGroupName $myResourceGroup<BR><BR>Hämtar information om ett speciellt nätverks gränssnitt. |
| Hämta IP-konfigurationen för ett nätverks gränssnitt |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP"-NetworkInterface $NIC<BR><BR>Hämtar information om IP-konfigurationen för det angivna nätverks gränssnittet. $Nic-värdet representerar det objekt som returneras av Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Hantera nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Lägga till ett undernät i ett virtuellt nätverk |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix xx. X. X. X/XX-Name "mySubnet1"-VirtualNetwork $vnet<BR><BR>Lägger till ett undernät i ett befintligt virtuellt nätverk. $Vnet-värdet representerar det objekt som returneras av Get-AzVirtualNetwork. |
| Ta bort ett virtuellt nätverk |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet"-ResourceGroupName $myResourceGroup<BR><BR>Tar bort det angivna virtuella nätverket från resurs gruppen. |
| Ta bort ett nätverks gränssnitt |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC"-ResourceGroupName $myResourceGroup<BR><BR>Tar bort det angivna nätverks gränssnittet från resurs gruppen. |
| Ta bort en lastbalanserare |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Tar bort den angivna belastningsutjämnaren från resurs gruppen. |
| Ta bort en offentlig IP-adress |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Tar bort den angivna offentliga IP-adressen från resurs gruppen. |

## <a name="next-steps"></a>Nästa steg
* Använd det nätverks gränssnitt som du nyss skapade när du [skapar en virtuell dator](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Läs om hur du kan [skapa en virtuell dator med flera nätverks gränssnitt](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

