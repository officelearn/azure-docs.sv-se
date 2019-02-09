---
title: Vanliga PowerShell-kommandon för virtuella Azure-nätverk | Microsoft Docs
description: Vanliga PowerShell-kommandon för att komma igång med att skapa ett virtuellt nätverk och dess kopplade resurser för virtuella datorer.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 31a0d486f2540ea75a57b29b8f1da21839783468
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984653"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Vanliga PowerShell-kommandon för virtuella Azure-nätverk

Om du vill skapa en virtuell dator, måste du skapa en [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) eller veta om ett befintligt virtuellt nätverk där den virtuella datorn kan läggas till. Vanligtvis när du skapar en virtuell dator kan behöva du också fundera på att skapa de resurser som beskrivs i den här artikeln.

Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

Några variabler som kan vara användbar för dig om körs mer än något av kommandona i den här artikeln:

- $location - platsen för nätverksresurser. Du kan använda [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) att hitta en [geografisk region](https://azure.microsoft.com/regions/) som passar dig.
- $myResourceGroup - namnet på resursgruppen där nätverksresurserna finns.

## <a name="create-network-resources"></a>Skapa nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa undernätskonfigurationer |$subnet1 = [nya AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name ”mySubnet1” - AddressPrefix XX. X.X.X/XX<BR>$subnet2 = ny AzVirtualNetworkSubnetConfig-Name ”mySubnet2” - AddressPrefix XX. X.X.X/XX<BR><BR>Ett typiskt nätverk kan ha ett undernät för ett [internetuppkopplad belastningsutjämnare](../../load-balancer/load-balancer-internet-overview.md) och ett separat undernät för ett [intern belastningsutjämnare](../../load-balancer/load-balancer-internal-overview.md). |
| Skapa ett virtuellt nätverk |$vnet = [New AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name ”myVNet” - ResourceGroupName $myResourceGroup-plats $location - AddressPrefix XX. X.X.X/XX-undernätet $subnet1, $subnet2 |
| Testning av ett unikt domännamn |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Du kan ange ett DNS-domännamn för en [offentliga IP-adressresurs](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), vilket skapar en mappning för domainname.location.cloudapp.azure.com till den offentliga IP-adressen i Azure-hanterade DNS-servrar. Namnet får endast innehålla bokstäver, siffror och bindestreck. Det första och sista tecknet måste vara en bokstav eller antal och domännamnet måste vara unika inom dess Azure-plats. Om **SANT** returneras, ditt föreslagna namn är globalt unikt. |
| Skapa en offentlig IP-adress |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>Den offentliga IP-adressen använder det domännamn som du tidigare har testat och används av frontend-konfigurationen för belastningsutjämnaren. |
| Skapa en frontend-IP-konfiguration |$frontendIP = [New AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name ”myFrontendIP” - PublicIpAddress $pip<BR><BR>Frontend-konfigurationen innehåller offentliga IP-adress som du skapade tidigare för inkommande nätverkstrafik. |
| Skapa en serverdelsadresspool |$beAddressPool = [New AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name ”myBackendAddressPool”<BR><BR>Innehåller interna adresser för serverdelen för belastningsutjämnaren som kan nås via ett nätverksgränssnitt. |
| Skapa en avsökning |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Innehåller hälsoavsökningar som används för att kontrollera tillgängligheten för virtuella datorer i serverdelsadresspoolen. |
| Skapa en regel för belastningsutjämning |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Innehåller regler som tilldelar en offentlig port på belastningsutjämnaren till en port i serverdelsadresspoolen. |
| Skapa en inkommande NAT-regel |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Innehåller regler som mappar en offentlig port på belastningsutjämnaren till en port för en specifik virtuell dator i serverdelsadresspoolen. |
| Skapa en lastbalanserare |$loadBalancer = [New AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) - ResourceGroupName $myResourceGroup-Name ”myLoadBalancer”-plats $location - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-avsökning $healthProbe |
| Skapa ett nätverksgränssnitt |$nic1 = [New AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) - ResourceGroupName $myResourceGroup-Name ”myNIC” - plats $location - PrivateIpAddress XX. X.X.X-undernätet $subnet2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Skapa ett nätverksgränssnitt med offentliga IP-adressen och undernät för virtuellt nätverk som du skapade tidigare. |

## <a name="get-information-about-network-resources"></a>Få information om nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Lista över virtuella nätverk |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över alla virtuella nätverk i resursgruppen. |
| Få information om ett virtuellt nätverk |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Lista undernät i ett virtuellt nätverk |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Få information om ett undernät |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Hämtar information om undernätet i det angivna virtuella nätverket. Värdet för $vnet representerar objektet som returnerades av Get-AzVirtualNetwork. |
| Lista över IP-adresser |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) - ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över offentliga IP-adresser i resursgruppen. |
| Lista över belastningsutjämnare |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) - ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över alla belastningsutjämnare i resursgruppen. |
| Lista nätverksgränssnitt |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) - ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över alla nätverksgränssnitt i resursgruppen. |
| Få information om ett nätverksgränssnitt |Get-AzNetworkInterface-Name ”myNIC” - ResourceGroupName $myResourceGroup<BR><BR>Hämtar information om ett visst nätverksgränssnitt. |
| Hämta IP-konfiguration för ett nätverksgränssnitt |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name ”myNICIP” - NetworkInterface $nic<BR><BR>Hämtar information om IP-adresskonfigurationen för det angivna nätverksgränssnittet. Värdet för $nic representerar objektet som returnerades av Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Hantera nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Lägg till ett undernät till ett virtuellt nätverk |[Lägg till AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) - AddressPrefix XX. X.X.X/XX-Name ”mySubnet1” - VirtualNetwork $vnet<BR><BR>Lägger till ett undernät i ett befintligt virtuellt nätverk. Värdet för $vnet representerar objektet som returnerades av Get-AzVirtualNetwork. |
| Ta bort ett virtuellt nätverk |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Tar bort det angivna virtuella nätverket från resursgruppen. |
| Ta bort ett nätverksgränssnitt |[Ta bort AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name ”myNIC” - ResourceGroupName $myResourceGroup<BR><BR>Tar bort det angivna nätverksgränssnittet från resursgruppen. |
| Ta bort en lastbalanserare |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Tar bort den angivna belastningsutjämnaren från resursgruppen. |
| Ta bort en offentlig IP-adress |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Tar bort den angivna offentliga IP-adressen från resursgruppen. |

## <a name="next-steps"></a>Nästa steg
* Använda nätverksgränssnittet som du nyss skapade när du [skapa en virtuell dator](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Lär dig mer om hur du kan [skapa en virtuell dator med flera nätverksgränssnitt](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

