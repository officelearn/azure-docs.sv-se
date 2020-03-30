---
title: Statisk IP-adress för behållargrupp
description: Skapa en behållargrupp i ett virtuellt nätverk och använd en Azure-programgateway för att exponera en statisk IP-adress för klientdelen för en behållarwebbapp
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481797"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Exponera en statisk IP-adress för en behållargrupp

Den här artikeln visar ett sätt att exponera en statisk, offentlig IP-adress för en [behållargrupp](container-instances-container-groups.md) med hjälp av en [Azure-programgateway](../application-gateway/overview.md). Följ dessa steg när du behöver en statisk startpunkt för en extern-vänd containerized app som körs i Azure Container Instanser. 

I den här artikeln använder du Azure CLI för att skapa resurser för det här scenariot:

* Ett virtuellt Azure-nätverk
* En behållargrupp som distribueras [i det virtuella nätverket (förhandsversion)](container-instances-vnet.md) som är värd för en liten webbapp
* En programgateway med en offentlig ip-adress för klientdel, en lyssnare som är värd för en webbplats på gatewayen och en väg till behållaren för backend-behållaren

Så länge programgatewayen körs och behållargruppen exponerar en stabil privat IP-adress i nätverkets delegerade undernät, är behållargruppen tillgänglig på den här offentliga IP-adressen.

> [!NOTE]
> Azure-avgifter för en programgateway baserat på den tid som gatewayen är etablerad och tillgänglig, samt mängden data som bearbetas. Se [priser](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Skapa det virtuella nätverket

I ett vanligt fall kanske du redan har ett virtuellt Azure-nätverk. Om du inte har någon, skapa en som visas med följande exempelkommandon. Det virtuella nätverket behöver separata undernät för programgatewayen och behållargruppen.

Om du behöver en, skapa en Azure-resursgrupp. Ett exempel:

```azureci
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk med kommandot [az network vnet create.][az-network-vnet-create] Med det här kommandot skapas undernätet *myAGSubnet* i nätverket.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Använd kommandot [az network vnet-nät skapa][az-network-vnet-subnet-create] för att skapa ett undernät för kontrolldelsbehållaregruppen. Här heter den *myACISubnet.*

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Använd kommandot [az network public-ip create][az-network-public-ip-create] för att skapa en statisk offentlig IP-resurs. I ett senare steg konfigureras den här adressen som klientdelen av programgatewayen.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Skapa containergrupp

Kör följande [az container skapa][az-container-create] för att skapa en behållargrupp i det virtuella nätverket som du konfigurerade i föregående steg. 

Gruppen distribueras i *undernätet myACISubnet* och innehåller en enda instans med `aci-helloworld` namnet *appcontainer* som hämtar avbildningen. Som visas i andra artiklar i dokumentationen paketerar den här bilden en liten webbapp skriven i Node.js som betjänar en statisk HTML-sida. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

När behållargruppen har distribuerats tilldelas en privat IP-adress i det virtuella nätverket. Kör till exempel följande kommando [för az-behållarvisning][az-container-show] för att hämta gruppens IP-adress:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Utdata liknar följande: `10.0.2.4`.

Om du vill använda ip-adressen i ett senare steg:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Skapa Application Gateway

Skapa en programgateway i det virtuella nätverket, i följande steg i [snabbstarten för programgateway](../application-gateway/quick-create-cli.md). Följande [az-nätverksprogram-gateway skapa][az-network-application-gateway-create] kommando skapar en gateway med en offentlig klientdel IP-adress och en väg till backend containergruppen. Mer information om gateway-inställningarna finns i dokumentationen för [Application Gateway.](/azure/application-gateway/)

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Det kan ta upp till 15 minuter för Azure att skapa programgatewayen. 

## <a name="test-public-ip-address"></a>Testa offentlig IP-adress
  
Nu kan du testa åtkomsten till webbappen som körs i behållargruppen bakom programgatewayen.

Kör kommandot [az network public-ip show][az-network-public-ip-show] för att hämta gatewayens offentliga IP-adress för frontend:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Utdata är en offentlig IP-adress, liknande: `52.142.18.133`.

Om du vill visa den webbapp som körs när den har konfigurerats navigerar du till gatewayens offentliga IP-adress i webbläsaren. Lyckad åtkomst liknar:

![Skärmbild från webbläsaren som visar ett program som körs i en instans av Azure-containern](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Nästa steg

* Se en [snabbstartsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) för att skapa en behållargrupp med en WordPress-behållarinstans som server för serverdel bakom en programgateway.
* Du kan också konfigurera en programgateway med ett certifikat för SSL-avslutning. Se [översikten](../application-gateway/ssl-overview.md) och [handledningen](../application-gateway/create-ssl-portal.md).
* Beroende på ditt scenario kan du överväga att använda andra Azure-belastningsutjämningslösningar med Azure Container Instances. Använd till exempel [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) för att distribuera trafik över flera behållarinstanser och mellan flera regioner. Se detta [blogginlägg](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show