---
title: Skapa en programgateway som är värd för flera webbplatser – Azure CLI
description: Lär dig hur du skapar en programgateway som är värd för flera webbplatser i Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f9b09982e5552a85ce5800059b114f30b5f4bfad
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178587"
---
# <a name="tutorial-create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>Självstudier: Skapa en programgateway som är värd för flera webbplatser med hjälp av Azure CLI

Du kan använda Azure CLI till att [konfigurera ett värdskap för flera webbplatser](multiple-site-overview.md) när du skapar en [programgateway](overview.md). I den här självstudien definierar du adresspooler för serverdelen med hjälp av VM-skalningsuppsättningar. Du konfigurerar sedan lyssnare och regler baserat på de domäner du äger för att kontrollera att webbtrafiken anländer till rätt servrar i poolerna. I den här självstudien förutsätts att du äger flera domäner. Vi använder *www.contoso.com* och *www.fabrikam.com* som exempel.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Skapa lyssnare för serverdelen
> * Skapa routningsregler
> * Skapa VM-skalningsuppsättningar med serverdelspoolerna
> * Skapa en CNAME-post i domänen

![Exempel på routning mellan flera webbplatser](./media/tutorial-multiple-sites-cli/scenario.png)


Om du vill kan du utföra den här självstudien med [Azure PowerShell](tutorial-multiple-sites-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med [az group create](/cli/azure/group#create).

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser 

Skapa det virtuella nätverket och ett undernät med namnet *myAGSubnet* med [az network vnet create](/cli/azure/network/vnet). Du kan sedan lägga till undernätet som servrarna i serverdelen behöver med [az network vnet subnet create](/cli/azure/network/vnet/subnet). Skapa den offentliga IP-adressen med namnet *myAGPublicIPAddress* med [az network public-ip create](/cli/azure/network/public-ip).

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

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Du kan använda [az network application-gateway create](/cli/azure/network/application-gateway#create) till att skapa programgatewayen. När du skapar en programgateway med hjälp av Azure CLI anger du konfigurationsinformation som kapacitet, sku och HTTP-inställningar. Programgatewayen tilldelas till *myAGSubnet* och *myAGPublicIPAddress* som du skapade tidigare. 

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

### <a name="add-the-backend-pools"></a>Lägga till serverdelspoolerna

Lägg till de serverdelspooler som ska innehålla servrarna med [az network application-gateway address-pool create](/cli/azure/network/application-gatewaywork_application_gateway_address_pool_create).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-backend-listeners"></a>Lägga till lyssnare för serverdelen

Lägg till lyssnarna för serverdelen som ska dirigera trafiken med [az network application-gateway http-listener create](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Lägga till routningsregler

Regler bearbetas i ordningen de anges, och trafiken dirigeras med hjälp av den första regel som matchar oavsett särskilda egenskaper. Om du till exempel har en regel med en grundläggande lyssnare och en regel med en lyssnare för flera webbplatser för samma port så måste regeln med lyssnare för flera platser stå innan regeln med den grundläggande lyssnaren om regeln för flera platser ska fungera som förväntat. 

I det här exemplet skapar du två nya regler och tar bort standardregeln som skapades när du skapade programgatewayen. Du kan lägga till regeln med [az network application-gateway rule create](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Skapa VM-skalningsuppsättningar

I det här exemplet skapar du tre VM-skalningsuppsättningar för de tre serverdelspoolerna i programgatewayen. Skalningsuppsättningarna du skapar har namnen *myvmss1*, *myvmss2* och *myvmss3*. Varje skalningsuppsättning innehåller två virtuella datorinstanser där du installerar IIS.

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>Skapa en CNAME-post i domänen

När du har skapat programgatewayen med dess offentliga IP-adress kan du hämta DNS-adressen och använda den till att skapa en CNAME-post i domänen. Hämta den DNS-adressen till programgatewayen med [az network public-ip show](/cli/azure/network/public-ipwork_public_ip_show). Kopiera värdet *fqdn* för DNSSettings och använd det som värde för CNAME-posten du skapar. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Du bör inte använda A-poster eftersom den virtuella IP-adressen kan ändras när programgatewayen startas om.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Ange domännamnet i adressfältet i webbläsaren. Till exempel http://www.contoso.com.

![Testa contoso-webbplatsen i programgatewayen](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

Om du ändrar adressen till din andra domän bör du se något som liknar följande exempel:

![Testa fabrikam-webbplatsen i programgatewayen](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver dem längre tar du bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Skapa lyssnare för serverdelen
> * Skapa routningsregler
> * Skapa VM-skalningsuppsättningar med serverdelspoolerna
> * Skapa en CNAME-post i domänen

> [!div class="nextstepaction"]
> [Skapa en programgateway med webbadressbaserade routningsregler](./tutorial-url-route-cli.md)
