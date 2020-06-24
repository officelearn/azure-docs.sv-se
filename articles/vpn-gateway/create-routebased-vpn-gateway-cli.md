---
title: 'Skapa en Route-baserad Azure-VPN Gateway: CLI'
description: Lär dig snabbt hur du skapar en VPN Gateway med CLI
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: 0cb03f827c8174932f235ec8ea327225da76ef4f
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987692"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Skapa en Route-baserad VPN-gateway med CLI

Den här artikeln hjälper dig att snabbt skapa en Route-baserad Azure VPN-gateway med hjälp av Azure CLI. En VPN-gateway används när du skapar en VPN-anslutning till ditt lokala nätverk. Du kan också använda en VPN-gateway för att ansluta virtuella nätverk.

Stegen i den här artikeln skapar ett VNet, ett undernät, ett Gateway-undernät och en Route-baserad VPN-gateway (virtuell nätverksgateway). En virtuell nätverksgateway kan ta 45 minuter eller mer att skapa. När gatewayen har skapats kan du skapa anslutningar. De här stegen kräver en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.4 eller senare för att du ska kunna följa anvisningarna i den här artikeln. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med hjälp av kommandot [az group create](/cli/azure/group). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med hjälp av kommandot [AZ Network VNet Create](/cli/azure/network/vnet) . I följande exempel skapas ett virtuellt nätverk med namnet **VNet1** på platsen för **öster** :

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Lägga till ett gatewayundernät

Gateway-undernätet innehåller de reserverade IP-adresser som används av tjänsten för virtuell nätverksgateway. Använd följande exempel för att lägga till ett Gateway-undernät:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Begär en offentlig IP-adress

En VPN-gateway måste ha en dynamiskt tilldelad offentlig IP-adress. Den offentliga IP-adressen allokeras till den VPN-gateway som du skapar för det virtuella nätverket. Använd följande exempel för att begära en offentlig IP-adress:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Skapa VPN gateway

Skapa en VPN-gateway med kommandot [az network vnet-gateway create](/cli/azure/group).

Om du kör det här kommandot med hjälp av `--no-wait` -parametern visas inga synpunkter eller utdata. `--no-wait`Parametern gör att gatewayen kan skapas i bakgrunden. Det innebär inte att VPN-gatewayen skapas direkt.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

Det kan ta minst 45 minuter att skapa en VPN-gateway.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Visa VPN-gatewayen

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Svaret ser ut ungefär så här:

```output
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
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
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

Använd följande exempel för att visa den offentliga IP-adress som tilldelats din Gateway:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Värdet som är kopplat till fältet **ipAddress** är den offentliga IP-adressen för din VPN-gateway.

Exempelsvar:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du har skapat använder du [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen. Därmed tas resursgruppen och alla resurser den innehåller bort.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Nästa steg

När gatewayen har skapats kan du skapa en anslutning mellan ditt virtuella nätverk och ett annat VNet. Eller skapa en anslutning mellan ditt virtuella nätverk och en lokal plats.

> [!div class="nextstepaction"]
> [Skapa en plats-till-plats-anslutning](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Skapa en punkt-till-plats-anslutning](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Skapa en anslutning till ett annat VNet](vpn-gateway-vnet-vnet-rm-ps.md)
