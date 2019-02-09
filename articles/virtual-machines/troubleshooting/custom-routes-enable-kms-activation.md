---
title: Använd Azure anpassade vägar för att aktivera KMS-aktivering med Tvingad tunneltrafik | Microsoft Docs
description: Visar hur du använder Azure anpassade vägar för att aktivera KMS-aktivering när med Tvingad tunneltrafik i Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: b121996530ea0618fc757f1ae12dfafde10ed7bb
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979385"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Windows-aktiveringen misslyckas i scenariot för Tvingad tunneltrafik

Den här artikeln beskriver hur du löser problemet för KMS-aktivering som kan uppstå när du aktiverar Tvingad tunneltrafik i VPN-anslutning för plats-till-plats eller ExpressRoute-scenarier.

## <a name="symptom"></a>Symtom

Du aktiverar [Tvingad tunneltrafik](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) på Azure virtuella undernätverk att dirigera all internetriktad trafik tillbaka till ditt lokala nätverk. I det här scenariot kan Azure virtuella datorer (VM) som kör Windows Server 2012 R2 (eller senare versioner av Windows) har aktivera Windows. Virtuella datorer som kör tidigare version av Windows men gick inte att aktivera Windows.

## <a name="cause"></a>Orsak

Azure Windows-datorer måste du ansluta till Azure KMS-servern för Windows-aktivering. Aktiveringen kräver att aktiveringsbegäran kommer från en Azure offentlig IP-adress. Tvingad tunneltrafik scenariot misslyckas aktiveringen eftersom aktiveringsbegäran kommer från det lokala nätverket i stället för från en Azure offentlig IP-adress.

## <a name="solution"></a>Lösning

Lös problemet genom att använda Azure anpassad väg till vägen aktivering trafik till Azure KMS-servern.

IP-adressen för KMS-servern för den globala Azure-molnet är 23.102.135.246. Its DNS name is kms.core.windows.net. Om du använder andra Azure-plattformar, till exempel Azure Tyskland, måste du använda IP-adressen för motsvarande KMS-servern. Mer information finns i följande tabell:

|Plattform| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Tyskland|kms.core.cloudapi.de|51.4.143.248|
|Azure för amerikanska myndigheter|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure Kina 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Lägg till anpassad väg genom att följa dessa steg:

### <a name="for-resource-manager-vms"></a>För Resource Manager-VM

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

1. Öppna Azure PowerShell, och sedan [logga in på Azure-prenumerationen](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Kör följande kommandon:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable
    ```
3. Gå till den virtuella datorn som har problem med aktivering. Använd [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) att testa om det kan nå KMS-servern:

        psping kms.core.windows.net:1688

4. Försök att aktivera Windows och se om problemet är löst.

### <a name="for-classic-vms"></a>För klassiska virtuella datorer

1. Öppna Azure PowerShell, och sedan [logga in på Azure-prenumerationen](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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

3. Gå till den virtuella datorn som har problem med aktivering. Använd [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) att testa om det kan nå KMS-servern:

        psping kms.core.windows.net:1688

4. Försök att aktivera Windows och se om problemet är löst.

## <a name="next-steps"></a>Nästa steg

- [Nycklar för KMS-klientinstallation](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Granska och välj Aktiveringsmetoder](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
