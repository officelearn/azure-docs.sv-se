---
title: "Belastningsutjämning på flera IP-konfigurationer i Azure | Microsoft Docs"
description: "Belastningsutjämning mellan primära och sekundära IP-konfigurationer."
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 2235d007434dabde1639ab19bc6813c818ed5ed7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Belastningsutjämning på flera IP-konfigurationer med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Den här artikeln beskriver hur du använder Azure-belastningsutjämnaren med flera IP-adresser på sekundärt nätverksgränssnitt (NIC). I det här scenariot har vi två virtuella datorer som kör Windows med en primär och sekundär NIC. Varje sekundär nätverkskort har två IP-konfigurationer. Varje virtuell värd för både webbplatser contoso.com och fabrikam.com. Varje webbplats är bunden till en IP-konfigurationer på sekundära nätverkskortet. Vi kan använda Azure belastningsutjämnare för att visa två klientdelens IP-adresser, en för varje webbplats för att distribuera trafik till respektive IP-konfiguration för webbplatsen. Det här scenariot använder samma portnummer på både frontends som båda backend poolen IP-adresser.

![LB scenariot bild](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Steg för att belastningsutjämna på flera IP-konfigurationer

Följ stegen nedan för att uppnå det scenario som beskrivs i den här artikeln:

1. Installera Azure PowerShell. Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.
2. Skapa en resursgrupp med följande inställningar:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Mer information finns i steg 2 i [skapa en resursgrupp](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Skapa en Tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) som innehåller dina virtuella datorer. I det här scenariot använder du följande kommando:

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Följ anvisningarna steg 3 till 5 i [skapa en virtuell Windows-dator](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) artikel du förbereder skapandet av en virtuell dator med ett enda nätverkskort. Köra steg 6.1 och använder du följande i stället för steg 6.2:

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Slutför [skapa en virtuell Windows-dator](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) steg 6.3 via 6.8.

5. Lägga till en andra IP-konfiguration till var och en av de virtuella datorerna. Följ instruktionerna i [tilldela flera IP-adresser till virtuella datorer](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) artikel. Använd följande inställningar:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Du behöver du inte associera sekundär IP-konfigurationer med offentliga IP-adresser i den här kursen. Redigera kommandot för att ta bort den offentliga IP-association delen.

6. Slutför steg 4 till 6 i den här artikeln igen för VM2. Glöm inte att ersätta VM-namn till VM2 på det när du gör detta. Observera att du inte behöver skapa ett virtuellt nätverk för den andra virtuella datorn. Du kan eller inte kan skapa ett nytt undernät baserat på dina användningsfall.

7. Skapa två offentliga IP-adresser och lagra dem på lämplig variabler som visas:

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Skapa två klientdelens IP-konfigurationer:

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Skapa din serverdelsadresspooler, en avsökning och regler för belastningsutjämning:

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. När du har dessa resurser som skapas kan skapa din belastningsutjämnare:

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Lägg till andra backend pool och klientdelens IP-adresskonfigurationen din nyskapade belastningsutjämnare:

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Nedanstående kommandon hämta nätverkskorten och Lägg sedan till båda IP-konfigurationer för varje sekundär NIC serverdelsadresspool för belastningsutjämnaren:

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. Slutligen måste du konfigurera DNS-resursposter för att peka till respektive klientdelens IP-adressen för belastningsutjämnaren. Du kan vara värd för dina domäner i Azure DNS. Mer information om hur du använder Azure DNS med belastningsutjämnaren finns [med hjälp av Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du kombinerar belastningsutjämning för tjänster i Azure i [med belastningsutjämning tjänster i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar i Azure för att hantera och felsöka belastningsutjämnare i [logga analytics för Azure belastningsutjämnare](../load-balancer/load-balancer-monitor-log.md).
