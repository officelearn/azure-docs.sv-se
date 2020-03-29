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
ms.openlocfilehash: b9debe04759772ef51946dc99943ec4eff6f61dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911839"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Vanliga PowerShell-kommandon för virtuella Azure-nätverk

Om du vill skapa en virtuell dator måste du skapa ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) eller känna till ett befintligt virtuellt nätverk där den virtuella datorn kan läggas till. När du skapar en virtuell dator måste du vanligtvis också överväga att skapa de resurser som beskrivs i den här artikeln.

Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

Vissa variabler kan vara användbara för dig om du kör mer än ett av kommandona i den här artikeln:

- $location - Platsen för nätverksresurserna. Du kan använda [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) för att hitta en [geografisk region](https://azure.microsoft.com/regions/) som fungerar för dig.
- $myResourceGroup - Namnet på resursgruppen där nätverksresurserna finns.

## <a name="create-network-resources"></a>Skapa nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa undernätskonfigurationer |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Namn "mySubnet1" -AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Namn "mySubnet2" -AddressPrefix XX. X.X.X/XX<BR><BR>Ett typiskt nätverk kan ha ett undernät för en [internetinriktad belastningsutjämnare](../../load-balancer/load-balancer-internet-overview.md) och ett separat undernät för en [intern belastningsutjämnare](../../load-balancer/load-balancer-internal-overview.md). |
| Skapa ett virtuellt nätverk |$vnet = [Ny-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Namn "myVNet" -ResourceGroupName $myResourceGroup -Plats $location -AddressPrefix XX. X.X.X/XX -Undernät $subnet1, $subnet2 |
| Testa för ett unikt domännamn |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Plats $location<BR><BR>Du kan ange ett DNS-domännamn för en [offentlig IP-resurs](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), som skapar en mappning för domainname.location.cloudapp.azure.com till den offentliga IP-adressen i Azure-hanterade DNS-servrar. Namnet får endast innehålla bokstäver, siffror och bindestreck. Det första och sista tecknet måste vara en bokstav eller ett nummer och domännamnet måste vara unikt inom sin Azure-plats. Om **True** returneras är ditt föreslagna namn globalt unikt. |
| Skapa en offentlig IP-adress |$pip = [Ny-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Namn "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Plats $location -AllocationMethod Dynamic<BR><BR>Den offentliga IP-adressen använder det domännamn som du tidigare testat och som används av belastningsutjämnarens klientkonfiguration. |
| Skapa en IP-konfiguration för klientdel |$frontendIP = [Ny-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Namn "myFrontendIP" -PublicIpAddress $pip<BR><BR>Frontend-konfigurationen innehåller den offentliga IP-adress som du tidigare skapade för inkommande nätverkstrafik. |
| Skapa en serverdelsadresspool |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Namn "myBackendAddressPool"<BR><BR>Tillhandahåller interna adresser för serverda för belastningsutjämnaren som används via ett nätverksgränssnitt. |
| Skapa en avsökning |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Namn "myProbe" -RequestPath 'HealthProbe.aspx' -Protokoll http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Innehåller hälsoavsökningar som används för att kontrollera tillgängligheten för instanser av virtuella datorer i serverdaadresspoolen. |
| Skapa en belastningsutjämningsregel |$lbRule = [Ny-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Namn HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Innehåller regler som tilldelar en offentlig port på belastningsutjämnaren till en port i backend-adresspoolen. |
| Skapa en inkommande NAT-regel |$inboundNATRule = [Ny-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Namn "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Innehåller regler som mappar en offentlig port på belastningsutjämnaren till en port för en viss virtuell dator i serverdaadresspoolen. |
| Skapa en lastbalanserare |$loadBalancer = [Ny-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Namn "myLoadBalancer" -Plats $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Skapa ett nätverksgränssnitt |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX. X.X.X -Undernät $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Skapa ett nätverksgränssnitt med den offentliga IP-adressen och det virtuella nätverksundernätet som du tidigare skapade. |

## <a name="get-information-about-network-resources"></a>Hämta information om nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Lista virtuella nätverk |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över alla virtuella nätverk i resursgruppen. |
| Få information om ett virtuellt nätverk |Get-AzVirtualNetwork -Namn "myVNet" -ResourceGroupName $myResourceGroup |
| Lista undernät i ett virtuellt nätverk |Get-AzVirtualNetwork -Namn "myVNet" -ResourceGroupName $myResourceGroup &#124; Välj undernät |
| Få information om ett undernät |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Namn "mySubnet1" -VirtualNetwork $vnet<BR><BR>Hämtar information om undernätet i det angivna virtuella nätverket. Värdet $vnet representerar objektet som returneras av Get-AzVirtualNetwork. |
| Lista IP-adresser |[Hämta-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över de offentliga IP-adresserna i resursgruppen. |
| Lista belastningsutjämnare |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Visar alla belastningsutjämnare i resursgruppen. |
| Lista nätverksgränssnitt |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Visar alla nätverksgränssnitt i resursgruppen. |
| Hämta information om ett nätverksgränssnitt |Get-AzNetworkInterface -Namn "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Hämtar information om ett visst nätverksgränssnitt. |
| Hämta IP-konfigurationen för ett nätverksgränssnitt |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Namn "myNICIP" -NetworkInterface $nic<BR><BR>Hämtar information om IP-konfigurationen för det angivna nätverksgränssnittet. Värdet $nic representerar objektet som returneras av Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Hantera nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Lägga till ett undernät i ett virtuellt nätverk |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X.X.X/XX -Namn "mySubnet1" -VirtualNetwork $vnet<BR><BR>Lägger till ett undernät i ett befintligt virtuellt nätverk. Värdet $vnet representerar objektet som returneras av Get-AzVirtualNetwork. |
| Ta bort ett virtuellt nätverk |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Namn "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Tar bort det angivna virtuella nätverket från resursgruppen. |
| Ta bort ett nätverksgränssnitt |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Tar bort det angivna nätverksgränssnittet från resursgruppen. |
| Ta bort en lastbalanserare |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Namn "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Tar bort den angivna belastningsutjämnaren från resursgruppen. |
| Ta bort en offentlig IP-adress |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Namn "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Tar bort den angivna offentliga IP-adressen från resursgruppen. |

## <a name="next-steps"></a>Efterföljande moment
Använd det nätverksgränssnitt som du just skapade när du [skapar en virtuell dator](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


