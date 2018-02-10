---
title: "Skapa en Programgateway med flera plats-värd - Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar en Programgateway som är värd för flera platser med hjälp av Azure CLI."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: df475cb6eed2b75275e573721f754e7de87698f5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Skapa en Programgateway med flera plats värd med hjälp av Azure CLI

Du kan använda Azure CLI för att konfigurera [att vara värd för flera webbplatser](application-gateway-multi-site-overview.md) när du skapar en [Programgateway](application-gateway-introduction.md). I den här självstudiekursen skapar du serverdelspooler med skalningsuppsättningar i virtuella datorer. Du konfigurerar sedan lyssnare och regler baserat på domäner som du äger för att kontrollera Internet-trafik anländer till rätt servrar i poolerna. Den här kursen förutsätter att du äger flera domäner och använder exempel på *www.contoso.com* och *www.fabrikam.com*.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Skapa lyssnare och regler för Routning
> * Skapa virtuella datorer med backend-pooler
> * Skapa en CNAME-post i din domän

![Exempel på flera platser routning](./media/tutorial-multisite-cli/scenario.png)

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

### <a name="add-the-backend-pools"></a>Lägg till backend-pooler

Lägg till backend-pooler med namnet *contosoPool* och *fabrikamPool* som behövs för att innehålla backend-servrar med hjälp av [az nätverket Programgateway adresspool skapa](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create).

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

### <a name="add-listeners"></a>Lägg till lyssnare

En lyssnare krävs för att aktivera Programgateway att dirigera trafik korrekt till serverdelspoolen. I de här självstudierna skapar du två lyssnare för de två domänerna. I det här exemplet skapas lyssnare för domänerna i *www.contoso.com* och *www.fabrikam.com*. 

Lägg till lyssnare med namnet *contosoListener* och *fabrikamListener* som behövs för att dirigera trafik med hjälp av [az http-lyssnare för Programgateway nätverk skapar](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

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

### <a name="add-routing-rules"></a>Lägg till regler för Routning

Regler bearbetas i den ordning som de skapades och trafik dirigeras med hjälp av den första regeln som matchar URL som skickas till programgatewayen. Till exempel om du har en regel med hjälp av en grundläggande lyssnare och en regel med en flera platser lyssnare båda på samma port måste regeln med flera platser lyssnaren anges innan en regel med grundläggande lyssnare för flera platser regeln för att fungera som förväntat. 

I det här exemplet skapar två nya regler och ta bort standardregel som skapades när du skapade programgatewayen. Du kan lägga till en regel med hjälp av [az nätverket Programgateway regeln skapa](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

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

## <a name="create-virtual-machine-scale-sets"></a>Skapa virtuella datorer

I det här exemplet kan du skapa tre skalningsuppsättningar i virtuella datorer som stöder tre serverdelspooler i programgatewayen. Skaluppsättningar som du skapar namnges *myvmss1*, *myvmss2*, och *myvmss3*. Varje skaluppsättning innehåller två instanser för virtuella datorer som du installerar NGINX.

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
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Skapa en CNAME-post i din domän

När programgatewayen har skapats med dess offentliga IP-adress kan du hämta DNS-adress och använda den för att skapa en CNAME-post i din domän. Du kan använda [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show) att hämta DNS-serveradress för programgatewayen. Kopiera den *fqdn* värdet för DNSSettings och använda det som värde för CNAME-posten som du skapar. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Användning av A-poster rekommenderas inte eftersom VIP kan ändras när programgatewayen har startats om.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Ange domännamnet i adressfältet i webbläsaren. Till exempel http://www.contoso.com.

![Testa plats för contoso i Programgateway](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Ändra adressen till den andra domänen och du bör se något som liknar följande exempel:

![Testa fabrikam plats i Programgateway](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Skapa lyssnare och regler för Routning
> * Skapa virtuella datorer med backend-pooler
> * Skapa en CNAME-post i din domän

> [!div class="nextstepaction"]
> [Mer information om vad du kan göra med Programgateway](application-gateway-introduction.md)