---
title: Använd anpassade Azure-vägar för att aktivera KMS-aktivering med påtvingad tunnelning | Microsoft-dokument
description: Visar hur du använder anpassade Azure-vägar för att aktivera KMS-aktivering när du använder tvångstunneler i Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 90034a56fcf5211059d37270e12391249f7a16b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920169"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Windows-aktivering misslyckas i forcerat tunnelscenario

I den hÃ¤r artikeln beskrivs hur du lÃ¶ser problemet med KMS-aktiveringsproblemet som kan uppstÃ¥ nÃ¤r du aktiverar tvingande tunnelning i VPN-anslutning från plats till plats eller ExpressRoute.

## <a name="symptom"></a>Symptom

Du aktiverar [tvångstunnelering](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) i Azures virtuella nätverksundernät för att dirigera all Internet-bunden trafik tillbaka till ditt lokala nätverk. I det här fallet Azure virtuella datorer (VIRTUELLA datorer) som kör Windows misslyckas med att aktivera Windows.

## <a name="cause"></a>Orsak

Virtuella Azure Windows-datorer måste ansluta till Azure KMS-servern för Windows-aktivering. Aktiveringen kräver att aktiveringsbegäran kommer från en offentlig Azure-IP-adress. I scenariot med påtvingad tunnel misslyckas aktiveringen eftersom aktiveringsbegäran kommer från ditt lokala nätverk i stället för från en offentlig Azure-IP-adress.

## <a name="solution"></a>Lösning

LÃ¶s problemet genom att anse den anpassade azure-vägen för att dirigera aktiverings traffic till Azure KMS-servern.

IP-adressen för KMS-servern för Azure Global-molnet är 23.102.135.246. Dess DNS-namn är kms.core.windows.net. Om du använder andra Azure-plattformar som Azure Germany måste du använda IP-adressen för motsvarande KMS-server. Mer information finns i följande tabell:

|Plattform| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Tyskland|kms.core.cloudapi.de|51.4.143.248|
|Azure för amerikanska myndigheter|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure Kina 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Så här lägger du till den anpassade vägen:

### <a name="for-resource-manager-vms"></a>För virtuella resurshanteraren-datorer

 

> [!NOTE] 
> Aktivering använder offentliga IP-adresser och påverkas av en standard SKU Load Balancer-konfiguration. Granska noggrant [utgående anslutningar i Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) för att lära dig mer om kraven.

1. Öppna Azure PowerShell och logga sedan [in på din Azure-prenumeration](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Kör följande kommandon:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Gå till den virtuella datorn som har aktiveringsproblem. Använd [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) för att testa om den kan nå KMS-servern:

        psping kms.core.windows.net:1688

4. Försök att aktivera Windows och se om problemet är löst.

### <a name="for-classic-vms"></a>För klassiska virtuella datorer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Öppna Azure PowerShell och logga sedan [in på din Azure-prenumeration](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Kör följande kommandon:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Gå till den virtuella datorn som har aktiveringsproblem. Använd [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) för att testa om den kan nå KMS-servern:

        psping kms.core.windows.net:1688

4. Försök att aktivera Windows och se om problemet är löst.

## <a name="next-steps"></a>Nästa steg

- [Nycklar för KMS-klientinstallation](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Granska och välj aktiveringsmetoder](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
