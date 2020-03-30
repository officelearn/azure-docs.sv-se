---
title: Belastningsutjämning på flera IP-konfigurationer - Azure CLI
titleSuffix: Azure Load Balancer
description: I den här artikeln kan du läsa mer om belastningsutjämning över primära och sekundära IP-konfigurationer med Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 6ac9e362314cc45e6adbdcf1390f70cbe6b05de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075967"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Belastningsutjämning på flera IP-konfigurationer med PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [Powershell](load-balancer-multiple-ip-powershell.md)


I den här artikeln beskrivs hur du använder Azure Load Balancer med flera IP-adresser i ett sekundärt nätverksgränssnitt (NIC). I det här scenariot har vi två virtuella datorer som kör Windows, var och en med ett primärt och ett sekundärt nätverkskort. Var och en av de sekundära nätverkskorten har två IP-konfigurationer. Varje virtuell dator är värd för både webbplatser contoso.com och fabrikam.com. Varje webbplats är bunden till en av IP-konfigurationerna på det sekundära nätverkskortet. Vi använder Azure Load Balancer för att exponera två IP-adresser för frontend, en för varje webbplats, för att distribuera trafik till respektive IP-konfiguration för webbplatsen. Det här scenariot använder samma portnummer för båda klientdelarna, samt båda serverdelspoolENs IP-adresser.

![BILD AV LB-scenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Steg för belastningsfördelning på flera IP-konfigurationer

Följ stegen nedan för att uppnå det scenario som beskrivs i den här artikeln:

1. Installera Azure PowerShell. Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.
2. Skapa en resursgrupp med följande inställningar:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Mer information finns i steg 2 [i Skapa en resursgrupp](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Skapa en tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) som ska innehålla dina virtuella datorer. I det här scenariot använder du följande kommando:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Följ instruktionerna steg 3 till 5 i [Skapa en Windows VM-artikel](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) för att förbereda skapandet av en virtuell dator med ett enda nätverkskort. Kör steg 6.1 och använd följande i stället för steg 6.2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Slutför sedan [Skapa steg för Windows VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) 6.3 till 6.8.

5. Lägg till en andra IP-konfiguration till var och en av de virtuella datorerna. Följ instruktionerna i [Tilldela flera IP-adresser till virtuella datorer](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) artikeln. Använd följande konfigurationsinställningar:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Du behöver inte associera de sekundära IP-konfigurationerna med offentliga IP-adresser i den här självstudien. Redigera kommandot för att ta bort den offentliga IP-associationsdelen.

6. Fyll i steg 4 till och med 6 i den här artikeln igen för VM2. Var noga med att ersätta VM-namnet till VM2 när du gör detta. Observera att du inte behöver skapa ett virtuellt nätverk för den andra virtuella datorn. Du kanske eller kanske inte skapar ett nytt undernät baserat på ditt användningsfall.

7. Skapa två offentliga IP-adresser och lagra dem i lämpliga variabler som visas:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Skapa två IP-konfigurationer för klientdel:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Skapa serverda adresspooler, en avsökning och dina belastningsutjämningsregler:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. När du har skapat dessa resurser skapar du belastningsutjämnaren:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Lägg till den andra serverdelsadresspoolen och IP-konfigurationen för klientdel i den nyskapade belastningsutjämnaren:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Kommandona nedan hämtar nätverkskorten och lägger sedan till båda IP-konfigurationerna för varje sekundärt nätverkskort i lastutjämningshanterarens serverdadress.

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Slutligen måste du konfigurera DNS-resursposter så att de pekar på respektive klientdels-IP-adress för belastningsutjämnaren. Du kan vara värd för dina domäner i Azure DNS. Mer information om hur du använder Azure DNS med belastningsutjämnare finns i [Använda Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om hur du kombinerar belastningsutjämningstjänster i Azure [med hjälp av belastningsutjämningstjänster i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar i Azure för att hantera och felsöka belastningsutjämnare i [Azure Monitor-loggar för Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
