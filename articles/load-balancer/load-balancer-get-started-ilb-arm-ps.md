---
title: Skapa en Azure intern belastningsutjämnare med hjälp av PowerShell
titlesuffix: Azure Load Balancer
description: Ta reda på hur du skapar en intern lastbalanserare med hjälp av Azure PowerShell-modulen i Azure Resource Manager
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 521f8f29e2f8475ab7308f5646b94c6fc0f6a01f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398832"
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Skapa en intern lastbalanserare med hjälp av Azure PowerShell-modulen

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Mall](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Kom igång med konfigurationen

I den här artikeln finns en beskrivning av hur du skapar en intern lastbalanserare med hjälp av Azure Resource Manager och Azure PowerShell-modulen. Resource Manager-distributionsmodellen tillhandahåller individuella konfigurationer av de objekt som behövs för att skapa en intern lastbalanserare. När objekten har skapats och konfigurerats kan de kombineras för att skapa en lastbalanserare.

Om du vill kunna distribuera en lastbalanserare måste du först skapa följande objekt:

* IP-adresspool på klientsidan: Privata IP-adressen för all inkommande nätverkstrafik.
* Backend adresspoolen: Nätverksgränssnitt som tar emot belastningsutjämnad trafik från klientdelens IP-adress.
* Regler för belastningsutjämning: (Käll- och local)-portkonfigurationen för belastningsutjämnaren.
* Avsökningskonfiguration: Avsökningar som kontrollerar hälsotillståndet för virtuella datorer.
* Ingående NAT-regler: Portregler för direkt åtkomst till virtuella datorer.

Mer information om komponenterna i lastbalanseraren finns i [Azure Resource Manager-stöd för lastbalanseraren](load-balancer-arm.md).

Följande steg beskriver hur du konfigurerar en lastbalanserare mellan två virtuella datorer.

## <a name="set-up-powershell-to-use-resource-manager"></a>Konfigurera PowerShell för användning med Resource Manager

Kontrollera att du har den senaste produktionsversionen av Azure PowerShell-modulen. PowerShell måste vara korrekt konfigurerad för att du ska kunna komma åt din Azure-prenumeration.

### <a name="step-1-start-powershell"></a>Steg 1: Starta PowerShell

Starta PowerShell-modulen för Azure Resource Manager.

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="step-2-view-your-subscriptions"></a>Steg 2: Visa dina prenumerationer

Kontrollera vilka Azure-prenumerationer som är tillgängliga.

```azurepowershell-interactive
Get-AzSubscription
```

Ange dina autentiseringsuppgifter när du uppmanas göra detta.

### <a name="step-3-select-the-subscription-to-use"></a>Steg 3: Välj prenumerationen som du använder

Välj vilken av dina Azure-prenumerationer som ska användas för att distribuera lastbalanseraren.

```azurepowershell-interactive
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Steg 4: Välj resursgruppen för belastningsutjämnaren

Skapa en ny resursgrupp för lastbalanseraren. Hoppa över det här steget om du använder en befintlig resursgrupp.

```azurepowershell-interactive
New-AzResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standard för alla resurser i resursgruppen. Använd alltid samma resursgrupp för alla kommandon som används när du skapar en lastbalanserare.

I exemplet ovan skapade vi resursgruppen **NRP-RG** och platsen USA, västra.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Skapa ett virtuellt nätverk och en IP-adress för IP-adresspoolen på klientsidan

Skapar ett undernät för det virtuella nätverket som tilldelas till variabeln **$backendSubnet**.

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Skapa ett virtuellt nätverk.

```azurepowershell-interactive
$vnet= New-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Det virtuella nätverket skapas. Undernätet **LB-Subnet-BE** läggs till i det virtuella nätverket **NRPVNet**. Värdena tilldelas till variabeln **$vnet**.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Skapa en IP-adresspool på klientsidan och en serverdelsadresspool

Skapa en IP-adresspool på klientsidan för inkommande trafik och en serverdelsadresspool för att ta emot belastningsutjämnad trafik.

### <a name="step-1-create-a-front-end-ip-pool"></a>Steg 1: Skapa en IP-adresspool på klientsidan

Skapa en IP-adresspool på klientsidan med den privata IP-adressen 10.0.2.5 för undernätet 10.0.2.0/24. Den här adressen utgör slutpunkten för all inkommande nätverkstrafik.

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Steg 2: Skapa en backend-adresspool

Konfigurera en serverdelsadresspool som används för att ta emot inkommande trafik från IP-adresspoolen på klientsidan:

```powershell
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Skapa konfigurationsregler, avsökning och lastbalanserare

Ange regler för lastbalanseringsresursen efter att IP-adresspoolen på klientsidan och serverdelsadresspoolen har skapats.

### <a name="step-1-create-the-configuration-rules"></a>Steg 1: Skapa konfigurationsregler

I exemplet skapas följande fyra regelobjekt:

* En inkommande NAT-regel för Remote Desktop Protocol (RDP): Omdirigerar all inkommande trafik på port 3441 till port 3389.
* En andra inkommande NAT-regeln för RDP: Omdirigerar all inkommande trafik på port 3442 till port 3389.
* En hälsoavsökningsregel: Kontrollerar hälsotillståndet för sökvägen HealthProbe.aspx.
* En belastningsutjämningsregel: Belastningsutjämnar all inkommande trafik på den offentliga porten 80 till den lokala porten 80 i backend-adresspoolen.

```azurepowershell-interactive
$inboundNATRule1= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>Steg 2: Skapa lastbalanseraren

Skapa lastbalanseraren och kombinera regelobjekten (inkommande NAT-regel för RDP, lastbalanserare och hälsoavsökning):

```azurepowershell-interactive
$NRPLB = New-AzLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Skapa nätverksgränssnitt

När du har skapat den interna lastbalanseraren måste du definiera vilka nätverksgränssnitt som kan ta emot den inkommande lastbalanserade nätverkstrafiken, NAT-reglerna och avsökningen. Varje nätverksgränssnitt konfigureras separat och kan tilldelas till en virtuell dator vid ett senare tillfälle.

### <a name="step-1-create-the-first-network-interface"></a>Steg 1: Skapa det första nätverksgränssnittet

Hitta det virtuella nätverket och undernätet för resursen. Dessa värden används för att skapa nätverksgränssnitten:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Skapa det första nätverksgränssnittet med namnet **lb-nic1-be**. Tilldela gränssnittet till lastbalanseraren för serverdelsadresspoolen. Koppla den första NAT-regeln för RDP till det här nätverkskortet:

```azurepowershell-interactive
$backendnic1= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Steg 2: Skapa det andra nätverksgränssnittet

Skapa det andra nätverksgränssnittet med namnet **lb-nic2-be**. Tilldela det andra gränssnittet till samma lastbalanserare för serverdelsadresspoolen som användes till det första gränssnittet. Koppla det andra nätverkskortet till den andra NAT-regeln för RDP:

```azurepowershell-interactive
$backendnic2= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Granska konfigurationen:

    $backendnic1

Inställningarna bör vara följande:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>Steg 3: Tilldela nätverkskortet till en virtuell dator

Tilldela nätverkskortet till en virtuell dator med hjälp av kommandot `Add-AzVMNetworkInterface`.

Stegvisa anvisningar för att skapa en virtuell dator och tilldela den till ett nätverkskort finns i [Skapa en virtuell Azure-dator med PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Lägga till nätverksgränssnittet

När den virtuella datorn har skapats kan du lägga till nätverksgränssnittet.

### <a name="step-1-store-the-load-balancer-resource"></a>Steg 1: Store belastningsutjämningsresursen

Lagra lastbalanseringsresursen i en variabel (om du inte har gjort det redan). Här använder vi variabelnamnet **$lb**. Använd samma namn för attributvärdena i skriptet som för lastbalanseringsresurserna som skapades i föregående steg.

```azurepowershell-interactive
$lb = Get-AzLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Steg 2: Store backend konfigurationen

Lagra serverdelskonfigurationen i variabeln **$backend**.

```azurepowershell-interactive
$backend = Get-AzLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Steg 3: Store nätverksgränssnittet

Lagra nätverksgränssnittet i en annan variabel. Det här gränssnittet skapades i ”Skapa nätverksgränssnitt, steg 1”. Här använder vi variabelnamnet **$nic1**. Använd samma namn på nätverksgränssnittet som i föregående exempel.

```azurepowershell-interactive
$nic = Get-AzNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Steg 4: Ändra backend konfigurationen

Ändra backend-konfigurationen för nätverksgränssnittet.

```azurepowershell-interactive
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Steg 5: Spara objektet för nätverksgränssnittet

Spara objektet för nätverksgränssnittet.

```azurepowershell-interactive
Set-AzNetworkInterface -NetworkInterface $nic
```

När gränssnittet har lagts till i serverdelspoolen kan nätverkstrafiken belastningsutjämnas enligt reglerna. Reglerna konfigurerades under ”Skapa konfigurationsregler, avsökning och lastbalanserare”.

## <a name="update-an-existing-load-balancer"></a>Uppdatera en befintlig lastbalanserare

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Steg 1: Tilldela belastningsutjämningsobjektet till en variabel

Tilldela lastbalanseringsobjektet (från det föregående exemplet) till variabeln **$slb** med hjälp av kommandot `Get-AzLoadBalancer`:

```azurepowershell-interactive
$slb = Get-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Steg 2: Lägg till en NAT-regel

Lägg till en ny NAT-regel för inkommande i en befintlig lastbalanserare. Använd port 81 till IP-adresspoolen på klientsidan och port 8181 till serverdelsadresspoolen:

```azurepowershell-interactive
$slb | Add-AzLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Steg 3: Spara konfigurationen

Spara den nya konfigurationen med hjälp av kommandot `Set-AzureLoadBalancer`:

```azurepowershell-interactive
$slb | Set-AzLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Ta bort en befintlig lastbalanserare

Ta bort lastbalanseraren **NRP-LB** i resursgruppen **NRP-RG** med hjälp av kommandot `Remove-AzLoadBalancer`:

```azurepowershell-interactive
Remove-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Använd den valfria växeln **-Force** för att stoppa visningen av en bekräftelsefråga om borttagning.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera distributionsläge för lastbalanserare](load-balancer-distribution-mode.md)
* [Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)