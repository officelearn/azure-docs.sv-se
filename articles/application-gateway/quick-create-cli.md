---
title: 'Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI | Microsoft Docs'
description: Lär dig hur använder Azure CLI för att skapa en Azure Application Gateway som leder webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: eb0f73d31abced8decbed31e5604a2056584eb98
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549433"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI

Den här snabbstarten visar hur du använder Azure-portalen för att skapa en Programgateway.  Efter att application gateway kan testa du den och kontrollera att de fungerar korrekt. Med Azure Application Gateway dirigera application Internet-trafik till specifika resurser genom att tilldela lyssnare till portar, skapa regler och lägga till resurser till en serverdelspool. För enkelhetens skull använder den här artikeln en enkel installation med en offentlig frontend IP-adress, en grundläggande lyssnare till en plats på den här application gateway-värd, två virtuella datorer som används för serverdelspoolen och en regel för vidarebefordran av grundläggande begäran.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Om du väljer att installera och använda CLI lokalt kör du Azure CLI version 2.0.4 eller senare. Du kan ta reda på versionen genom att köra **az --version**. Information om att installera eller uppgradera finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Skapa en resursgrupp med hjälp av [az group create](/cli/azure/group#az-group-create). 

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Resurser som krävs 

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk.  Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.  Du kan skapa ett nytt undernät för Application Gateway, eller så kan du använda en befintlig. I det här exemplet skapar du två undernät i det här exemplet: en för application gateway och en för backend-servrarna. Du kan konfigurera Frontend IP för Programgatewayen ska vara offentlig eller privat enligt ditt användningsområde. I det här exemplet väljer vi en offentlig Klientdels-IP.

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
  --name myAGPublicIPAddress
```

### <a name="backend-servers"></a>Backend-servrar

Serverdel kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade namn (FQDN) och flera innehavare serverprogram som Azure App Service. I det här exemplet skapar du två virtuella datorer för Azure som ska användas som serverdelsservrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att verifiera att Azure har skapat programgatewayen.

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
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Det kan ta upp till 30 minuter för Azure att skapa programgatewayen. När den har skapats kan du visa följande inställningar i avsnittet **Inställningar** på sidan **Programgateway**:

- **appGatewayBackendPool**: Finns på sidan **Serverdelspooler**. Den anger den serverpool som krävs.
- **appGatewayBackendHttpSettings**: Finns på sidan **HTTP-inställningar**. Den anger att programgatewayen använder port 80 och HTTP-protokollet för kommunikation.
- **appGatewayHttpListener**: Finns på sidan **Lyssnare**. Den anger den standardlyssnare som är associerad med **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Finns på sidan **IP-konfigurationer för klientdel**. Den tilldelar *myAGPublicIPAddress* till **appGatewayHttpListener**.
- **rule1**: Finns på sidan **Regler**. Den anger den standardroutningsregel som är associerad med **appGatewayHttpListener**.

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

När du uppdaterar webbläsaren bör du se namnet på den andra virtuella datorn. Ett giltigt svar verifierar att application gateway har skapats och är det kunna ansluta med serverdelen.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för ta bort resursgruppen. När du tar bort resursgruppen tas även programgatewayen och alla dess relaterade resurser bort.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)

