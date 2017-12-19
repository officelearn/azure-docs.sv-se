---
title: "Skapa ett virtuellt nätverk - Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar ett virtuellt nätverk med Azure CLI."
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 659a791124eab002290ac0b7f0898cf1c06c2951
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Skapa ett virtuellt nätverk med Azure CLI

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure har två distributionsmodeller: Azure Resource Manager och klassisk. Microsoft rekommenderar att skapa resurser med Resource Manager-distributionsmodellen. Mer information om skillnaderna mellan de två modellerna finns i artikeln [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Förstå Azure-distributionsmodellerna).

Du kan också skapa ett virtuellt nätverk via Resource Manager med andra verktyg eller skapa ett virtuellt nätverk med den klassiska distributionsmodellen genom att välja ett annat alternativ från listan nedan:

> [!div class="op_single_selector"]
> * [Portal](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [Mall](virtual-networks-create-vnet-arm-template-click.md)
> * [Portal (klassisk)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (klassisk)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [CLI (klassisk)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Om du vill skapa ett virtuellt nätverk med Azure CLI, gör du följande:

1. Installera och konfigurera senast [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in till en Azure med hjälp av [az inloggningen](/cli/azure/#login).

2. Skapa en resursgrupp för ditt virtuella nätverk med hjälp av den [az gruppen skapa](/cli/azure/group#create) kommandot med de `--name` och `--location` argument:

    ```azurecli
    az group create --name TestRG --location centralus
    ```

3. Skapa ett VNet och ett undernät:

    ```azurecli
    az network vnet create \
    --name TestVNet \
    --resource-group TestRG \
    --location centralus \
    --address-prefix 192.168.0.0/16 \
    --subnet-name FrontEnd \
    --subnet-prefix 192.168.1.0/24
    ```

    Förväntad utdata:
    
    ```json
    {
        "newVNet": {
            "addressSpace": {
            "addressPrefixes": [
            "192.168.0.0/16"
            ]
            },
            "dhcpOptions": {
            "dnsServers": []
            },
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>",
            "subnets": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                "name": "FrontEnd",
                "properties": {
                "addressPrefix": "192.168.1.0/24",
                "provisioningState": "Succeeded"
                },
                "resourceGroup": "TestRG"
            }
            ]
            }
    }
    ```

    Parametrar som används:

    - `--name TestVNet`: Namnet på VNet som ska skapas.
    - `--resource-group TestRG`: # Resursgruppens namn som styr resursen. 
    - `--location centralus`: Den plats där du vill distribuera.
    - `--address-prefix 192.168.0.0/16`: Adressprefixet och block.  
    - `--subnet-name FrontEnd`: Namnet på undernätet.
    - `--subnet-prefix 192.168.1.0/24`: Adressprefixet och block.

    Om du vill visa grundläggande information om att använda i nästa kommando, kan du fråga VNet med hjälp av en [frågefilter](/cli/azure/query-az-cli2):

    ```azurecli
    az network vnet list --query '[?name==`TestVNet`].{Where:location,Name:name,Group:resourceGroup}' -o table
    ```

    Vilket resulterar i följande utdata:

        Where      Name      Group

        centralus  TestVNet  TestRG

4. Skapa ett undernät:

    ```azurecli
    az network vnet subnet create \
    --address-prefix 192.168.2.0/24 \
    --name BackEnd \
    --resource-group TestRG \
    --vnet-name TestVNet
    ```

    Förväntad utdata:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid> \"",
    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "TestRG",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

    Parametrar som används:

    - `--address-prefix 192.168.2.0/24`: Undernät CIDR-block.
    - `--name BackEnd`: Namnet på det nya undernätet.
    - `--resource-group TestRG`: Resursgruppen.
    - `--vnet-name TestVNet`: Namnet på det ägande VNet.

5. Fråga om egenskaperna för det nya VNet:

    ```azurecli
    az network vnet show \
    -g TestRG \
    -n TestVNet \
    --query '{Name:name,Where:location,Group:resourceGroup,Status:provisioningState,SubnetCount:subnets | length(@)}' \
    -o table
    ```

    Förväntad utdata:

        Name      Where      Group    Status       SubnetCount

        TestVNet  centralus  TestRG   Succeeded              2

6. Fråga om egenskaperna undernät:

    ```azurecli
    az network vnet subnet list \
    -g TestRG \
    --vnet-name testvnet \
    --query '[].{Name:name,CIDR:addressPrefix,Status:provisioningState}' \
    -o table
    ```

    Förväntad utdata:

        Name      CIDR            Status

        FrontEnd  192.168.1.0/24  Succeeded
        BackEnd   192.168.2.0/24  Succeeded

## <a name="next-steps"></a>Nästa steg

Lär dig hur du ansluter:

- En virtuell dator (VM) till ett virtuellt nätverk genom att läsa den [skapa ett Linux VM](../virtual-machines/linux/quick-create-cli.md) artikel. I stället för att skapa ett virtuellt nätverk och undernät i stegen i artikeln kan du ansluta en virtuell dator till ett befintligt virtuellt nätverk och undernät.
- Det virtuella nätverket till andra virtuella nätverk genom att läsa artikeln [Connect VNets](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) (Ansluta virtuella nätverk).
- Det virtuella nätverket till ett lokalt nätverk med hjälp av ett virtuellt privat nätverk (VPN) för plats till plats eller en ExpressRoute-krets. Lär dig hur genom att läsa den [ansluta ett virtuellt nätverk till ett lokalt nätverk med hjälp av en plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) och [länka ett VNet till en ExpressRoute-krets](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).