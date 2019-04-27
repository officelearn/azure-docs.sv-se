---
title: Kontrollera routning i ett virtuellt nätverk för Azure - CLI - klassisk | Microsoft Docs
description: Lär dig hur du styr routning i virtuella nätverk med Azure CLI i den klassiska distributionsmodellen
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: e1b8bb3544a08b60564ceb5bd7e1666214059e09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743929"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Kontrollera Routning och använder virtuella installationer (klassisk) med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (klassisk)](virtual-network-create-udr-classic-ps.md)
> * [CLI (klassisk)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [styr Routning och använder virtuella installationer i Resource Manager-distributionsmodellen](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Exemplet Azure CLI-kommandona nedan förväntar sig en enkel miljö som redan har skapats baserat på scenariot ovan. Om du vill köra kommandon som de visas i det här dokumentet, skapa miljön visas i [skapa ett virtuellt nätverk (klassisk) med Azure CLI](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Skapa den användardefinierade vägen för klientdelens undernät
Följ stegen nedan om du vill skapa routningstabell och väg som behövs för klientdelens undernät baserat på scenariot ovan.

1. Kör följande kommando för att växla till klassiskt läge:

    ```azurecli
    azure config mode asm
    ```

    Utdata:

        info:    New mode is asm

2. Kör följande kommando för att skapa en routningstabell för klientdelsundernätet:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Utdata:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parametrar:
   
   * **-l (eller --location)**. Azure-region där den nya NSG: N kommer att skapas. För vårt scenario, *westus*.
   * **-n (eller --name)**. Namnet för den nya NSG. För vårt scenario, *NSG-klientdel*.
3. Kör följande kommando för att skapa en väg i routningstabellen för att skicka all trafik till serverdelsundernätet (192.168.2.0/24) till den **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Utdata:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametrar:
   
   * **-r (eller--route-table-name)**. Namnet på routningstabellen där flödet kommer att läggas till. För vårt scenario, *UDR-klientdel*.
   * **-a (eller --address-prefix)**. Adressprefix för undernätet där paket är avsedd för. För vårt scenario, *192.168.2.0/24*.
   * **-t (eller--nästa hopptyp)**. Typ av objekt trafik skickas till. Möjliga värden är *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*, eller *ingen*.
   * **-p (eller--nästa-hopp-ip-adress**). IP-adressen för nästa hopp. För vårt scenario, *192.168.0.4*.
4. Kör följande kommando för att associera routningstabellen som skapats med den **klientdel** undernät:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Utdata:
   
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
   
   * **-t (eller--vnet-namn)**. Namnet på det virtuella nätverket där undernätet finns. I vårt scenario, *TestVNet*.
   * **-n (eller--subnet-name**. Namnet på undernätet routningstabellen kommer att läggas till. I vårt scenario, *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Skapa den användardefinierade vägen för backend-undernät
Utför följande steg för att skapa routningstabell och väg som behövs för serverdelsundernätet baserat på scenariot:

1. Kör följande kommando för att skapa en routningstabell för backend-undernät:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Kör följande kommando för att skapa en väg i routningstabellen för att skicka all trafik till klientdelsundernätet (192.168.1.0/24) till den **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Kör följande kommando för att associera routningstabellen med den **serverdel** undernät:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

