---
title: "Skapa ett virtuellt nätverk med Azure CLI 1.0 | Microsoft Docs"
description: "Lär dig hur du skapar ett virtuellt nätverk med Azure CLI 1.0 | Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Skapa ett virtuellt nätverk med Azure CLI

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure har två distributionsmodeller: Azure Resource Manager och klassisk. Microsoft rekommenderar att skapa resurser med Resource Manager-distributionsmodellen. Mer information om skillnaderna mellan de två modellerna finns i artikeln [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Förstå Azure-distributionsmodellerna).

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 2.0](virtual-networks-create-vnet-arm-cli.md) – vår nästa generations CLI för distributionsmodellen resurshantering
- [Azure CLI 1.0](#create-a-virtual-network) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Om du vill skapa ett virtuellt nätverk med Azure CLI, gör du följande:

1. Installera och konfigurera Azure CLI genom att följa stegen i den [installera och konfigurera Azure CLI](../cli-install-nodejs.md) artikel.

2. Skapa ett VNet och ett undernät:

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    Förväntad utdata:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    Parametrar som används:

   * **--vnet**. Namnet på den VNet som ska skapas. I vårt exempel, *TestVNet*
   * **-e (eller---adressutrymmet)**. VNet-adressutrymmet. I vårt scenario, *192.168.0.0*
   * **-i (eller - cidr)**. Nätverksmasken i CIDR-format. I vårt scenario, *16*.
   * **-n (eller--undernätsnamn**). Namnet på det första undernätet. I vårt scenario, *FrontEnd*.
   * **-p (eller--undernät-start-ip)**. Första IP-adressen för undernätet eller undernätsadressutrymmet. I vårt scenario, *192.168.1.0*.
   * **-r (eller--undernät cidr)**. Nätverksmasken i CIDR-format för undernätet. I vårt scenario, *24*.
   * **-l (eller --location)**. Azure-region där VNet skapas. I vårt scenario, *centrala USA*.

3. Skapa ett undernät:

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    Förväntad utdata:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    Parametrar som används:

   * **-t (eller--vnet-name**. Namnet på VNet där undernätet kommer att skapas. I vårt scenario, *TestVNet*.
   * **-n (eller --name)**. Namnet på det nya undernätet. I vårt scenario, *BackEnd*.
   * **-a (eller --address-prefix)**. CIDR-block för undernätet. Fyra vårt scenario, *192.168.2.0/24*.
   
4. Visa egenskaperna för det nya VNet:

    ```azurecli
    azure network vnet show
    ```
   
    Förväntad utdata:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>Nästa steg

Lär dig hur du ansluter:

- En virtuell dator (VM) till ett virtuellt nätverk genom att läsa den [skapa ett Linux VM](../virtual-machines/linux/quick-create-cli.md) artikel. I stället för att skapa ett virtuellt nätverk och undernät i stegen i artikeln kan du ansluta en virtuell dator till ett befintligt virtuellt nätverk och undernät.
- Det virtuella nätverket till andra virtuella nätverk genom att läsa artikeln [Connect VNets](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) (Ansluta virtuella nätverk).
- Det virtuella nätverket till ett lokalt nätverk med hjälp av ett virtuellt privat nätverk (VPN) för plats till plats eller en ExpressRoute-krets. Lär dig hur genom att läsa den [ansluta ett virtuellt nätverk till ett lokalt nätverk med hjälp av en plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) och [länka ett VNet till en ExpressRoute-krets](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).