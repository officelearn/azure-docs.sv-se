---
title: "Skapa en intern Azure-belastningsutjämnare – PowerShell | Microsoft Docs"
description: "Ta reda på hur du skapar en intern belastningsutjämnare med PowerShell i Resource Manager"
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
ms.openlocfilehash: 1215ca8ff4d45e3b910b8e0ec0bd6833e4bfc308
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-internal-load-balancer-using-powershell"></a>Skapa en intern belastningsutjämnare med PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Mall](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln beskriver Resource Manager-distributionsmodellen, som Microsoft rekommenderar för de flesta nya distributioner i stället för [den klassiska distributionsmodellen](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

Följande steg beskriver hur du skapar en intern belastningsutjämnare med hjälp av Azure Resource Manager och PowerShell. Med Azure Resource Manager konfigureras objekten som behövs för att skapa en intern belastningsutjämnare för sig och sedan kombineras de för att skapa en belastningsutjämnare.

Skapa och konfigurera följande objekt för att distribuera en belastningsutjämnare:

* IP-konfiguration på klientsidan – konfigurerar den offentliga IP-adressen för inkommande nätverkstrafik.
* Serverdelsadresspool – konfigurerar nätverksgränssnitt som tar emot belastningsutjämnad trafik från IP-poolen på klientsidan.
* Belastningsutjämningsregler – käll- och lokalportkonfiguration för belastningsutjämnaren.
* Avsökningar – konfigurerar hälsoavsökningar för virtuella datorinstanser.
* Ingående NAT-regler – konfigurerar portreglerna för direktåtkomst till någon av de virtuella datorinstanserna.

Mer information om belastningsutjämningskomponenter för Azure Resource Manager finns i [Azure Resource Manager support for Load Balancer](load-balancer-arm.md) (Belastningsutjämningsstöd i Azure Resource Manager).

Följande steg beskriver hur du konfigurerar en belastningsutjämnare mellan två virtuella datorer.

## <a name="set-up-powershell-to-use-resource-manager"></a>Konfigurera PowerShell för användning med Resource Manager

Kontrollera att du har den senaste produktionsversionen av Azure-modulen för PowerShell, och att PowerShell är konfigurerat på rätt sätt för att få åtkomst till din Azure-prenumeration.

### <a name="step-1"></a>Steg 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Steg 2

Kontrollera kontots prenumerationer

```powershell
Get-AzureRmSubscription
```

Du ombeds att autentisera dig med dina autentiseringsuppgifter.

### <a name="step-3"></a>Steg 3

Välj vilka av dina Azure-prenumerationer som du vill använda.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="create-resource-group-for-load-balancer"></a>Skapa resursgrupp för belastningsutjämnare

Skapa en ny resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp)

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standard för resurserna i den resursgruppen. Se till att alla kommandon för att skapa en belastningsutjämnare använder samma resursgrupp.

I exemplet ovan skapade vi resursgruppen **NRP-RG** och platsen **Västra USA**.

## <a name="create-a-virtual-network-and-a-private-ip-address-for-a-front-end-ip-pool"></a>Skapa ett virtuellt nätverk och en privat IP-adress för IP-pooler på klientsidan

Skapar ett undernät för det virtuella nätverket som tilldelas till variabeln $backendSubnet

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Skapa ett virtuellt nätverk:

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Skapar det virtuella nätverket och lägger till undernätet lb-subnet-be i det virtuella nätverket NRPVNet som tilldelas till variabeln $vnet

## <a name="create-a-front-end-ip-pool-and-back-end-address-pool"></a>Skapa en IP-adresspool på klientsidan och en serverdelsadresspool

Konfigurera en IP-pool på klientsidan för inkommande belastningsutjämningstrafik och en serverdelsadresspool för att ta emot belastningsutjämnad trafik.

### <a name="step-1"></a>Steg 1

Skapa en IP-pool på klientsidan med den privata IP-adressen 10.0.2.5 för undernätet 10.0.2.0/24 som är slutpunkten för den inkommande nätverkstrafiken.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2"></a>Steg 2

Konfigurera en serverdelsadresspool som används för att ta emot inkommande trafik från IP-poolen på klientsidan:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-load-balancing-rules-nat-rules-probe-and-load-balancer"></a>Skapa belastningsutjämningsregler, NAT-regler, avsökning och belastningsutjämnare

