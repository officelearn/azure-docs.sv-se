---
title: 'Skapa en ruttbaserad Azure VPN-gateway: CLI | Microsoft Docs'
description: Snabbt lära dig hur du skapar en VPN-Gateway med hjälp av CLI
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2018
ms.author: cherylmc
ms.openlocfilehash: b25efb600fc89b5a6ead6ec27e212d09c9a14435
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Skapa en ruttbaserad VPN-gateway med hjälp av CLI

Den här artikeln hjälper dig att snabbt skapa en ruttbaserad Azure VPN-gateway med hjälp av Azure CLI. En VPN-gateway för att skapa en VPN-anslutning till lokalt nätverk. Du kan också använda en VPN-gateway för att ansluta Vnet.

Stegen i den här artikeln skapar ett VNet, ett undernät, ett gateway-undernät och en ruttbaserad VPN-gateway (virtuella nätverksgateway). En virtuell nätverksgateway kan ta minst 45 minuter att skapa. Du kan sedan skapa anslutningar när skapandet en gateway är klar. Dessa åtgärder kräver en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i den här artikeln kräver att du använder Azure CLI version 2.0.4 eller senare. Du hittar den installerade versionen genom att köra `az --version`. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs med det [az gruppen skapa](/cli/azure/group#az_group_create) kommando. En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med den [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create) kommando. I följande exempel skapas ett virtuellt nätverk med namnet **VNet1** i den **EastUS** plats:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Lägg till en gatewayundernät

Gateway-undernätet innehåller reserverade IP-adresser med gateway-tjänster för virtuella nätverk. Använd följande exempel för att lägga till ett gateway-undernät:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Begär en offentlig IP-adress

En VPN-gateway måste ha en dynamiskt tilldelad offentliga IP-adress. Den offentliga IP-adressen allokeras till VPN-gateway som du skapar för det virtuella nätverket. Använd följande exempel för att begära en offentlig IP-adress:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWPIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>Skapa en VPN-gateway

Skapa en VPN-gateway med kommandot [az network vnet-gateway create](/cli/azure/group#az_network_vnet_gateway_create).

Om du kör kommandot med hjälp av den `--no-wait` parameter du inte ser några feedback eller utdata. Den `--no-wait` parameter gör det möjligt för en gateway som ska skapas i bakgrunden. Det innebär inte att VPN-gateway har skapats direkt.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWPIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

En VPN-gateway kan ta minst 45 minuter att skapa.

## <a name="viewgw"></a>Visa VPN-gateway

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Svaret ser ut ungefär så här:

```
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWPIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Visa den offentliga IP-adressen

Använd följande exempel för att visa den offentliga IP-adressen till din gateway:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWPIP \
  --resource-group TestRG11
```

Värdet som associeras med den **ipAddress** fältet är offentliga IP-adressen för VPN-gateway.

Exempelsvar:

```
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWPIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```
## <a name="clean-up-resources"></a>Rensa resurser

Använd när du inte längre behöver de resurser som du skapade [ta bort grupp az](/cli/azure/group#az_group_delete) att ta bort resursgruppen. Detta tar bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Nästa steg

När gatewayen har skapat, kan du skapa en anslutning mellan det virtuella nätverket och ett annat VNet. Eller skapa en anslutning mellan det virtuella nätverket och en lokal plats.

> [!div class="nextstepaction"]
> [Skapa en plats-till-plats-anslutning](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Skapa en punkt-till-plats-anslutning](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Skapa en anslutning till ett annat virtuellt nätverk](vpn-gateway-vnet-vnet-rm-ps.md)