---
title: Självstudie – Dirigera webbtrafik baserat på webbadressen – Azure CLI
description: Lär dig hur du dirigerar webbtrafik baserat på webbadressen till specifika och skalbara serverpooler med Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 4/27/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 61f884e186316b0b7829ef8e9f8f62d6c5df6b5d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307959"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Självstudie: Dirigera webbtrafik baserat på webbadressen med Azure CLI

Du kan använda Azure CLI till att konfigurera dirigering av webbtrafik till specifika, skalbara serverpooler baserat på webbadressen som användes för åtkomst till ditt program. I den här självstudien skapar du en gateway med [Azure Application Gateway](application-gateway-introduction.md) med tre serverdelspooler med [VM-skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Var och en av serverdelspoolerna har ett specifikt syfte, som vanliga data, bilder eller video.  När du dirigerar trafiken till olika pooler garanterar du att dina kunder får den information de behöver, när de behöver den.

Om du vill aktivera trafikdirigering skapar du [routningsregler](application-gateway-url-route-overview.md) som tilldelas till lyssnarna för specifika portar. Då ser du till att webbtrafiken kommer till rätt server i poolen.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa lyssnare, en mappning av adressökvägar och regler
> * Skapa skalbara serverdelspooler


![URL-routningsexempel](./media/tutorial-url-route-cli/scenario.png)

Om du vill kan du utföra den här självstudien med [Azure PowerShell](tutorial-url-route-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare där du distribuerar och hanterar Azure-resurser. Skapa en resursgrupp med [az group create](/cli/azure/group#create).

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser 

Skapa ett virtuellt nätverk med namnet *myVNet* och ett undernät med namnet *myAGSubnet* med [az network vnet create](/cli/azure/network/vnet#az_net). Lägg sedan till ett undernät med namnet *myBackendSubnet* som servrarna i serverdelen behöver med [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Skapa den offentliga IP-adressen med namnet *myAGPublicIPAddress* med [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create).

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

Använd [az network application-gateway create](/cli/azure/application-gateway#create) till att skapa en programgateway med namnet *myAppGateway*. När du skapar en programgateway med hjälp av Azure CLI anger du konfigurationsinformation som kapacitet, sku och HTTP-inställningar. Programgatewayen tilldelas till *myAGSubnet* och *myAGPublicIPAddress* som du skapade tidigare. 

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

Lägg till serverdelspoolerna *imagesBackendPool* och *videoBackendPool* för programgatewayen med [az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create). Du lägger till klientdelsporten för poolerna med [az network application-gateway frontend-port create](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). 

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

Lägg till serverdelslyssnaren *backendListener* som ska dirigera trafiken med [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Lägga till webbadressmappningar

Med adressmappningar ser du till att specifika webbadresser dirigeras till specifika serverdelspooler. Skapa webbadressmappningarna *imagePathRule* och *videoPathRule* med [az network application-gateway url-path-map create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) och [az network application-gateway url-path-map rule create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create)

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

Routningsregeln associerar adressmappningarna med den lyssnare du skapade. Lägg till en regel med namnet *rule2* med [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

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

I den här självstudien skapar du tre VM-skalningsuppsättningar för de tre serverdelspooler du har skapat. Skalningsuppsättningarna du skapar har namnen *myvmss1*, *myvmss2* och *myvmss3*. Varje skalningsuppsättning innehåller två virtuella datorinstanser där du installerar NGINX.

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

Hämta den offentliga IP-adressen för programgatewayen med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm* eller *http://40.121.222.19:8080/video/test.htm*.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testa basadressen i programgatewayen](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Ändra webbadressen till http://&lt;ip-adress&gt;:8080/images/test.htm och använd din IP-adress istället för &lt;ip-adress&gt;. Du bör se någonting som liknar följande exempel:

![Testa bildadressen i programgatewayen](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Ändra webbadressen till http://&lt;ip-adress&gt;:8080/video/test.htm och använd din IP-adress istället för &lt;ip-adress&gt;. Du bör se någonting som liknar följande exempel:

![Testa videoadressen i programgatewayen](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver dem längre tar du bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa lyssnare, en mappning av adressökvägar och regler
> * Skapa skalbara serverdelspooler

> [!div class="nextstepaction"]
> [Skapa en programgateway med webbadressbaserad omdirigering](./tutorial-url-redirect-cli.md)