När du har skapat IP-poolen på klientsidan och serverdelsadresspoolen skapar du reglerna som hör till belastningsutjämningsresursen:

### <a name="step-1"></a>Steg 1

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

I föregående exempel skapar du följande objekt:

* NAT-regel som anger att all inkommande trafik till port 3441 skickas till port 3389.
* en andra NAT-regel som anger att all inkommande trafik till port 3442 skickas till port 3389.
* en belastningsutjämningsregel som balanserar all inkommande trafik på den offentliga porten 80 till den lokala porten 80 i serverdelsadresspoolen.
* en avsökningsregel som kontrollerar hälsotillståndet för sökvägen HealthProbe.aspx

### <a name="step-2"></a>Steg 2

Skapa belastningsutjämnaren genom att lägga samman alla objekt (NAT-regler, belastningsutjämningsregler, avsökningskonfigurationer):

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-network-interfaces"></a>Skapa nätverksgränssnitt

När du har skapat den interna belastningsutjämnaren måste du definiera vilka nätverksgränssnitt som kan ta emot den inkommande belastningsutjämnade nätverkstrafiken, NAT-reglerna och avsökningen. Nätverksgränssnittet konfigureras i det här fallet separat och kan tilldelas till en virtuell dator senare.

### <a name="step-1"></a>Steg 1

Hämta det virtuella resursnätverket och undernätet för att skapa nätverksgränssnitt:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Det här steget skapar ett nätverksgränssnitt som tillhör belastningsutjämnarens serverdelspool och associerar den första NAT-regeln för RDP för det här nätverksgränssnittet:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2"></a>Steg 2

Skapa ett andra nätverksgränssnitt som kallas LB-Nic2-BE:

Det här steget skapar ett andra nätverksgränssnitt, som tilldelas samma serverdelspool för belastningsutjämnaren och associerar den andra NAT-regeln som skapats för RDP:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Slutresultatet visar följande utdata:

    $backendnic1

Förväntad utdata:

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



### <a name="step-3"></a>Steg 3

Använd kommandot Add-AzureRmVMNetworkInterface för att tilldela nätverkskortet till en virtuell dator.

Du kan hitta detaljerade anvisningarna för att skapa en virtuell dator och tilldela den till ett nätverkskort enligt dokumentationen: [Skapa en virtuell Azure-dator med PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Lägga till nätverksgränssnittet

Om du redan har skapat en virtuell dator kan du lägga till nätverksgränssnittet med följande steg:

### <a name="step-1"></a>Steg 1

Läs in belastningsutjämningsresursen i en variabel (om du inte har gjort det redan). Variabeln som används kallas $lb och använder samma namn från belastningsutjämningsresursen som skapades under föregående steg.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2"></a>Steg 2

Läs in serverdelskonfigurationen i en variabel.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3"></a>Steg 3

Läs in nätverksgränssnittet som du redan skapat i en variabel. variabelnamnet som används är $nic. Namnet på nätverksgränssnittet som används är detsamma som i föregående exempel.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4"></a>Steg 4

Ändra serverdelskonfigurationen i nätverksgränssnittet.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5"></a>Steg 5

Spara objektet för nätverksgränssnittet.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

När ett nätverksgränssnitt har lagts till i serverdelspoolen för belastningsutjämnaren börjar det tar emot nätverkstrafik baserat på reglerna för belastningsutjämning för den belastningsutjämningsresursen.

## <a name="update-an-existing-load-balancer"></a>Uppdatera en befintlig belastningsutjämnare

### <a name="step-1"></a>Steg 1
Med hjälp av belastningsutjämnaren från föregående exempel tilldelar du belastningsutjämningsobjektet till variabeln $slb med hjälp av Get-AzureRmLoadBalancer

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2"></a>Steg 2

I följande exempel lägger du till en ny inkommande NAT-regel som använder port 81 på klientsidan och port 8181 för serverdelspoolen till en befintlig belastningsutjämnare

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3"></a>Steg 3

Spara den nya konfigurationen med hjälp av Set-AzureLoadBalancer

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-a-load-balancer"></a>Ta bort en belastningsutjämnare

Använd kommandot Remove-AzureRmLoadBalancer för att ta bort en tidigare skapad belastningsutjämnare med namnet NRP-LB från resursgruppen NRP-RG

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Du kan använda den valfria växeln -Force (Framtvinga) om du inte vill uppmanas att bekräfta borttagningen.

## <a name="next-steps"></a>Nästa steg

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
