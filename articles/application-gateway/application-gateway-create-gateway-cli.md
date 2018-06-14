---
title: Skapa en Programgateway - Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en Programgateway med Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 791cc8bca95fc2264b485c23f30e24254067f513
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33201835"
---
# <a name="create-an-application-gateway-using-the-azure-cli"></a>Skapa en Programgateway med hjälp av Azure CLI

Du kan använda Azure CLI för att skapa eller hantera programgatewayer från kommandoraden eller i skript. Den här snabbstarten visar hur du skapar nätverksresurser backend-servrar och en Programgateway.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt denna Snabbstart kräver att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs med [az gruppen skapa](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* i den *eastus* plats.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser 

Skapa virtuella nätverk och undernät med [az network vnet skapa](/cli/azure/vnet#az_vnet_create). Skapa en offentlig IP-adress med hjälp av [az nätverket offentliga IP-skapa](/cli/azure/public-ip#az_public_ip_create).

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du två virtuella datorer som ska användas som backend-servrar för programgatewayen. Du kan också installera NGINX på de virtuella datorerna för att verifiera att programgatewayen har skapats.

### <a name="create-two-virtual-machines"></a>Skapa två virtuella datorer

Du kan använda en konfigurationsfil för cloud-init för att installera NGINX och köra en Hello World Node.js-app på en virtuell Linux-dator. Skapa en fil med namnet cloud-init.txt i ditt nuvarande gränssnitt och kopiera och klistra in följande konfiguration i gränssnittet. Se till att kopiera hela cloud-init-filen korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Skapa nätverksgränssnitt med [az nätverket nic skapa](/cli/azure/network/nic#az_network_nic_create). Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az_vm_create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Skapa en gateway som använder [az nätverket Programgateway skapa](/cli/azure/application-gateway#az_application_gateway_create). När du skapar en Programgateway med hjälp av Azure CLI kan ange du konfigurationsinformation, till exempel kapacitet, sku och HTTP-inställningar. Privata IP-adresser för nätverksgränssnitten läggs till som servrar i serverdelspoolen för programgatewayen.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Det kan ta flera minuter för Programgateway ska skapas. När programgatewayen har skapats kan se du dessa funktioner i den:

- *appGatewayBackendPool* -en Programgateway måste ha minst en backend-adresspool.
- *appGatewayBackendHttpSettings* -anger att port 80 och en HTTP-protokollet används för kommunikation.
- *appGatewayHttpListener* -standard lyssnaren som är associerade med *appGatewayBackendPool*.
- *appGatewayFrontendIP* -tilldelar *myAGPublicIPAddress* till *appGatewayHttpListener*.
- *regel 1* - standard routning regel som är associerad med *appGatewayHttpListener*.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Offentliga IP-adressen för programgatewayen får använda [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Testa Programgateway](./media/application-gateway-create-gateway-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [ta bort grupp az](/cli/azure/group#az_group_delete) kommando för att ta bort resursgruppen, Programgateway och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en resursgrupp, nätverksresurser och serverdelsservrar. Du kan sedan använda resurserna för att skapa en Programgateway. Mer information om programgatewayer och deras associerade resurser fortsätter du att artiklarna.

