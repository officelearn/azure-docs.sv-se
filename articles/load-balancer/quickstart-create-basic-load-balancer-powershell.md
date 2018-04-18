---
title: Skapa en offentlig belastningsutjämnare – PowerShell | Microsoft Docs
description: Lär dig hur du skapar en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4ce11b0b06e1feaf55d17e25500c43a7eb1bf3d5
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="get-started"></a>Skapa en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Mall](../load-balancer/load-balancer-get-started-internet-arm-template.md)



[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Distribuera lösningen med hjälp av Azure PowerShell

Följande procedurer beskriver hur du skapar en offentlig belastningsutjämnare i Azure Resource Manager med hjälp av PowerShell. Med Azure Resource Manager skapas och konfigureras varje resurs separat, och läggs sedan ihop för att skapa en belastningsutjämnare.

Du måste skapa och konfigurera följande objekt för att distribuera en belastningsutjämnare:

* IP-konfiguration på klientsidan: innehåller offentliga IP-adresser för inkommande nätverkstrafik.
* Backend-adresspool (serverdelspool): innehåller nätverksgränssnitten (NIC) som de virtuella datorerna använder för att ta emot nätverkstrafik från belastningsutjämnaren.
* Belastningsutjämningsregler: innehåller regler som mappar en offentlig port på belastningsutjämnaren till en port i backend-adresspoolen.
* NAT-regler för inkommande trafik: innehåller regler som mappar en offentlig port i belastningsutjämnaren till en port för en specifik virtuell dator i backend-adresspoolen.
* Avsökningar: innehåller hälsoavsökningar som används för att kontrollera tillgängligheten av instanser av virtuella datorer i backend-adresspoolen.

Mer information finns i [Azure Resource Manager-stöd för belastningsutjämnare](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Konfigurera PowerShell för användning med Resource Manager

Kontrollera att du har den senaste produktionsversionen av Azure Resource Manager-modulen för PowerShell:

1. Logga in i Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Ange dina autentiseringsuppgifter när du uppmanas att göra det.

2. Kontrollera prenumerationerna för kontot.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Välj vilka av dina Azure-prenumerationer som du vill använda.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Skapa en resursgrupp. (Hoppa över det här steget om du använder en befintlig resursgrupp.)

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Skapa ett virtuellt nätverk och en offentlig IP-adress för IP-adresspoolen på klientsidan

1. Skapa ett undernät och ett virtuellt nätverk.

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Skapa en offentlig IP-adressresurs i Azure med namnet **PublicIP**, som ska användas av en IP-pool på klientsidan med DNS-namnet **loadbalancernrp.westus.cloudapp.azure.com**. Följande kommando använder den statiska allokeringstypen.

    ```powershell
    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -DomainNameLabel loadbalancernrp
    ```

   > [!IMPORTANT]
   > Belastningsutjämnaren använder domänetiketten för den offentliga IP-adressen som ett prefix för dess fullständiga domännamn (FQDN). Detta skiljer sig från den klassiska distributionsmodellen, som använder molntjänsten som belastningsutjämnarens fullständiga domännamn.
   > I det här exemplet är det fullständiga domännamnet **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Skapa en IP-adresspool på klientsidan och en backend-adresspool

1. Skapa en IP-adresspool för klientsidan med namnet **LB-Frontend** som använder resursen **PublicIp**.

    ```powershell
    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
    ```

2. Skapa en backend-adresspool med namnet **LB-backend**.

    ```powershell
    $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend
    ```

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Skapa NAT-regler, en belastningsutjämningsregel, en avsökning och en belastningsutjämnare

I det här exemplet skapas följande objekt:

* En NAT-regel som översätter all inkommande trafik på port 3441 till port 3389
* En NAT-regel som översätter all inkommande trafik på port 3442 till port 3389
* En avsökningsregel som kontrollerar hälsostatusen på en sida med namnet **HealthProbe.aspx**
* En belastningsutjämningsregel som balanserar all inkommande trafik på port 80 till port 80 för adresserna i backend-poolen
* En belastningsutjämnare som använder alla dessa objekt

Följ dessa steg:

1. Skapa NAT-reglerna.

    ```powershell
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    ```

2. Skapa en hälsoavsökning. Du kan konfigurera en avsökning på två sätt:

    HTTP-avsökning

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    TCP-avsökning

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

3. Skapa en belastningsutjämningsregel.

    ```powershell
    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

4. Skapa belastningsutjämnaren genom att använda objekten som skapats tidigare.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

## <a name="create-nics"></a>Skapa nätverkskort

Skapa nätverksgränssnitt (eller ändra befintliga) och associera dem sedan med NAT-regler, belastningsutjämningsregler och avsökningar:

1. Hämta det virtuella nätverket och undernätet till det virtuella nätverket, där nätverkskorten ska skapas.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Skapa ett nätverkskort med namnet **lb-nic1-be** och associera det med den första NAT-regeln och den första (och enda) backend-adresspoolen.

    ```powershell
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
    ```

3. Skapa ett nätverkskort med namnet **lb-nic2-be** och associera det med den andra NAT-regeln och den första (och enda) backend-adresspoolen.

    ```powershell
    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
    ```

4. Kontrollera nätverkskorten.

        $backendnic1

    Förväntad utdata:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Använd cmdleten `Add-AzureRmVMNetworkInterface` för att tilldela nätverkskorten till olika virtuella datorer.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Vägledning om hur du skapar en virtuell dator och tilldelar ett nätverkskort finns i avsnittet om hur du [skapar en virtuell dator i Azure med hjälp av PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Lägga till nätverksgränssnittet till belastningsutjämnaren

1. Hämta belastningsutjämnaren från Azure.

    Läs in belastningsutjämningsresursen i en variabel (om du inte har gjort det redan). Variabeln heter **$lb**. Använd samma namn från belastningsutjämningsresursen som du skapade tidigare.

    ```powershell
    $lb= get-azurermloadbalancer -name NRP-LB -resourcegroupname NRP-RG
    ```

2. Läs in backend-konfigurationen i en variabel.

    ```powershell
    $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
    ```

3. Läs in nätverksgränssnittet som du redan skapat i en variabel. Variabelnamnet är **$nic**. Nätverksgränssnittets namn är samma som i det förra exemplet.

    ```powershell
    $nic =get-azurermnetworkinterface -name lb-nic1-be -resourcegroupname NRP-RG
    ```

4. Ändra backend-konfigurationen för nätverksgränssnittet.

    ```powershell
    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
    ```

5. Spara objektet för nätverksgränssnittet.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```

    När ett nätverksgränssnitt har lagts till i backend-poolen för belastningsutjämnaren börjar det ta emot nätverkstrafik baserat på belastningsutjämningsresursens belastningsutjämningsregler.

## <a name="update-an-existing-load-balancer"></a>Uppdatera en befintlig belastningsutjämnare

1. Använd belastningsutjämnaren från det föregående exemplet och tilldela ett belastningsutjämningsobjekt till variabeln **$slb** med hjälp av `Get-AzureLoadBalancer`.

    ```powershell
    $slb = get-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
    ```

2. I följande exempel lägger du till en NAT-regel för inkommande trafik i en befintlig belastningsutjämnare. Använd port 81 i poolen på klientsidan och port 8181 för backend-poolen.

    ```powershell
    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
    ```

3. Spara den nya konfigurationen med hjälp av `Set-AzureLoadBalancer`.

    ```powershell
    $slb | Set-AzureRmLoadBalancer
    ```

## <a name="remove-a-load-balancer"></a>Ta bort en belastningsutjämnare

Använd kommandot `Remove-AzureLoadBalancer` om du vill ta bort en tidigare skapad belastningsutjämnare med namnet **NRP LB** i resursgruppen **NRP-RG**.

```powershell
Remove-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Du kan använda den valfria växeln **-Force** (Framtvinga) om du inte vill uppmanas att bekräfta borttagningen.

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
