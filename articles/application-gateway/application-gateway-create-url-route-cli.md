---
title: Skapa en Programgateway med URL-sökvägsbaserad routning regler – Azure CLI | Microsoft Docs
description: Lär dig mer om att skapa URL-baserad routningsregler för en application gateway och den virtuella datorn skalningsuppsättning med Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: 77226795f5d1cb56dc09946eda8c51889cb3cd3b
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "42059206"
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>Skapa en Programgateway med URL-sökvägsbaserad routning regler med hjälp av Azure CLI

Du kan använda Azure CLI till att konfigurera [webbadressbaserade routningsregler](application-gateway-url-route-overview.md) när du skapar en [programgateway](application-gateway-introduction.md). I den här självstudien skapar du serverdelspooler med hjälp av en [virtual machine scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Du kan sedan skapa routningsregler som gör att Internet-trafik anländer till rätt servrar i poolerna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en Programgateway med URL-karta
> * Skapa VM-skalningsuppsättningar med serverdelspoolerna

![URL-routningsexempel](./media/application-gateway-create-url-route-cli/scenario.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med [az group create](/cli/azure/group#create).

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser 

Skapa ett virtuellt nätverk med namnet *myVNet* och ett undernät med namnet *myAGSubnet* med [az network vnet create](/cli/azure/network/vnet#az-net). Du kan sedan lägga till undernätet med namnet *myBackendSubnet* som servrarna i serverdelen behöver med [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network_vnet_subnet_create). Skapa den offentliga IP-adressen med namnet *myAGPublicIPAddress* med [az network public-ip create](/cli/azure/network/public-ip#az-network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>Skapa programgatewayen med webbadressmappning

Du kan använda [az network application-gateway create](/cli/azure/network/application-gateway#create) till att skapa en programgateway med namnet *myAppGateway*. När du skapar en programgateway med hjälp av Azure CLI anger du konfigurationsinformation som kapacitet, sku och HTTP-inställningar. Programgatewayen tilldelas till *myAGSubnet* och *myAGPublicIPAddress* som du skapade tidigare. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Det kan ta flera minuter att skapa programgatewayen. När programgatewayen har skapats ser du de här nya funktionerna:

- *appGatewayBackendPool* – en programgateway måste ha minst en serverdelsadresspool.
- *appGatewayBackendHttpSettings* – anger att port 80 och ett HTTP-protokoll används för kommunikation.
- *appGatewayHttpListener* – standardlyssnaren som är associerad med *appGatewayBackendPool*.
- *appGatewayFrontendIP* – tilldelar *myAGPublicIPAddress* till *appGatewayHttpListener*.
- *regel 1* – standardroutningsregel som är associerad med *appGatewayHttpListener*.


### <a name="add-image-and-video-backend-pools-and-port"></a>Lägga till serverdelspooler och portar för bilder och video

Du kan lägga till serverdelspooler med namnet *imagesBackendPool* och *videoBackendPool* till din application gateway med hjälp av [az network application-gateway adresspool skapa](/cli/azure/network/application-gateway#az-network_application_gateway_address-pool_create). Du lägger till klientdelsporten för poolerna med [az network application-gateway frontend-port create](/cli/azure/network/application-gateway#az-network_application_gateway_frontend_port_create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Lägga till serverdelslyssnare

Lägg till serverdelslyssnaren *backendListener* som ska dirigera trafiken med [az network application-gateway http-listener create](/cli/azure/network/application-gateway#az-network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Lägga till webbadressmappningar

Med adressmappningar ser du till att specifika webbadresser dirigeras till specifika serverdelspooler. Du kan skapa webbadressmappningarna *imagePathRule* och *videoPathRule* med [az network application-gateway url-path-map create](/cli/azure/network/application-gateway#az-network_application_gateway_url_path_map_create) och [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway#az-network_application_gateway_url_path_map_rule_create)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Lägga till routningsregeln

Routningsregeln associerar adressmappningarna med den lyssnare du skapade. Du kan lägga till regeln med namnet *2* med [az network application-gateway rule skapa](/cli/azure/network/application-gateway#az-network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Skapa VM-skalningsuppsättningar

I det här exemplet skapar du tre VM-skalningsuppsättningar för de tre serverdelspooler du har skapat. Skalningsuppsättningarna du skapar har namnen *myvmss1*, *myvmss2* och *myvmss3*. Varje skalningsuppsättning innehåller två virtuella datorinstanser där du installerar NGINX.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Installera NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan hämta den offentliga IP-adressen för programgatewayen med [az network public-ip show](/cli/azure/network/public-ip#az-network_public_ip_show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm* eller *http://40.121.222.19:8080/video/test.htm*.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testa basadressen i programgatewayen](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

Ändra URL: en till http://<ip-address>:8080/video/test.html i slutet av den grundläggande Webbadressen och du bör se ut ungefär som i följande exempel:

![Testa bildadressen i programgatewayen](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

Ändra URL: en till http://<ip-address>:8080/video/test.html och du bör se ut ungefär som i följande exempel.

![Testa videoadressen i programgatewayen](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en Programgateway med URL-karta
> * Skapa VM-skalningsuppsättningar med serverdelspoolerna

Om du vill veta mer om application gateway och deras associerade resurser kan du fortsätta i instruktionsartiklarna.
