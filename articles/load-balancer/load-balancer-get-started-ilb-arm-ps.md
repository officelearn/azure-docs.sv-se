---
title: "Skapa en intern Azure-belastningsutjämnare med PowerShell | Microsoft Docs"
description: "Ta reda på hur du skapar en intern belastningsutjämnare med hjälp av Azure PowerShell-modulen i Azure Resource Manager"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 7950c3e23463260c4c89c2a4f6b28bbc2a34b7c2
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Skapa en intern belastningsutjämnare med hjälp av Azure PowerShell-modulen

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Mall](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure har två distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) och den [klassiska distributionsmodellen](load-balancer-get-started-ilb-classic-ps.md). I den här artikeln får du veta mer om hur du skapar en belastningsutjämnare med hjälp av Resource Manager-distributionsmodellen. Microsoft rekommenderar du använder Azure Resource Manager till de flesta nya distributionerna.

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Kom igång med konfigurationen

I den här artikeln finns en beskrivning av hur du skapar en intern belastningsutjämnare med hjälp av Azure Resource Manager och Azure PowerShell-modulen. Resource Manager-distributionsmodellen tillhandahåller individuella konfigurationer av de objekt som behövs för att skapa en intern belastningsutjämnare. När objekten har skapats och konfigurerats kan de kombineras för att skapa en belastningsutjämnare.

Om du vill kunna distribuera en belastningsutjämnare måste du först skapa följande objekt:

* IP-adresspool på klientsidan: Den privata IP-adressen för all inkommande nätverkstrafik.
* Serverdelsadresspool: De nätverksgränssnitt som tar emot belastningsutjämnad trafik från IP-adressen på klientsidan.
* Belastningsutjämningsregler: Käll- och lokalportkonfiguration för belastningsutjämnaren.
* Avsökningskonfiguration: Avsökningar som kontrollerar hälsotillståndet för virtuella datorer.
* Regler för ingående NAT: Portregler för direkt åtkomst till virtuella datorer.

Mer information om komponenterna i belastningsutjämnaren finns i [Azure Resource Manager-stöd för belastningsutjämnaren](load-balancer-arm.md).

Följande steg beskriver hur du konfigurerar en belastningsutjämnare mellan två virtuella datorer.

## <a name="set-up-powershell-to-use-resource-manager"></a>Konfigurera PowerShell för användning med Resource Manager

Kontrollera att du har den senaste produktionsversionen av Azure PowerShell-modulen. PowerShell måste vara korrekt konfigurerad för att du ska kunna komma åt din Azure-prenumeration.

### <a name="step-1-start-powershell"></a>Steg 1: Starta PowerShell

Starta PowerShell-modulen för Azure Resource Manager.

```powershell
Login-AzureRmAccount
```

### <a name="step-2-view-your-subscriptions"></a>Steg 2: Visa dina prenumerationer

Kontrollera vilka Azure-prenumerationer som är tillgängliga.

```powershell
Get-AzureRmSubscription
```

Ange dina autentiseringsuppgifter när du uppmanas göra detta.

### <a name="step-3-select-the-subscription-to-use"></a>Steg 3: Välj den prenumeration som du vill använda

Välj vilken av dina Azure-prenumerationer som ska användas för att distribuera belastningsutjämnaren.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Steg 4: Välj resursgrupp för belastningsutjämnaren

Skapa en ny resursgrupp för belastningsutjämnaren. Hoppa över det här steget om du använder en befintlig resursgrupp.

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standard för alla resurser i resursgruppen. Använd alltid samma resursgrupp för alla kommandon som används när du skapar en belastningsutjämnare.

I exemplet ovan skapade vi resursgruppen **NRP-RG** och platsen USA, västra.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Skapa ett virtuellt nätverk och en IP-adress för IP-adresspoolen på klientsidan

Skapar ett undernät för det virtuella nätverket som tilldelas till variabeln **$backendSubnet**.

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Skapa ett virtuellt nätverk.

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Det virtuella nätverket skapas. Undernätet **LB-Subnet-BE** läggs till i det virtuella nätverket **NRPVNet**. Värdena tilldelas till variabeln **$vnet**.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Skapa en IP-adresspool på klientsidan och en serverdelsadresspool

Skapa en IP-adresspool på klientsidan för inkommande trafik och en serverdelsadresspool för att ta emot belastningsutjämnad trafik.

### <a name="step-1-create-a-front-end-ip-pool"></a>Steg 1: Skapa en IP-adresspool på klientsidan

Skapa en IP-adresspool på klientsidan med den privata IP-adressen 10.0.2.5 för undernätet 10.0.2.0/24. Den här adressen utgör slutpunkten för all inkommande nätverkstrafik.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Steg 2: Skapa en serverdelsadresspool

Konfigurera en serverdelsadresspool som används för att ta emot inkommande trafik från IP-adresspoolen på klientsidan:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Skapa konfigurationsregler, avsökning och belastningsutjämnare

Ange regler för belastningsutjämningsresursen efter att IP-adresspoolen på klientsidan och serverdelsadresspoolen har skapats.

