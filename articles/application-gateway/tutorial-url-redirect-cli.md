---
title: "Skapa en Programgateway med URL-sökväg-baserade omdirigering - Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar en Programgateway med URL-sökväg-baserade omdirigerade trafik med hjälp av Azure CLI."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: 73fc20cf738f584008e7d8a019b8f7012dcacab1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Skapa en Programgateway med URL-sökväg-baserade omdirigering med hjälp av Azure CLI

Du kan använda Azure CLI för att konfigurera [URL-sökväg-baserade regler för routning](application-gateway-url-route-overview.md) när du skapar en [Programgateway](application-gateway-introduction.md). I den här självstudiekursen skapar du serverdelspooler med [skalningsuppsättningar i virtuella](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Sedan kan du skapa routningsregler för URL: en som gör att Internet-trafik dirigeras till rätt serverdelspoolen.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Lägg till lyssnare och regler för Routning
> * Skapa skalningsuppsättningar i virtuella datorer för serverdelspooler

I följande exempel visas trafiken kommer från port 8080 och 8081 och dirigeras till samma serverdelspooler:

![Exempel på URL: en Routning](./media/tutorial-url-redirect-cli/scenario.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Skapa en resurs med [az gruppen skapa](/cli/azure/group#create).

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* i den *eastus* plats.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser 

Skapa ett virtuellt nätverk med namnet *myVNet* och undernätet med namnet *myAGSubnet* med [az network vnet skapa](/cli/azure/network/vnet#az_net). Du kan sedan lägga till undernätet med namnet *myBackendSubnet* som krävs av backend-servrar med hjälp av [az undernät för virtuellt nätverk skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Skapa offentlig IP-adress med namnet *myAGPublicIPAddress* med [az nätverket offentliga IP-skapa](/cli/azure/public-ip#az_network_public_ip_create).

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

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Du kan använda [az nätverket Programgateway skapa](/cli/azure/application-gateway#create) att skapa Programgateway med namnet myAppGateway. När du skapar en Programgateway med hjälp av Azure CLI kan ange du konfigurationsinformation, till exempel kapacitet, sku och HTTP-inställningar. Programgatewayen har tilldelats *myAGSubnet* och *myPublicIPSddress* som du skapade tidigare.  

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

 Det kan ta flera minuter för Programgateway ska skapas. När programgatewayen har skapats kan se du de här nya funktionerna i den:

- *appGatewayBackendPool* -en Programgateway måste ha minst en backend-adresspool.
- *appGatewayBackendHttpSettings* -anger att port 80 och en HTTP-protokollet används för kommunikation.
- *appGatewayHttpListener* -standard lyssnaren som är associerade med *appGatewayBackendPool*.
- *appGatewayFrontendIP* -tilldelar *myAGPublicIPAddress* till *appGatewayHttpListener*.
- *regel 1* - standard routning regel som är associerad med *appGatewayHttpListener*.


### <a name="add-backend-pools-and-ports"></a>Lägg till serverdelspooler och portar

Du kan lägga till backend-adresspooler med namnet *imagesBackendPool* och *videoBackendPool* till din Programgateway med hjälp av [az nätverket Programgateway adresspool skapa](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create). Du lägger till klientdelsportar för pooler med [az Programgateway frontend-nätverksport skapa](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). 

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
  --name bport
az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Lägg till lyssnare och regler

### <a name="add-listeners"></a>Lägg till lyssnare

Lägg till backend-lyssnare med namnet *backendListener* och *redirectedListener* som behövs för att dirigera trafik med hjälp av [az http-lyssnare för Programgateway nätverk skapar](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Lägg till URL-sökvägen standardmappning

URL-sökväg maps se till att specifika URL-adresser dirigeras till specifika serverdelspooler. Du kan skapa URL-sökväg maps med namnet *imagePathRule* och *videoPathRule* med [az Programgateway url-sökväg-Nätverkskarta skapa](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) och [az nätverk gateway-url-sökväg-kartregeln skapa](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
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
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Lägga till omdirigering av konfigurationen

Du kan konfigurera omdirigering för lyssnare med [az nätverket Programgateway omdirigerings-config skapa](/cli/azure/application-gateway#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Lägga till mappningen omdirigering URL-sökväg

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Lägg till regler för Routning

Regler för vidarebefordran koppla kartor för URL-sökvägen till lyssnare som du skapade. Du kan lägga till regler med namnet *defaultRule* och *redirectedRule* med [az nätverket Programgateway regeln skapa](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Skapa virtuella datorer

I det här exemplet kan du skapa tre skalningsuppsättningar i virtuella datorer som har stöd för de tre serverdelspooler som du skapade. Skaluppsättningar som du skapar namnges *myvmss1*, *myvmss2*, och *myvmss3*. Varje skaluppsättning innehåller två instanser för virtuella datorer som du installerar NGINX.

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
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan använda för att hämta den offentliga IP-adressen för programgatewayen [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show). Kopiera den offentliga IP-adressen och klistra in den i adressfältet i webbläsaren. T.ex, *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm*, *http://40.121.222.19:8080/video/test.htm*, eller *http:// 40.121.222.19:8081/images/test.htm*.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testa bas-URL i Programgateway](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Ändra Webbadressen till http://&lt;ip-adress&gt;: 8080/video/test.html, ersätter din IP-adress för &lt;ip-adress&gt;, och du bör se något som liknar följande exempel:

![Testa URL: en för bilder i Programgateway](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Ändra Webbadressen till http://&lt;ip-adress&gt;: 8080/video/test.html, ersätter din IP-adress för &lt;ip-adress&gt;, och du bör se något som liknar följande exempel.

![Testa video-URL i Programgateway](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Nu ska du ändra Webbadressen till http://&lt;ip-adress&gt;: 8081/images/test.htm, ersätter din IP-adress för &lt;ip-adress&gt;, och du bör se trafik omdirigeras till serverdelspoolen bilder på http://&lt;ip-adress&gt;: 8080/bilder.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Lägg till lyssnare och regler för Routning
> * Skapa skalningsuppsättningar i virtuella datorer för serverdelspooler

> [!div class="nextstepaction"]
> [Mer information om vad du kan göra med Programgateway](application-gateway-introduction.md)