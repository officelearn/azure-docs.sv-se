---
title: Vanliga PowerShell-kommandon för Azure Virtual Networks | Microsoft Docs
description: Vanliga PowerShell-kommandon för att komma igång med att skapa ett virtuellt nätverk och dess associerade resurser för virtuella datorer.
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
ms.openlocfilehash: a5b3f84c27a0a5f6458808940b16a9001097b30b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Vanliga PowerShell-kommandon för virtuella Azure-nätverk

Om du vill skapa en virtuell dator, måste du skapa en [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) eller veta om ett befintligt virtuellt nätverk där den virtuella datorn kan läggas till. Vanligtvis när du skapar en virtuell dator kan behöva du också överväga att skapa resurser som beskrivs i den här artikeln.

Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

Några variabler som kan vara praktiskt om körs mer än något av kommandona i den här artikeln:

- $location - platsen för nätverksresurser. Du kan använda [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) att hitta en [geografisk region](https://azure.microsoft.com/regions/) som passar dig.
- $myResourceGroup - namnet på resursgruppen där nätverksresurserna finns.

## <a name="create-network-resources"></a>Skapa nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Skapa undernät |$Undernät1 = [nya AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) -Name ”mySubnet1” - AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>En typisk nätverk kan ha ett undernät för ett [internetuppkopplad belastningsutjämnaren](../../load-balancer/load-balancer-internet-overview.md) och ett separat undernät för ett [intern belastningsutjämnare](../../load-balancer/load-balancer-internal-overview.md). |
| Skapa ett virtuellt nätverk |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) -Name ”myVNet” - ResourceGroupName $myResourceGroup-plats $location - AddressPrefix XX. X.X.X/XX-undernätet $Undernät1, $Undernät2 |
| Testa ett unikt domännamn |[Testa AzureRmDnsAvailability](https://docs.microsoft.com/powershell/module/azurerm.network/test-azurermdnsavailability) - DomainNameLabel ”myDNS”-plats $location<BR><BR>Du kan ange ett DNS-domännamn för en [offentliga IP-resurs](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), vilket skapar en mappning för domainname.location.cloudapp.azure.com till den offentliga IP-adressen i Azure-hanterade DNS-servrar. Namnet kan innehålla endast bokstäver, siffror och bindestreck. Det första och sista tecknet måste vara en bokstav eller siffra och domännamnet måste vara unikt i Azure-plats. Om **SANT** returneras ditt föreslagna namn är globalt unika. |
| Skapa en offentlig IP-adress |$pip = [ny AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress) -Name ”myPublicIp” - ResourceGroupName $myResourceGroup - DomainNameLabel ”myDNS”-plats $location - AllocationMethod dynamiska<BR><BR>Den offentliga IP-adressen används det domännamn som du tidigare har testat och används av klientdel konfigurationen av belastningsutjämnaren. |
| Skapa en IP-konfiguration för klientdel |$frontendIP = [ny AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) -Name ”myFrontendIP” - PublicIpAddress $pip<BR><BR>Frontend-konfigurationen innehåller den offentliga IP-adressen som du skapade tidigare för inkommande trafik. |
| Skapa en serverdelsadresspool |$beAddressPool = [ny AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) -Name ”myBackendAddressPool”<BR><BR>Innehåller interna adresser för serverdelen av belastningsutjämnaren som kan nås via ett nätverksgränssnitt. |
| Skapa en avsökning |$healthProbe = [ny AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) -Name ”myProbe” - RequestPath 'HealthProbe.aspx'-protokollet http-Port 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Innehåller hälsoavsökningar som används för att kontrollera tillgängligheten för virtuella datorer instanser i serverdelen för adresspoolen. |
| Skapa en regel för belastningsutjämning |$lbRule = [ny AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) -namnet HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-avsökning $healthProbe-protokollet Tcp - FrontendPort 80 - BackendPort 80<BR><BR>Innehåller regler som tilldelar en offentlig port på belastningsutjämnaren till en port i serverdelen för adresspoolen. |
| Skapa en inkommande NAT-regel |$inboundNATRule = [ny AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) -Name ”myInboundRule1” - FrontendIpConfiguration $frontendIP-protokollet TCP - FrontendPort 3441 - BackendPort 3389<BR><BR>Innehåller regler för mappning av en offentlig port på belastningsutjämnaren till en port för en specifik virtuell dator i backend-adresspool. |
| Skapa en belastningsutjämnare |$loadBalancer = [ny AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancer) - ResourceGroupName $myResourceGroup-Name ”myLoadBalancer”-plats $location - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-avsökning $healthProbe |
| Skapa ett nätverksgränssnitt |$nic1 = [ny AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface) - ResourceGroupName $myResourceGroup-Name ”myNIC” - plats $location - PrivateIpAddress XX. X.X.X-undernätet $Undernät2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Skapa ett nätverksgränssnitt med hjälp av offentliga IP-adressen och undernät för virtuellt nätverk som du skapade tidigare. |

## <a name="get-information-about-network-resources"></a>Hämta information om nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Lista över virtuella nätverk |[Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) - ResourceGroupName $myResourceGroup<BR><BR>Visar en lista över alla virtuella nätverk i resursgruppen. |
| Hämta information om ett virtuellt nätverk |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Lista över undernät i ett virtuellt nätverk |Get-AzureRmVirtualNetwork-Name ”myVNet” - ResourceGroupName $myResourceGroup &#124; Välj undernät |
| Hämta information om ett undernät |[Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) -Name ”mySubnet1” - VirtualNetwork $vnet<BR><BR>Hämtar information om undernätet i det angivna virtuella nätverket. $Vnet värdet representerar objektet som returneras av Get-AzureRmVirtualNetwork. |
| Lista över IP-adresser |[Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) - ResourceGroupName $myResourceGroup<BR><BR>Visar de offentliga IP-adresserna i resursgruppen. |
| Lista över belastningsutjämnare |[Get-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermloadbalancer) - ResourceGroupName $myResourceGroup<BR><BR>Listar alla belastningsutjämnare i resursgruppen. |
| Lista över nätverksgränssnitt |[Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterface) - ResourceGroupName $myResourceGroup<BR><BR>Listar alla nätverksgränssnitt i resursgruppen. |
| Hämta information om ett nätverksgränssnitt |Get-AzureRmNetworkInterface-Name ”myNIC” - ResourceGroupName $myResourceGroup<BR><BR>Hämtar information om ett visst nätverksgränssnitt. |
| Hämta IP-konfigurationen för ett nätverksgränssnitt |[Get-AzureRmNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Hämtar information om IP-adresskonfigurationen för det angivna nätverksgränssnittet. $Nic värdet representerar objektet som returneras av Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Hantera nätverksresurser

| Aktivitet | Kommando |
| ---- | ------- |
| Lägg till ett undernät till ett virtuellt nätverk |[Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Lägger till ett undernät till ett befintligt virtuellt nätverk. $Vnet värdet representerar objektet som returneras av Get-AzureRmVirtualNetwork. |
| Ta bort ett virtuellt nätverk |[Ta bort-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetwork) -Name ”myVNet” - ResourceGroupName $myResourceGroup<BR><BR>Tar bort det angivna virtuella nätverket från resursgruppen. |
| Ta bort ett nätverksgränssnitt |[Ta bort AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermnetworkinterface) -Name ”myNIC” - ResourceGroupName $myResourceGroup<BR><BR>Tar bort det angivna nätverksgränssnittet från resursgruppen. |
| Ta bort en belastningsutjämnare |[Ta bort AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermloadbalancer) -Name ”myLoadBalancer” - ResourceGroupName $myResourceGroup<BR><BR>Tar bort den angivna belastningsutjämnaren från resursgruppen. |
| Ta bort en offentlig IP-adress |[Ta bort AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermpublicipaddress)-Name ”myIPAddress” - ResourceGroupName $myResourceGroup<BR><BR>Tar bort angiven offentlig IP-adress från resursgruppen. |

## <a name="next-steps"></a>Nästa steg
* Använd nätverksgränssnittet som du nyss skapade när du [skapa en virtuell dator](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Lär dig mer om hur du kan [skapa en virtuell dator med flera nätverksgränssnitt](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

