---
title: Använd Azure-anpassade vägar för att aktivera KMS-aktivering med Tvingad tunnel trafik | Microsoft Docs
description: Visar hur du använder Azure-anpassade vägar för att aktivera KMS-aktivering när du använder Tvingad tunnel trafik i Azure.
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
ms.openlocfilehash: 1c2050969e95b521554bba100b688add3a987a80
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526749"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Det går inte att aktivera Windows i ett scenario med Tvingad tunnel trafik

Den här artikeln beskriver hur du löser problemet med KMS-aktivering som du kan uppleva när du aktiverar Tvingad tunnel trafik i plats-till-plats-VPN-anslutning eller ExpressRoute-scenarier.

## <a name="symptom"></a>Symptom

Du aktiverar [Tvingad tunnel](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) trafik i Azure Virtual Network-undernät för att dirigera all Internet-baserad trafik tillbaka till ditt lokala nätverk. I det här scenariot går det inte att aktivera Windows med Azure Virtual Machines (virtuella datorer) som kör Windows.

## <a name="cause"></a>Orsak

De virtuella Azure Windows-datorerna måste ansluta till Azure KMS-servern för Windows-aktivering. Aktiveringen kräver att aktiverings förfrågan kommer från en offentlig Azure-IP-adress. I det tvingade tunnel scenariot Miss lyckas aktiveringen eftersom aktiverings förfrågan kommer från ditt lokala nätverk i stället för från en offentlig Azure-IP-adress.

## <a name="solution"></a>Lösning

Lös problemet genom att använda den anpassade Azure-vägen för att dirigera aktiverings trafik till Azure KMS-servern.

IP-adressen för KMS-servern för det globala Azure-molnet är 23.102.135.246. Dess DNS-namn är kms.core.windows.net. Om du använder andra Azure-plattformar som Azure Germany måste du använda IP-adressen för motsvarande KMS-server. Mer information finns i följande tabell:

|Plattform| KMS DNS|KMS-IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Tyskland|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure Kina 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Följ dessa steg om du vill lägga till en anpassad väg:

### <a name="for-resource-manager-vms"></a>För virtuella Resource Manager-datorer

 

> [!NOTE] 
> Aktivering använder offentliga IP-adresser och kommer att påverkas av en standard-SKU Load Balancer-konfiguration. Läs noggrant igenom [utgående anslutningar i Azure](../../load-balancer/load-balancer-outbound-connections.md) för att lära dig mer om kraven.

1. Öppna Azure PowerShell och logga in [på din Azure-prenumeration](/powershell/azure/authenticate-azureps).
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
3. Gå till den virtuella datorn som har aktiverings problem. Använd [PsPing](/sysinternals/downloads/psping) för att testa om den kan komma åt KMS-servern:

    ```console
    psping kms.core.windows.net:1688
    ```

4. Försök att aktivera Windows och se om problemet är löst.

### <a name="for-classic-vms"></a>För klassiska virtuella datorer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Öppna Azure PowerShell och logga in [på din Azure-prenumeration](/powershell/azure/authenticate-azureps).
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

3. Gå till den virtuella datorn som har aktiverings problem. Använd [PsPing](/sysinternals/downloads/psping) för att testa om den kan komma åt KMS-servern:

    ```console
    psping kms.core.windows.net:1688
    ```

4. Försök att aktivera Windows och se om problemet är löst.

## <a name="next-steps"></a>Nästa steg

- [Nycklar för KMS-klientinstallation](/windows-server/get-started/kmsclientkeys)
- [Granska och välj aktiverings metoder](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj134256(v=ws.11))
