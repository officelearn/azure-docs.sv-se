---
title: Skapa en Programgateway med ett certifikat - Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en Programgateway och lägga till ett certifikat för SSL-avslutning med hjälp av Azure CLI.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: davidmu
ms.openlocfilehash: 2a662d212c4feddf39f29d309d3d082b6507c356
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Skapa en Programgateway med HTTP till HTTPS-omdirigering med hjälp av Azure CLI

Du kan använda Azure CLI för att skapa en [Programgateway](overview.md) med ett certifikat för SSL-avslutning. En regel för vidarebefordran används för att dirigera HTTP-trafik till HTTPS-porten i Programgateway. I det här exemplet du också skapa en [virtuella datorns skaluppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för serverdelspoolen för Programgateway som innehåller två virtuella instanser.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Skapa ett nätverk
> * Skapa en Programgateway med certifikatet
> * Lägga till en lyssnare och omdirigering av regel
> * Skapa en virtuell dator-skala med serverdelspoolen standard

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

För produktion, ska du importera ett giltigt certifikat som signerats av en betrodd provider. För den här självstudiekursen skapar du ett självsignerat certifikat och pfx-filen med kommandot openssl.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Ange värden som passar ditt certifikat. Du kan acceptera standardvärdena.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Ange lösenordet för certifikatet. I det här exemplet *Azure123456!* används.

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

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Du kan använda [az nätverket Programgateway skapa](/cli/azure/network/application-gateway#az_network_application_gateway_create) att skapa Programgateway med namnet *myAppGateway*. När du skapar en Programgateway med hjälp av Azure CLI kan ange du konfigurationsinformation, till exempel kapacitet, sku och HTTP-inställningar. 

Programgatewayen har tilldelats *myAGSubnet* och *myAGPublicIPAddress* som du skapade tidigare. I det här exemplet associera du det certifikat som du skapade och dess lösenord när du skapar programgatewayen. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Det kan ta flera minuter för Programgateway ska skapas. När programgatewayen har skapats kan se du de här nya funktionerna i den:

- *appGatewayBackendPool* -en Programgateway måste ha minst en backend-adresspool.
- *appGatewayBackendHttpSettings* -anger att port 80 och en HTTP-protokollet används för kommunikation.
- *appGatewayHttpListener* -standard lyssnaren som är associerade med *appGatewayBackendPool*.
- *appGatewayFrontendIP* -tilldelar *myAGPublicIPAddress* till *appGatewayHttpListener*.
- *regel 1* - standard routning regel som är associerad med *appGatewayHttpListener*.

## <a name="add-a-listener-and-redirection-rule"></a>Lägga till en lyssnare och omdirigering av regel

### <a name="add-the-http-port"></a>Lägga till HTTP-porten

Du kan använda [az Programgateway frontend-nätverksport skapa](/cli/azure/network/application-gateway/frontend-port#az_network_application_gateway_frontend_port_create) att lägga till HTTP-porten i programgatewayen.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>Lägga till HTTP-lyssnare

Du kan använda [az http-lyssnare för Programgateway nätverk skapar](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create) att lägga till lyssnaren med namnet *myListener* till programgatewayen.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Lägg till omdirigering av konfigurationen

Lägga till HTTP till HTTPS omdirigering av konfigurationen till programmet gateway med [az nätverket Programgateway omdirigerings-config skapa](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Lägg till regel för vidarebefordran

Lägga till routning regeln med namnet *regel 2* med omdirigering av konfigurationen till programmet gateway med [az nätverket Programgateway regeln skapa](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en virtuell dator skaluppsättningen namngivna *myvmss* som ger servrar för serverdelspoolen i programgatewayen. De virtuella datorerna i skaluppsättning som är associerade med *myBackendSubnet* och *appGatewayBackendPool*. Att skapa skala, kan du använda [az vmss skapa](/cli/azure/vmss#az_vmss_create).

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

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan använda för att hämta den offentliga IP-adressen för programgatewayen [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Säker varning](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Om du vill acceptera säkerhetsmeddelande om du använder ett självsignerat certifikat, Välj **information** och sedan **går du vidare till webbsidan**. Din skyddade NGINX-webbplats visas sedan som i exemplet nedan:

![Testa bas-URL i Programgateway](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Skapa ett nätverk
> * Skapa en Programgateway med certifikatet
> * Lägga till en lyssnare och omdirigering av regel
> * Skapa en virtuell dator-skala med serverdelspoolen standard


