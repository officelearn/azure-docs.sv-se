---
title: Styra routning i ett Azure VNet – PowerShell – Klassiskt
description: Lär dig hur du styr routning i virtuella nätverk med PowerShell | Form
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: ffeb8e1347d75af2be59763faf0b512c0b329221
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975350"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Kontrol lera Routning och Använd virtuella apparater (klassisk) med PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (klassisk)](virtual-network-create-udr-classic-ps.md)
> * [CLI (klassisk)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Innan du börjar arbeta med Azure-resurser är det viktigt att du vet att Azure för närvarande har två distributionsmodeller: Azure Resource Manager och klassisk. Se till att du förstår [distributionsmodeller och verktyg](../azure-resource-manager/management/deployment-models.md) innan du börjar arbeta med Azure-resurser. Du kan visa dokumentationen för olika verktyg genom att välja ett alternativ överst i den här artikeln. Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Exempel Azure PowerShell kommandon nedan förväntar sig en enkel miljö som redan har skapats baserat på scenariot ovan. Om du vill köra kommandona som de visas i det här dokumentet skapar du miljön som visas i [skapa ett VNet (klassisk) med hjälp av PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Skapa UDR för klient delens undernät
Följ stegen nedan om du vill skapa en routningstabell och routning som krävs för klient dels under nätet baserat på scenariot ovan.

1. Kör följande kommando för att skapa en routningstabell för klient dels under nätet:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Kör följande kommando för att skapa en väg i routningstabellen för att skicka all trafik som är avsedd för backend-undernätet (192.168.2.0/24) till **FW1** VM (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Kör följande kommando för att associera routningstabellen med **klient dels** under nätet:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Skapa UDR för Server dels under nätet
Utför följande steg för att skapa den routningstabell och det flöde som krävs för Server dels under nätet baserat på scenariot:

1. Kör följande kommando för att skapa en routningstabell för Server dels under nätet:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Kör följande kommando för att skapa en väg i routningstabellen för att skicka all trafik till klient dels under nätet (192.168.1.0/24) till **FW1** VM (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Kör följande kommando för att associera routningstabellen med **Server dels** under nätet:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Aktivera IP-vidarebefordran på den virtuella datorn FW1

Utför följande steg för att aktivera IP-vidarebefordring i den virtuella datorn FW1:

1. Kör följande kommando för att kontrol lera status för IP-vidarebefordring:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Kör följande kommando för att aktivera IP-vidarebefordran för den virtuella datorn *FW1* :

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
