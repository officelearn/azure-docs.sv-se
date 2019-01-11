---
title: Belastningsutjämning på flera IP-konfigurationer – Azure CLI
titlesuffix: Azure Load Balancer
description: Belastningsutjämning mellan primära och sekundära IP-konfigurationer.
services: load-balancer
documentationcenter: na
author: anavinahar
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 18d5bbd6b4b4c48fee2e01c70dfe696a0c10834a
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201019"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Belastningsutjämning på flera IP-konfigurationer med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Den här artikeln beskriver hur du använder Azure Load Balancer med flera IP-adresser på ett sekundärt nätverksgränssnitt (NIC). För det här scenariot har vi två virtuella datorer som kör Windows med en primär och en sekundärt nätverkskort. Var och en av de sekundära nätverkskort har två IP-konfigurationer. Varje virtuell dator är värd för både webbplatser contoso.com och fabrikam.com. Varje webbplats är bunden till en av IP-konfigurationer på sekundärt nätverkskort. Vi kan använda Azure Load Balancer för att visa två frontend IP-adresser, en för varje webbplats, för att distribuera trafik till respektive IP-konfigurationen för webbplatsen. Det här scenariot använder samma portnummer i såväl klienter, som båda backend-pool IP-adresser.

![LB scenariot bild](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Steg för att belastningsutjämna på flera IP-konfigurationer

Följ stegen nedan för att uppnå det scenario som beskrivs i den här artikeln:

1. Installera Azure PowerShell. Se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.
2. Skapa en resursgrupp med hjälp av följande inställningar:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Mer information finns i steg 2 i [skapa en resursgrupp](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Skapa en Tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) som innehåller dina virtuella datorer. Det här scenariot använder du följande kommando:

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Följ anvisningarna steg 3 till 5 i [skapa en virtuell Windows-dator](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) artikeln du förbereder skapandet av en virtuell dator med ett enda nätverkskort. Köra steget 6.1 och använder du följande i stället för steg 6.2:

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Slutför [skapa en virtuell Windows-dator](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) steg 6.3 via 6.8.

5. Lägga till en sekundär IP-konfiguration till var och en av de virtuella datorerna. Följ instruktionerna i [tilldela flera IP-adresser till virtuella datorer](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) artikeln. Använd följande inställningar:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Du behöver inte att associera de sekundära IP-konfigurationerna med offentliga IP-adresser i den här kursen. Redigera kommandot för att ta bort den offentliga IP-association delen.

6. Slutför steg 4 till 6 i den här artikeln igen för VM2. Var noga med att ersätta VM-namn till VM2 på det när du gör detta. Observera att du inte behöver skapa ett virtuellt nätverk för den andra virtuella datorn. Du kanske eller kanske inte att skapa ett nytt undernät baserat på ditt användningsområde.

7. Skapa två offentliga IP-adresser och lagra dem i rätt variablerna som visas:

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

10. När du har dessa resurser som har skapats kan du skapa belastningsutjämnaren:

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Lägg till andra backend-pool och frontend IP-adresskonfigurationen i din nyligen skapade belastningsutjämnare:

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Kommandona nedan få nätverkskorten och Lägg sedan till båda IP-konfigurationer för varje sekundär nätverkskort till serverdelsadresspoolen för belastningsutjämnaren:

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

13. Slutligen måste du konfigurera DNS-resursposter så att den pekar till respektive frontend IP-adressen för belastningsutjämnaren. Du kan vara värd för dina domäner i Azure DNS. Läs mer om hur du använder Azure DNS med belastningsutjämnare, [med hjälp av Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du kombinerar belastningsutjämning i Azure i [med tjänster för belastningsutjämning i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar i Azure för att hantera och felsöka belastningsutjämnare i [logganalys för Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
