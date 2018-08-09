---
title: 'Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI | Microsoft Docs'
description: Lär dig hur använder Azure CLI för att skapa en Azure Application Gateway som leder webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 282f6d965ea85b25f1eada1a63897734c6c7b298
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435272"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI

Med Azure Application Gateway kan du dirigera programwebbtrafiken till specifika resurser genom att tilldela lyssnare till portar, skapa regler och att lägga till resurser i en serverdelspool.

Den här snabbstarten visar hur du använder Azure CLI för att snabbt skapa programgatewayen med två virtuella datorer i dess serverdelspool. Sedan testar du den och kontrollerar att den fungerar korrekt.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du måste alltid skapa resurser i en resursgrupp. Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create). 

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser 

Du måste skapa ett virtuellt nätverk för att programgatewayen ska kunna kommunicera med andra resurser. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen. Två undernät skapas i det här exemplet: ett för programgatewayen och ett annat för virtuella datorer. 

Skapa det virtuella nätverket och undernätet med [az network vnet create](/cli/azure/vnet#az-vnet-create). Skapa den offentliga IP-adressen med [az network public-ip create](/cli/azure/public-ip#az-public-ip-create).

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

I det här exemplet skapar du två virtuella datorer som ska användas som serverdelsservrar för programgatewayen. 

### <a name="create-two-virtual-machines"></a>Skapa två virtuella datorer

Du kan även installera NGINX på de virtuella datorerna för att verifiera att programgatewayen har skapats. Du kan använda en konfigurationsfil för cloud-init för att installera NGINX och köra en Hello World Node.js-app på en virtuell Linux-dator. 

Skapa en fil med namnet cloud-init.txt i ditt nuvarande gränssnitt och kopiera och klistra in följande konfiguration i gränssnittet. Se till att kopiera hela cloud-init-filen korrekt, särskilt den första raden:

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

Skapa nätverksgränssnitten med [az network nic create](/cli/azure/network/nic#az-network-nic-create). Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az-vm-create).

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

Skapa en programgateway med [az network application-gateway create](/cli/azure/application-gateway#az-application-gateway-create). När du skapar en programgateway med hjälp av Azure CLI anger du konfigurationsinformation, till exempel kapacitet, sku och HTTP-inställningar. De privata IP-adresserna för nätverksgränssnitten läggs till som servrar i serverdelspoolen för programgatewayen.

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

Det kan ta upp till 30 minuter för programgateway att skapas. När programgatewayen har skapats kan se du dessa funktioner i den:

- *appGatewayBackendPool* – en programgateway måste ha minst en serverdelsadresspool.
- *appGatewayBackendHttpSettings* – anger att port 80 och ett HTTP-protokoll används för kommunikation.
- *appGatewayHttpListener* – standardlyssnaren som är associerad med *appGatewayBackendPool*.
- *appGatewayFrontendIP* – tilldelar *myAGPublicIPAddress* till *appGatewayHttpListener*.
- *regel 1* – standardroutningsregel som är associerad med *appGatewayHttpListener*.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du behöver inte installera NGINX för att skapa programgatewayen men du har installerat det i den här snabbstarten för att verifiera om programgatewayen har skapats. Hämta den offentliga IP-adressen för programgatewayen med [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Testa programgatewayen](./media/quick-create-cli/application-gateway-nginxtest.png)

Du bör se namnet på den andra virtuella datorn när du uppdaterar webbläsaren.

## <a name="clean-up-resources"></a>Rensa resurser

Utforska först de resurser som har skapats med programgatewayen och sedan när den inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)

