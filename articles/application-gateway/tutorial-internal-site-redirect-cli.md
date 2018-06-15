---
title: Skapa en Programgateway med interna omdirigering - Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en Programgateway som omdirigerar interna webbtrafik till i poolen med hjälp av Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: victorh
ms.openlocfilehash: 42b45d07c8ea326f0daa8f0e6efd7cf567dbfd1b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355822"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Skapa en Programgateway med interna genom att använda Azure CLI

Du kan använda Azure CLI för att konfigurera [web trafik omdirigering](application-gateway-multi-site-overview.md) när du skapar en [Programgateway](application-gateway-introduction.md). I den här självstudiekursen skapar du en serverdelspool med hjälp av en skaluppsättning för virtuella datorer. Du konfigurerar sedan lyssnare och regler baserat på domäner som du äger för att kontrollera Internet-trafik anländer till i poolen. Den här kursen förutsätter att du äger flera domäner och använder exempel på *www.contoso.com* och *www.contoso.org*.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Lägg till lyssnare och omdirigering av regel
> * Skapa en virtuell dator-skala med serverdelspoolen
> * Skapa en CNAME-post i din domän

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

Skapa ett virtuellt nätverk med namnet *myVNet* och undernätet med namnet *myAGSubnet* med [az network vnet skapa](/cli/azure/network/vnet#az_net). Du kan sedan lägga till undernätet med namnet *myBackendSubnet* som krävs av backend-pool med servrar med hjälp av [az undernät för virtuellt nätverk skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Skapa offentlig IP-adress med namnet *myAGPublicIPAddress* med [az nätverket offentliga IP-skapa](/cli/azure/public-ip#az_network_public_ip_create).

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

Du kan använda [az nätverket Programgateway skapa](/cli/azure/application-gateway#create) att skapa Programgateway med namnet *myAppGateway*. När du skapar en Programgateway med hjälp av Azure CLI kan ange du konfigurationsinformation, till exempel kapacitet, sku och HTTP-inställningar. Programgatewayen har tilldelats *myAGSubnet* och *myAGPublicIPAddress* som du skapade tidigare. 

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


## <a name="add-listeners-and-rules"></a>Lägg till lyssnare och regler 

En lyssnare krävs för att aktivera Programgateway att dirigera trafik korrekt till serverdelspoolen. I de här självstudierna skapar du två lyssnare för de två domänerna. I det här exemplet skapas lyssnare för domänerna i *www.contoso.com* och *www.contoso.org*.

Lägg till backend-lyssnare som behövs för att dirigera trafik med hjälp av [az http-lyssnare för Programgateway nätverk skapar](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Lägg till omdirigering av konfigurationen

Lägg till konfigurationen för omdirigering som skickar trafik från *www.consoto.org* till lyssnaren för *www.contoso.com* i programmet gateway med [az nätverk program-gateway omdirigerings-config skapa](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoComListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Lägg till regler för Routning

Regler bearbetas i den ordning som de skapades och trafik dirigeras med hjälp av den första regeln som matchar URL som skickas till programgatewayen. Grundläggande Standardregeln som skapades behövs inte i den här självstudiekursen. I det här exemplet skapar du två nya regler som heter *contosoComRule* och *contosoOrgRule* och ta bort standardregel som har skapats.  Du kan lägga till regler med hjälp av [az nätverket Programgateway regeln skapa](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Skapa virtuella datorer

I det här exemplet skapar du en skaluppsättning för virtuell dator som stöder standard serverdelspoolen som har skapats. Den skaluppsättning som du skapar heter *myvmss* och innehåller två instanser för virtuella datorer som du installerar NGINX.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Installera NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>Skapa CNAME-post i din domän

När programgatewayen har skapats med dess offentliga IP-adress kan du hämta DNS-adress och använda den för att skapa en CNAME-post i din domän. Du kan använda [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show) att hämta DNS-serveradress för programgatewayen. Kopiera den *fqdn* värdet för DNSSettings och använda det som värde för CNAME-posten som du skapar. Användning av A-poster rekommenderas inte eftersom VIP kan ändras när programgatewayen har startats om.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Ange domännamnet i adressfältet i webbläsaren. T.ex, http://www.contoso.com.

![Testa plats för contoso i Programgateway](./media/tutorial-internal-site-redirect-cli/application-gateway-nginxtest.png)

Ändra adressen till din domän, till exempel http://www.contoso.org ska du se att trafiken dirigeras till lyssnaren för www.contoso.com.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Lägg till lyssnare och omdirigering av regel
> * Skapa en virtuell dator-skala med serverdelspoolen
> * Skapa en CNAME-post i din domän

> [!div class="nextstepaction"]
> [Mer information om vad du kan göra med Programgateway](./application-gateway-introduction.md)