### <a name="step-1-create-the-configuration-rules"></a>Steg 1: Skapa konfigurationsregler

I exemplet skapas följande fyra regelobjekt:

* En inkommande NAT-regel för Remote Desktop Protocol (RDP): Omdirigerar all inkommande trafik på port 3441 till port 3389.
* En andra inkommande NAT-regel för RDP: Omdirigerar all inkommande trafik på port 3442 till port 3389.
* En hälsoavsökningsregel: Kontrollerar hälsotillståndet för sökvägen HealthProbe.aspx.
* En belastningsutjämningsregel: Belastningsutjämnar all inkommande trafik på den offentliga porten 80 till den lokala porten 80 i serverdelsadresspoolen.

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>Steg 2: Skapa belastningsutjämnaren

Skapa belastningsutjämnaren och kombinera regelobjekten (inkommande NAT-regel för RDP, belastningsutjämnare och hälsoavsökning):

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Skapa nätverksgränssnitt

När du har skapat den interna belastningsutjämnaren måste du definiera vilka nätverksgränssnitt som kan ta emot den inkommande belastningsutjämnade nätverkstrafiken, NAT-reglerna och avsökningen. Varje nätverksgränssnitt konfigureras separat och kan tilldelas till en virtuell dator vid ett senare tillfälle.

### <a name="step-1-create-the-first-network-interface"></a>Steg 1: Skapa det första nätverksgränssnittet

Hitta det virtuella nätverket och undernätet för resursen. Dessa värden används för att skapa nätverksgränssnitten:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Skapa det första nätverksgränssnittet med namnet **lb-nic1-be**. Tilldela gränssnittet till belastningsutjämnaren för serverdelsadresspoolen. Koppla den första NAT-regeln för RDP till det här nätverkskortet:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Steg 2: Skapa det andra nätverksgränssnittet

Skapa det andra nätverksgränssnittet med namnet **lb-nic2-be**. Tilldela det andra gränssnittet till samma belastningsutjämnare för serverdelsadresspoolen som användes till det första gränssnittet. Koppla det andra nätverkskortet till den andra NAT-regeln för RDP:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Granska konfigurationen:

    $backendnic1

Inställningarna bör vara följande:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
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

Tilldela nätverkskortet till en virtuell dator med hjälp av kommandot `Add-AzureRmVMNetworkInterface`.

Stegvisa anvisningar för att skapa en virtuell dator och tilldela den till ett nätverkskort finns i [Skapa en virtuell Azure-dator med PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Lägga till nätverksgränssnittet

När den virtuella datorn har skapats kan du lägga till nätverksgränssnittet.

### <a name="step-1-store-the-load-balancer-resource"></a>Steg 1: Lagra belastningsutjämningsresursen

Lagra belastningsutjämningsresursen i en variabel (om du inte har gjort det redan). Här använder vi variabelnamnet **$lb**. Använd samma namn för attributvärdena i skriptet som för belastningsutjämningsresurserna som skapades i föregående steg.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Steg 2: Lagra serverdelskonfigurationen

Lagra serverdelskonfigurationen i variabeln **$backend**.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Steg 3: Lagra nätverksgränssnittet

Lagra nätverksgränssnittet i en annan variabel. Det här gränssnittet skapades i ”Skapa nätverksgränssnitt, steg 1”. Här använder vi variabelnamnet **$nic1**. Använd samma namn på nätverksgränssnittet som i föregående exempel.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Steg 4: Ändra serverdelskonfigurationen

Ändra backend-konfigurationen för nätverksgränssnittet.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Steg 5: Spara objektet för nätverksgränssnittet

Spara objektet för nätverksgränssnittet.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

När gränssnittet har lagts till i serverdelspoolen kan nätverkstrafiken belastningsutjämnas enligt reglerna. Reglerna konfigurerades under ”Skapa konfigurationsregler, avsökning och belastningsutjämnare”.

## <a name="update-an-existing-load-balancer"></a>Uppdatera en befintlig belastningsutjämnare

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Steg 1: Tilldela ett belastningsutjämningsobjekt till en variabel

Tilldela belastningsutjämningsobjektet (från det föregående exemplet) till variabeln **$slb** med hjälp av kommandot `Get-AzureRmLoadBalancer`:

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Steg 2: Lägg till en NAT-regel

Lägg till en ny NAT-regel för inkommande i en befintlig belastningsutjämnare. Använd port 81 till IP-adresspoolen på klientsidan och port 8181 till serverdelsadresspoolen:

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Steg 3: Spara konfigurationen

Spara den nya konfigurationen med hjälp av kommandot `Set-AzureLoadBalancer`:

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Ta bort en befintlig belastningsutjämnare

Ta bort belastningsutjämnaren **NRP-LB** i resursgruppen **NRP-RG** med hjälp av kommandot `Remove-AzureRmLoadBalancer`:

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Använd den valfria växeln **-Force** för att stoppa visningen av en bekräftelsefråga om borttagning.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)
* [Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
