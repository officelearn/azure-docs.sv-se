---
title: Styra routning i ett Azure Virtual Network-CLI-Klassiskt | Microsoft Docs
description: Lär dig hur du styr routning i virtuella nätverk med hjälp av Azure CLI i den klassiska distributions modellen
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 1193145b315175e6394db4caf93ab2e76a942ed9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058792"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Kontrol lera Routning och Använd virtuella apparater (klassisk) med Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (klassisk)](virtual-network-create-udr-classic-ps.md)
> * [CLI (klassisk)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [styra Routning och använda virtuella enheter i distributions modellen för Resource Manager](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Exempel på Azure CLI-kommandon nedan förväntar sig en enkel miljö som redan har skapats baserat på scenariot ovan. Om du vill köra kommandona som de visas i det här dokumentet skapar du miljön som visas i [skapa ett VNet (klassisk) med hjälp av Azure CLI](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Skapa UDR för klient delens undernät
Följ stegen nedan om du vill skapa en routningstabell och routning som krävs för klient dels under nätet baserat på scenariot ovan.

1. Kör följande kommando för att växla till klassiskt läge:

    ```azurecli
    azure config mode asm
    ```

    Resultat:

        info:    New mode is asm

2. Kör följande kommando för att skapa en routningstabell för klient dels under nätet:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Resultat:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parametrar:
   
   * **-l (eller --location)** . Azure-region där den nya NSG kommer att skapas. I vårt scenario, *väster*.
   * **-n (eller --name)** . Namnet på den nya NSG. I vårt scenario, *NSG-FrontEnd*.
3. Kör följande kommando för att skapa en väg i routningstabellen för att skicka all trafik som är avsedd för backend-undernätet (192.168.2.0/24) till **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Resultat:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametrar:
   
   * **-r (eller-Route-Table-Name)** . Namnet på den routningstabell där vägen ska läggas till. I vårt scenario, *UDR-FrontEnd*.
   * **-a (eller --address-prefix)** . Adressprefix för det undernät där paketen är avsedda för. För vårt scenario, *192.168.2.0/24*.
   * **-t (eller---nästa hopp-typ)** . Typ av objekt trafik kommer att skickas till. Möjliga värden är *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*eller *none*.
   * **-p (eller--Next-hop-IP-Address**). IP-adress för nästa hopp. För vårt scenario, *192.168.0.4*.
4. Kör följande kommando för att associera routningstabellen som skapats med **klient dels** under nätet:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Resultat:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parametrar:
   
   * **-t (eller--VNet-namn)** . Namnet på det VNet där under nätet finns. I vårt scenario, *TestVNet*.
   * **-n (eller--undernäts namn**. Namnet på under nätet som routningstabellen ska läggas till i. I vårt scenario, *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Skapa UDR för Server dels under nätet
Utför följande steg för att skapa den routningstabell och det flöde som krävs för backend-undernätet baserat på scenariot:

1. Kör följande kommando för att skapa en routningstabell för Server dels under nätet:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Kör följande kommando för att skapa en väg i routningstabellen för att skicka all trafik till klient dels under nätet (192.168.1.0/24) till **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Kör följande kommando för att associera routningstabellen med **Server dels** under nätet:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

