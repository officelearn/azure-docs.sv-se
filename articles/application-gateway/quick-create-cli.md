---
title: 'Snabb start: direkt webb trafik med CLI'
titleSuffix: Azure Application Gateway
description: Lär dig hur du använder Azure CLI för att skapa en Azure Application-Gateway som dirigerar webb trafik till virtuella datorer i en backend-pool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: de1f30d949509ab48999d0080ccc4df74321ce01
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075207"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI

Den här snabb starten visar hur du använder Azure CLI för att skapa en Programgateway.  När du har skapat programgatewayen testar du den för att kontrol lera att den fungerar som den ska. Med Azure Application Gateway dirigerar du din program webb trafik till specifika resurser genom att tilldela lyssnare till portar, skapa regler och lägga till resurser i en backend-pool. Den här artikeln använder en enkel installation med en offentlig IP-adress, en grundläggande lyssnare som är värd för en enda plats på programgatewayen, två virtuella datorer som används för backend-poolen och en grundläggande regel för routning av förfrågningar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

### <a name="azure-cli"></a>Azure CLI

Om du väljer att installera och använda CLI lokalt kör du Azure CLI version 2.0.4 eller senare. Du kan ta reda på versionen genom att köra **az --version**. Information om att installera eller uppgradera finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Skapa en resursgrupp med hjälp av [az group create](/cli/azure/group#az-group-create). 

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Nödvändiga nätverks resurser 

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk.  Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.  Du kan antingen skapa ett nytt undernät för Application Gateway eller använda ett befintligt. I det här exemplet skapar du två undernät i det här exemplet: ett för programgatewayen och en annan för backend-servrarna. Du kan konfigurera klient delens IP för Application Gateway att vara offentlig eller privat enligt ditt användnings fall. I det här exemplet väljer du en offentlig IP-adress för klient delen.

Du skapar det virtuella nätverket och undernätet med hjälp av [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Kör [az network public-ip create](/cli/azure/network/public-ip) för att skapa den offentliga IP-adressen.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

### <a name="backend-servers"></a>Backend-servrar

En server del kan ha nätverkskort, skalnings uppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domän namn (FQDN) och backend-ändar för flera klienter som Azure App Service. I det här exemplet skapar du två virtuella datorer som ska användas som backend-servrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att testa programgatewayen.

#### <a name="create-two-virtual-machines"></a>Skapa två virtuella datorer

Installera [NGINX-webbservern](https://docs.nginx.com/nginx/) på de virtuella datorerna för att verifiera att programgatewayen har skapats. Du kan använda en konfigurationsfil för cloud-init för att installera NGINX och köra en ”Hello World”-Node.js-app på en virtuell Linux-dator. Mer information om cloud-init finns i avsnittet om [stöd för cloud-init för virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

I Azure Cloud Shell kopierar du och klistrar in följande konfiguration i en fil som heter *cloud-init.txt*. Ange *editor cloud-init.txt* för att skapa filen.

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

Skapa nätverksgränssnitten med [az network nic create](/cli/azure/network/nic#az-network-nic-create). Du skapar de virtuella datorerna med hjälp av [az vm create](/cli/azure/vm#az-vm-create).

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

Skapa en programgateway med hjälp av [az network application-gateway create](/cli/azure/network/application-gateway). När du skapar en programgateway med Azure CLI anger du konfigurationsinformation såsom kapacitet, SKU och HTTP-inställningar. Azure lägger sedan till de privata IP-adresserna för nätverksgränssnitten som servrar i serverdelspoolen för programgatewayen.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Det kan ta upp till 30 minuter för Azure att skapa programgatewayen. När den har skapats kan du visa följande inställningar i avsnittet **Inställningar** på sidan **Programgateway**:

- **appGatewayBackendPool**: finns på sidan **Server dels pooler** . Den anger den serverpool som krävs.
- **appGatewayBackendHttpSettings**: finns på sidan **http-inställningar** . Den anger att programgatewayen använder port 80 och HTTP-protokollet för kommunikation.
- **appGatewayHttpListener**: finns på **sidan lyssnare**. Den anger den standardlyssnare som är associerad med **appGatewayBackendPool**.
- **appGatewayFrontendIP**: finns på sidan **IP-konfigurationer för klient** del. Den tilldelar *myAGPublicIPAddress* till **appGatewayHttpListener**.
- **regel 1**: finns på sidan **regler** . Den anger den standardroutningsregel som är associerad med **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Azure kräver inte en NGINX-webbserver för skapande av programgatewayen, men du installerade det i den här snabbstarten för att kontrollera om Azure lyckades skapa programgatewayen. Hämta den offentliga IP-adressen för den nya programgatewayen med hjälp av [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Kopiera och klistra in den offentliga IP-adressen i webbläsarens adressfält.
    
![Testa programgatewayen](./media/quick-create-cli/application-gateway-nginxtest.png)

När du uppdaterar webbläsaren bör du se namnet på den andra virtuella datorn. Detta anger att Application Gateway har skapats och kan ansluta till Server delen.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för ta bort resursgruppen. När du tar bort resursgruppen tas även programgatewayen och alla dess relaterade resurser bort.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway med hjälp av Azure CLI](./tutorial-manage-web-traffic-cli.md)

