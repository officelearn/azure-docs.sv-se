---
title: Självstudie – Dirigera webbtrafik baserat på webbadressen – Azure CLI
description: I den här självstudien får du lära dig hur du dirigerar webbtrafik baserat på webbadressen till specifika och skalbara serverpooler med Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 10/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 4f0c93c41a468b62baf1ec50d030f235d36a8dd2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006470"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Självstudier: Dirigera webbtrafik baserat på URL: en med hjälp av Azure CLI

Som en IT-administratör som hanterar webbtrafik vill du hjälpa dina kunder eller användare att få den information de behöver så snabbt som möjligt. Ett sätt som du kan optimera deras erfarenheter är genom att dirigera olika typer av Internet-trafik till olika serverresurser. Den här självstudien visar hur du använder Azure CLI för att installera och konfigurera Application Gateway-routning för olika typer av trafik från ditt program. Routningen dirigerar sedan trafiken till olika serverpooler baserat på webbadressen.

![URL-routningsexempel](./media/tutorial-url-route-cli/scenario.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp för nätverksresurserna som du behöver
> * Skapa nätverksresurserna
> * Skapa en programgateway för trafik som kommer från ditt program
> * Ange serverpooler och hanteringsregler för olika typer av trafik
> * Skapa en skalningsuppsättning för varje pool så att poolen kan skalas automatiskt
> * Kör ett test så att du kan kontrollera att de olika typerna av trafik går till rätt pool

Om du vill kan du slutföra den här självstudien med [Azure PowerShell](tutorial-url-route-powershell.md) eller [Azure-portalen](create-url-route-portal.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där du distribuerar och hanterar Azure-resurser. Skapa en resursgrupp med `az group create`.

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Skapa ett virtuellt nätverk med namnet *myVNet* och ett undernät med namnet *myAGSubnet* med `az network vnet create`. Lägg sedan till ett undernät med namnet *myBackendSubnet* som servrarna i serverdelen behöver med `az network vnet subnet create`. Skapa den offentliga IP-adressen med namnet *myAGPublicIPAddress* med `az network public-ip create`.

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

## <a name="create-the-app-gateway-with-a-url-map"></a>Skapa App Gateway med en webbadressmappning

Använd `az network application-gateway create` för att skapa en programgateway med namnet *myAppGateway*. När du skapar en programgateway med hjälp av Azure CLI anger du konfigurationsinformation som kapacitet, sku och HTTP-inställningar. Programgatewayen tilldelas till *myAGSubnet* och *myAGPublicIPAddress* som du skapade tidigare.

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


|Funktion  |Beskrivning  |
|---------|---------|
|appGatewayBackendPool     |En programgateway måste ha minst en serverdelsadresspool.|
|appGatewayBackendHttpSettings     |Anger att port 80 och ett HTTP-protokoll används för kommunikation.|
|appGatewayHttpListener     |Standardlyssnaren som är associerad med appGatewayBackendPool|
|appGatewayFrontendIP     |Tilldelar myAGPublicIPAddress till appGatewayHttpListener.|
|rule1     |Standardhanteringsregel som är associerad med appGatewayHttpListener.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Lägga till serverdelspooler och en port för bilder och video

Lägg till serverdelspoolerna *imagesBackendPool* och *videoBackendPool* för programgatewayen med `az network application-gateway address-pool create`. Du lägger till klientdelsporten för poolerna med hjälp av `az network application-gateway frontend-port create`.

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

### <a name="add-a-backend-listener"></a>Lägga till en serverdelslyssnare

Lägg till serverdelslyssnaren *backendListener* som ska dirigera trafiken med `az network application-gateway http-listener create`.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>Lägga till en webbadressmappning

Med adressmappningar ser du till att specifika webbadresser dirigeras till specifika serverdelspooler. Skapa URL-sökvägsmappningar med namnet *imagePathRule* och *videoPathRule* med `az network application-gateway url-path-map create` och `az network application-gateway url-path-map rule create`.

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

### <a name="add-a-routing-rule"></a>Lägga till en hanteringsregel

Routningsregeln associerar adressmappningarna med den lyssnare du skapade. Lägg till en regel med namnet *rule2* med `az network application-gateway rule create`.

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

## <a name="create-vm-scale-sets"></a>Skapa skalningsuppsättningar för virtuella datorer

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
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Hämta den offentliga IP-adressen för programgatewayen med az network public-ip show. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. T.ex, `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm`, eller `http://40.121.222.19:8080/video/test.htm`.

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

> [!div class="nextstepaction"]
> [Skapa en programgateway med webbadressbaserad omdirigering](./tutorial-url-redirect-cli.md)
