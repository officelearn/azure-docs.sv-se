---
title: Skapa en Programgateway med intern omdirigering – Azure CLI | Microsoft Docs
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
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: f1bf61aaf7fefea5bca216b3ba880c23910b9391
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "42054352"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Skapa en Programgateway med intern omdirigering med Azure CLI

Du kan använda Azure CLI för att konfigurera [web traffic omdirigering](multiple-site-overview.md) när du skapar en [Programgateway](overview.md). I den här självstudien definierar du en serverdelspool med hjälp av en skalningsuppsättning för virtuella datorer. Du sedan konfigurera lyssnare och regler baserat på domäner som du äger för att se till att Internet-trafik anländer till i poolen. Den här självstudien förutsätter att du äger flera domäner och använder exempel på *www.contoso.com* och *www.contoso.org*.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Lägg till lyssnare och omdirigering av regel
> * Skapa en VM-skalningsuppsättning med backend-pool
> * Skapa en CNAME-post i domänen

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

Skapa ett virtuellt nätverk med namnet *myVNet* och ett undernät med namnet *myAGSubnet* med [az network vnet create](/cli/azure/network/vnet#az-net). Du kan sedan lägga till undernätet med namnet *myBackendSubnet* som krävs av servrar med hjälp av serverdelspool [az network vnet-undernät skapa](/cli/azure/network/vnet/subnet#az-network_vnet_subnet_create). Skapa den offentliga IP-adressen med namnet *myAGPublicIPAddress* med [az network public-ip create](/cli/azure/network/public-ip#az-network_public_ip_create).

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


## <a name="add-listeners-and-rules"></a>Lägga till lyssnare och regler 

Du behöver en lyssnare så att programgatewayen kan dirigera trafiken till serverdelspoolen på rätt sätt. I den här självstudien skapar du två lyssnare för de två domänerna. I det här exemplet lyssnare har skapats för domänerna med *www.contoso.com* och *www.contoso.org*.

Lägg till lyssnarna för serverdelen som ska dirigera trafiken med [az network application-gateway http-listener create](/cli/azure/network/application-gateway#az-network_application_gateway_http_listener_create).

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

### <a name="add-the-redirection-configuration"></a>Lägga till konfigurationen för omdirigering

Lägg till omdirigeringskonfiguration som skickar trafik från *www.consoto.org* till lyssnaren för *www.contoso.com* i application gateway med [az network application-gateway omdirigerings-config skapa](/cli/azure/network/application-gateway/redirect-config#az-network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Lägga till routningsregler

Regler bearbetas i den ordning som de skapas och trafik dirigeras med hjälp av den första regeln som matchar URL som skickas till application gateway. Om du till exempel har en regel med en grundläggande lyssnare och en regel med en lyssnare för flera webbplatser för samma port så måste regeln med lyssnare för flera platser stå innan regeln med den grundläggande lyssnaren om regeln för flera platser ska fungera som förväntat. 

I det här exemplet skapar två nya regler och ta bort Standardregeln som skapades.  Du kan lägga till regeln med [az network application-gateway rule create](/cli/azure/network/application-gateway#az-network_application_gateway_rule_create).

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

## <a name="create-virtual-machine-scale-sets"></a>Skapa VM-skalningsuppsättningar

I det här exemplet skapar du en skalningsuppsättning för virtuella datorer som har stöd för serverdelspoolen som du skapade. Skalningsuppsättningen som du skapar heter *myvmss* och innehåller två instanser av virtuella datorer som du installerar NGINX.

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

Kör följande kommando i fönstret shell:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>Skapa en CNAME-post i domänen

När du har skapat programgatewayen med dess offentliga IP-adress kan du hämta DNS-adressen och använda den till att skapa en CNAME-post i domänen. Hämta den DNS-adressen till programgatewayen med [az network public-ip show](/cli/azure/network/public-ip#az-network_public_ip_show). Kopiera värdet *fqdn* för DNSSettings och använd det som värde för CNAME-posten du skapar. Du bör inte använda A-poster eftersom den virtuella IP-adressen kan ändras när programgatewayen startas om.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Ange domännamnet i adressfältet i webbläsaren. Till exempel http://www.contoso.com.

![Testa contoso-webbplatsen i programgatewayen](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Ändra adressen till din domän, till exempel http://www.contoso.org så bör du se att trafiken har omdirigerats till lyssnaren för www.contoso.com.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> * Konfigurera nätverket
> * Skapa en programgateway
> * Lägg till lyssnare och omdirigering av regel
> * Skapa en VM-skalningsuppsättning med backend-pool
> * Skapa en CNAME-post i domänen