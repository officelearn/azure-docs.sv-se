---
title: Skapa en Programgateway med externa trafiken omdirigering - Azure CLI | Microsoft Docs
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
ms.openlocfilehash: 3ded6fc0950c82d0aa36da89fdd5635afef7be0b
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204946"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Skapa en Programgateway med externa genom att använda Azure CLI

Du kan använda Azure CLI för att konfigurera [web trafik omdirigering](application-gateway-multi-site-overview.md) när du skapar en [Programgateway](application-gateway-introduction.md). I kursen får konfigurera du en lyssnare och en regel som omdirigerar Internet-trafik som når Programgateway till en extern plats.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en lyssnare och omdirigering av regel
> * Skapa en programgateway

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

Skapa ett virtuellt nätverk med namnet *myVNet* och undernätet med namnet *myAGSubnet* med [az network vnet skapa](/cli/azure/network/vnet#az_net). Skapa offentlig IP-adress med namnet *myAGPublicIPAddress* med [az nätverket offentliga IP-skapa](/cli/azure/public-ip#az_network_public_ip_create). Dessa resurser används för att ge nätverksanslutning till gateway för programmet och dess associerade resurser.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Du kan använda [az nätverket Programgateway skapa](/cli/azure/application-gateway#create) att skapa Programgateway med namnet *myAppGateway*. När du skapar en Programgateway med hjälp av Azure CLI kan ange du konfigurationsinformation, till exempel kapacitet, sku och HTTP-inställningar. Programgatewayen har tilldelats *myAGSubnet* och *myPublicIPSddress* som du skapade tidigare. 

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
  --frontend-port 8080 \
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

### <a name="add-the-redirection-configuration"></a>Lägg till omdirigering av konfigurationen

Lägg till konfigurationen för omdirigering som skickar trafik från programgatewayen till *bing.com* med [az nätverket Programgateway omdirigerings-config skapa](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "http://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Lägga till en lyssnare och routningsregel

En lyssnare krävs för att aktivera Programgateway att dirigera trafik på lämpligt sätt. Skapa lyssnare med [az http-lyssnare för Programgateway nätverk skapar](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create) med klientdelsport som skapats med [az Programgateway frontend-nätverksport skapa](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). Det krävs en regel för lyssnaren ska kunna skicka inkommande trafik. Skapa en enkel regel med namnet *redirectRule* med [az nätverket Programgateway regeln skapa](/cli/azure/application-gateway#az_network_application_gateway_rule_create) med omdirigering av konfigurationen.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan använda för att hämta den offentliga IP-adressen för programgatewayen [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

Du bör se *bing.com* visas i webbläsaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> * Konfigurera nätverket
> * Skapa en lyssnare och omdirigering av regel
> * Skapa en programgateway

> [!div class="nextstepaction"]
> [Mer information om vad du kan göra med Programgateway](./application-gateway-introduction.md)