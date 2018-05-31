---
title: 'Snabbstart: Skapa en offentlig belastningsutjämnare – Azure CLI | Microsoft Docs'
description: Den här snabbstarten visar hur du skapar en offentlig belastningsutjämnare med Azure CLI
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 6c7a9bd83af5d23bdc9e6dd8c910dbf64a6efd6f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304927"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>Snabbstart: Skapa en offentlig belastningsutjämnare som belastningsutjämnar virtuella datorer med Azure CLI 2.0

Den här snabbstartsguiden visar hur du skapar en Azure Load Balancer. Om du vill testa belastningsutjämnaren så distribuera två virtuella datorer (VM) som kör Ubuntu-servern och belastningsutjämna en webbapp mellan dem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att installera och använda CLI lokalt så kräver den här självstudiekursen att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroupLB* på platsen *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för belastningsutjämnaren. Använd [az nätverket offentliga IP-skapa](https://docs.microsoft.com/cli/azure/network/public-ip#create) när du ska skapa en offentlig IP-adress med namnet *myPublicIP* i *myResourceGroupLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupLB --name myPublicIP
```

## <a name="create-azure-load-balancer"></a>Skapa en Azure Load Balancer

Det här avsnittet beskriver hur du gör för att skapa och konfigurera följande komponenter i belastningsutjämnaren:
  - en klientdels-IP-pool som tar emot inkommande nätverkstrafik i belastningsutjämnaren.
  - en serverdels-IP-pool om klientdelspoolen skickar den belastningsutjämnade nätverkstrafiken.
  - en hälsoavsökning som fastställer hälsan för serverdelens virtuella datorinstanser.
  - en belastningsutjämningsregel som definierar hur trafiken ska distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer"></a>Skapa belastningsutjämnaren

Skapa en offentlig Azure Load Balancer med [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) med namnet **myLoadBalancer** som innehåller en klientdelspool med namnet **myFrontEndPool**, och en serverdelspool med namnet **myBackEndPool** som är associerad med den offentliga IP-adressen **myPublicIP** som du skapade i föregående steg.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan skicka nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från belastningsutjämnaren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälsoavsökning med [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa belastningsutjämningsregeln

En belastningsutjämningsregel definierar klientdelens IP-konfiguration för inkommande trafik och serverdelens IP-pool för att ta emot trafiken, tillsammans med nödvändiga käll- och målportar. Skapa belastningsutjämningsregeln *myLoadBalancerRuleWeb* med [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) så att du kan lyssna på port 80 i klientdelspoolen *myFrontEndPool* och skicka belastningsutjämnad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du distribuerar några virtuella datorer och testar din belastningsutjämnare, måste du skapa virtuella nätverksresurser som stöd.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med namnet *myVnet* med undernätet *mySubnet* i *myResourceGroup* med [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Skapa en nätverkssäkerhetsgrupp så att du kan definiera inkommande anslutningar till det virtuella nätverket.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverkssäkerhetsgrupp som tillåter inkommande anslutningar via port 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```
### <a name="create-nics"></a>Skapa nätverkskort

Skapa tre nätverksgränssnitt med [az network nic create](/cli/azure/network/nic#az_network_nic_create) och koppla dem till den offentliga IP-adressen och nätverkssäkerhetsgruppen. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```


## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du tre virtuella datorer som ska användas som serverdelsservrar för belastningsutjämnaren. Du kan kontrollera att belastningsutjämnaren har skapats genom att även installera NGINX på de virtuella datorerna.

### <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Skapa en tillgänglighetsuppsättning med [az vm availabilityset create](/cli/azure/network/nic#az_network_availabilityset_create)

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupLB \
    --name myAvailabilitySet
```

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
 
Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az_vm_create).

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    --no-wait
    done
```
Det kan ta några minuter för de virtuella datorerna att distribueras.

## <a name="test-the-load-balancer"></a>Testa belastningsutjämnaren

Hämta belastningsutjämnarens offentliga IP-adress med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
![Testa belastningsutjämnaren](./media/load-balancer-get-started-internet-arm-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resursgruppen, belastningsutjämnaren och alla relaterade resurser med kommandot [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
  az group delete --name myResourceGroupLB
```


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skapat en grundläggande belastningsutjämnare, anslutit virtuella datorer till den, konfigurerat regeln för trafikbelastningsutjämning, konfigurerat hälsoavsökningen och sedan testat belastningsutjämnaren. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
